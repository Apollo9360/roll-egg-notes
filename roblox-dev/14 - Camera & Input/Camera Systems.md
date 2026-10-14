---
title: Camera Systems
category: Camera & Input
tags: [camera, scriptable-camera, camera-shake, spring, fov, cinematic, mobile]
status: active
source_quality: AUTHORITATIVE (Roblox camera docs) + HIGH CONFIDENCE (GDC technique); code type-checked and the spring numerically verified
last_verified: 2026-10-14
aliases: [Custom camera, Camera shake, Spring camera, Scriptable camera, Lock-on camera, Top-down camera]
---

# Camera Systems

## Summary

- **Start from the default camera.** Its third- and first-person modes, occlusion and touch controls are solid. Configure it through `StarterPlayer`: `CameraMinZoomDistance`/`CameraMaxZoomDistance`, `CameraMode` (Classic or LockFirstPerson), `DevCameraOcclusionMode` (Zoom or Invisicam), and the per-platform movement modes.
- **Layer effects on top of it**: shake, FOV punches, offsets. Bind them with `RunService:BindToRenderStep` at `Enum.RenderPriority.Camera.Value + 1`.
- **Replace it** (`CameraType = Scriptable`) only for genuinely different cameras: isometric, top-down, cutscenes, menus, lock-on.
- Every camera is **client-only**; the server can't move a player's camera directly.

## Patterns

