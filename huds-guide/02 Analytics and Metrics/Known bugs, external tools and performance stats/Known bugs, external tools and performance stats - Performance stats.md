---
tags: [huds-guide, analytics]
---

# Known bugs, external tools and performance stats - Performance stats

Up: [[Known bugs, external tools and performance stats]] · Previous: [[Known bugs, external tools and performance stats - CCU patterns]]

- Network stats (Shift+F3 or F9): "recv" is data received per second between server and client.
- Keep received under about 100 KB/s and sent lower. 700 KB/s received with 300 KB/s sent has caused server crashes.
- High network load usually comes from running animations or effects on the server. Keep only important work there.
- Microprofiler: Ctrl+F6. Target average frame time is 16.66 ms at 60 Hz and 8.33 ms at 120 Hz.
- Frame drops usually come from game optimization, not PC specs.
- Client FPS of 50 on PC and 30–40 on mobile is probably normal.
- If memory keeps trending upward, it is a leak. Use an instance-counter plugin while idle in Studio.

---
Related concepts: [[Test Bursts and Spikes]]
