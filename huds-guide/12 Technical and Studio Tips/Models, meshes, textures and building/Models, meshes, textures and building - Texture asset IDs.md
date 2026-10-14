---
tags: [huds-guide, technical]
---

# Models, meshes, textures and building - Texture asset IDs

Up: [[Models, meshes, textures and building]] · Previous: [[Models, meshes, textures and building - Welding and anchoring]] · Next: [[Models, meshes, textures and building - Other building tricks]]

| Texture | Asset ID |
|---|---|
| Welds | rbxassetid://7317691230 |
| Studs | rbxassetid://15910695828 or rbxassetid://16510058099 |
| Inverted studs | rbxassetid://133272741630146 |
| Chessboard | rbxassetid://2194711968 |
| Default checkerboard | rbxassetid://6372755229 (16x16 studs most accurate) |
| Checkerboard without square outlines | rbxassetid://4806196507 |
| Gradient VFX beam | 4839671501 |

- Put the texture in ColorMap and choose the right face; each part has 6 faces ([DevForum thread](https://devforum.roblox.com/t/how-would-you-make-a-baseplate-use-a-checkered-pattern/481017)).
- Copy the stud texture from a new Baseplate. The [Resurface Converter](https://devforum.roblox.com/t/resurface-converter-apply-classic-studs-and-more-to-parts-meshes-and-unions/1226536) plugin applies classic studs and custom materials to parts, meshes and unions.
- Stud-style buttons: UIStroke for the black border plus a stud decal.