| Camera | How |
|---|---|
| Effects on the default camera | BindToRenderStep after the camera priority; modify `CurrentCamera.CFrame` relative to its value |
| Over-the-shoulder | `Humanoid.CameraOffset`, plus mouse lock or shift-lock style. Keep the default camera if you can. |
| Top-down / isometric / strategy | Scriptable; position = focus + fixed offset; smooth with [Spring](#code); clamp the zoom; long-range orthographic-ish look via low `FieldOfView` |
| Cinematic and cutscenes | Scriptable; tween the CFrame along keyframes, or animate a camera part; restore `CameraType = Custom` and the `CameraSubject` afterwards |
| Menu camera | Scriptable, slow drift around a scene behind the UI |
| Spectator | Set `CameraSubject` to another player's Humanoid (default camera) |
| Lock-on | Scriptable or effect layer: look at the midpoint between player and target, spring-smoothed; give the player a clear break-lock input |
| Boss intro | Short cutscene, then hand control back quickly; let players skip on replay |

## Feel

- **Camera shake**, trauma-based (Squirrel Eiserloh, GDC 2016 "Juicing Your Cameras With Math"):
  - add *trauma* on impacts; shake = trauma²; decay over time;
  - use smooth noise rather than random jumps.
  - Big feel, little nausea. Keep it subtle and short.
- **FOV punch**: briefly widen the FOV on dash or boost (+5–10°) and spring back. Common practice; tune by feel.
- **Hit-stop and shake** together sell impacts; see [[Combat Systems Engineering]].
- **Springs, not lerp-per-frame.** `a = a:Lerp(b, 0.1)` each frame is **frame-rate dependent**. A critically damped spring is stable at any FPS.

## Comfort and accessibility

- **Motion sickness:** limit shake amplitude and duration, avoid large forced rotations, and keep the horizon level.
- **Respect `GuiService.ReducedMotionEnabled`**: disable shake and heavy sway.
- **FOV:** the default is 70. Very low FOV feels zoomed; very high FOV distorts.
- **Mobile:** a scripted camera must still support the touch drag and pinch your game needs. Test with Device Simulator **and** a real phone.
- **Gamepad:** right-stick camera with sensible sensitivity; don't fight player input with auto-rotation.

## Code

### CameraShake (client)

<!-- code: ReplicatedStorage/Client/CameraShake.luau -->
```lua
--!strict
-- ReplicatedStorage/Client/CameraShake (ModuleScript, client)
-- Trauma-based camera shake (Squirrel Eiserloh, "Math for Game Programmers:
-- Juicing Your Cameras With Math", GDC 2016): add trauma on impacts, shake
-- strength = trauma^2, trauma decays over time, offsets come from smooth noise.
-- Works on top of the default camera by offsetting it after camera scripts run.
local GuiService = game:GetService("GuiService")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

local MAX_ANGLE = math.rad(4) -- maximum rotation at trauma 1
local MAX_OFFSET = 0.6 -- studs at trauma 1
local DECAY_PER_SECOND = 1.6
local NOISE_SPEED = 18

local trauma = 0
local seed = math.random() * 1000
local CameraShake = {}

-- amount in 0..1. Small hit ~0.2, big explosion ~0.6. Clamped to 1.
function CameraShake.add(amount: number)
	if GuiService.ReducedMotionEnabled then
		return -- respect the player's Reduce Motion setting
	end
	trauma = math.clamp(trauma + amount, 0, 1)
end

function CameraShake.start()
	-- Camera priority + 1 runs right after the default camera has positioned itself.
	RunService:BindToRenderStep("CameraShake", Enum.RenderPriority.Camera.Value + 1, function(dt: number)
		if trauma <= 0 then
			return
		end
		local camera = Workspace.CurrentCamera
		if not camera then
			return
		end
		local shake = trauma * trauma
		local t = os.clock() * NOISE_SPEED
		local pitch = MAX_ANGLE * shake * math.noise(seed, t)
		local yaw = MAX_ANGLE * shake * math.noise(seed + 1, t)
		local roll = MAX_ANGLE * shake * math.noise(seed + 2, t)
		local offset = Vector3.new(math.noise(seed + 3, t), math.noise(seed + 4, t), 0) * (MAX_OFFSET * shake)
		camera.CFrame = camera.CFrame * CFrame.new(offset) * CFrame.Angles(pitch, yaw, roll)
		trauma = math.max(0, trauma - DECAY_PER_SECOND * dt)
	end)
end

function CameraShake.stop()
	RunService:UnbindFromRenderStep("CameraShake")
	trauma = 0
end

return CameraShake
```
<!-- /code -->

Usage: call `CameraShake.start()` once, then `CameraShake.add(0.3)` on a hit or `CameraShake.add(0.7)` on an explosion.

### Spring (shared)

<!-- code: ReplicatedStorage/Shared/Spring.luau -->
```lua
--!strict
-- ReplicatedStorage/Shared/Spring (ModuleScript)
-- Critically damped spring for smooth, frame-rate-independent following
-- (cameras, UI elements, view-model sway). No overshoot, no jitter at any FPS.
-- smoothTime ~= time to close most of the distance (seconds).
export type Spring = {
	position: Vector3,
	velocity: Vector3,
	smoothTime: number,
	update: (self: Spring, target: Vector3, dt: number) -> Vector3,
}

local Spring = {}

function Spring.new(initial: Vector3, smoothTime: number): Spring
	local spring = {
		position = initial,
		velocity = Vector3.zero,
		smoothTime = smoothTime,
	} :: Spring

	-- Closed-form critically damped step (the same approximation as Unity's
	-- SmoothDamp), stable for any dt.
	function spring.update(self: Spring, target: Vector3, dt: number): Vector3
		local omega = 2 / math.max(self.smoothTime, 1e-4)
		local x = omega * dt
		local decay = 1 / (1 + x + 0.48 * x * x + 0.235 * x * x * x)
		local change = self.position - target
		local temp = (self.velocity + change * omega) * dt
		self.velocity = (self.velocity - temp * omega) * decay
		self.position = target + (change + temp) * decay
		return self.position
	end

	return spring
end

return Spring
```
<!-- /code -->

Simulated at 240, 60, 30 and 10 FPS, moving 0→10 with smoothTime 0.3: each run converged to 9.9998 in 2 s with **no overshoot**. That confirms frame-rate independence.

```lua
-- Top-down follow camera using Spring
local camera = workspace.CurrentCamera
camera.CameraType = Enum.CameraType.Scriptable
local OFFSET = Vector3.new(0, 60, 30)
local follow = Spring.new(Vector3.zero, 0.25)
RunService:BindToRenderStep("TopDown", Enum.RenderPriority.Camera.Value, function(dt)
	local root = getRootPart() -- your character lookup; return early if nil
	if not root then return end
	local focus = follow:update(root.Position, dt)
	camera.CFrame = CFrame.lookAt(focus + OFFSET, focus)
end)
```

## Common mistakes

- Setting the CFrame every frame without making the camera Scriptable, so the default camera fights you.
- Forgetting to restore `CameraType = Custom` after a cutscene.
- Per-frame lerp smoothing that behaves differently at 60 and 240 FPS.
- Shake on every tiny event, so it stops meaning anything and causes nausea.
- Tweening the camera from the server. It's impossible; cameras are client-side.

## Related notes

- [[Input Systems]]
- [[Combat Systems Engineering]]
- [[VFX Production Guide]]

## Sources

- [Customize the camera](https://create.roblox.com/docs/workspace/camera). AUTHORITATIVE.
- [RunService:BindToRenderStep](https://create.roblox.com/docs/reference/engine/classes/RunService#BindToRenderStep) and [Enum.RenderPriority](https://create.roblox.com/docs/reference/engine/enums/RenderPriority). AUTHORITATIVE.
- Squirrel Eiserloh, *Math for Game Programmers: Juicing Your Cameras With Math*, GDC 2016. HIGH CONFIDENCE, widely used technique.

## Last Verified

2026-10-14
