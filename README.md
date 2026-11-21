<img width="1376" height="768" alt="Gemini_Generated_Image_kagz04kagz04kagz" src="https://github.com/user-attachments/assets/1d6041d4-9c0a-4700-a25a-5b78b76cd539" />


# 🔦 ALAN WAKE REMASTERED VR 🔦

**Full 6DoF VR for Alan Wake Remastered (PC, DirectX 12).** Head tracking, real stereo 3D drawn by the game, first
person, a big cinema screen for cutscenes and an in-headset settings menu. One file, any OpenXR headset (SteamVR,
Virtual Desktop / VDXR, Meta, Pimax, WMR).

---

## 🟢 WHAT YOU GET

| | |
|---|---|
| 🥽 **Stereo 3D** | *True stereo* (the game renders both eyes every frame), *Synchronized* (both eyes from one game moment) or *Alternate eye* |
| 🔦 **Head aim** | Aim the gun and the flashlight with your head - always, or only while L2 / LT / right mouse is held. The camera never turns with it. Third and first person |
| 🎯 **Matched view** | Each eye is drawn with a view the game fully knows, so the flashlight glow, light shafts, volumetric lights, shadows and AO are computed for that eye |
| 🧍 **First person** | Press **End** — you see through Alan's eyes |
| 🎬 **Cutscene theater** | Cutscenes on a giant screen, size slider up to 170°, fade in / out |
| 🧭 **Comfort** | Level horizon, camera-shake filter, head-bob filter — your own head is never filtered |
| 🖥️ **HUD** | Pulled into the middle of your view, on its own panel, or hidden (**F7**) |
| ⌨️🎮 **Hotkeys tab** | Put any function on any key or controller button / combo — saved in the config |
| 🖱️ **Mouse in the panel** | Point, click and scroll in the VR panel with a glowing flashlight-amber cursor that lights up over anything clickable |
| ⚡ **Performance** | Renders at your headset's size, headset-rate limiter, foveated rendering (VRS) |
| 🐢 **Cheat Engine speedhack** | Works at any speed — the game speeds up or slows down, your head tracking stays real-time |

---

## 🟡 REQUIREMENTS

1. **Alan Wake Remastered** for PC (`Game_f_x64_EOS.exe`) — Epic 
2. A **PC VR headset** with an OpenXR runtime running.
3. In the game's options: **HDR off**, **DLSS off**.
4. A **gamepad** is recommended.

---

## 🔵 INSTALL

1. Copy **`mod\dxgi.dll`** and **`mod\awrvr_config.txt`** next to `Game_f_x64_EOS.exe`.
   The included config is set up for VR: **head aim always on**, the VR panel at its **lowest opacity**.
   (Already have an `awrvr_config.txt` with your own settings? Keep yours, or replace it to get these.)
2. Start your VR runtime, then the game.

> ⚠️⚠️ **TURN ON YOUR HEADSET AND START ITS VR SOFTWARE (Pimax Play, SteamVR, Oculus, Virtual Desktop…) BEFORE YOU LAUNCH THE GAME.** ⚠️⚠️
>
> ⚠️ **On the very first start the game waits a moment (normally under a second, at most 14 seconds) while AWRVR asks the headset for its resolution.** The game then renders at exactly that resolution from this first start on, always windowed. The mod sets it in the game's `resolution.xml` before the game reads it, and keeps a backup.
>
> ⚠️ **If the headset is not on, that first start uses the game's own size.** Start the game once more with the headset on.
>
> ⚠️ **Changed headsets?** Start the game once with the new headset: AWRVR notices it and uses its resolution from the next start (or delete `AWRVR\headset.txt` to use it at once).

> 🐧 Linux / Proton: launch options `WINEDLLOVERRIDES="dxgi=n,b" %command%`

**Uninstall:** delete `dxgi.dll`, `awrvr_config.txt` and the `AWRVR` folder.

---

## 📕 READ THIS BEFORE PLAYING OR REPORTING

These rules hold for every mod on this VR framework. Setups that break them are not supported, and reports from them are answered with this list.

- ⚠️ **Motion smoothing must be OFF.** Oculus ASW, SteamVR Motion Smoothing, Virtual Desktop SSW and Pimax Smart Smoothing halve the frame rate and invent every other frame. Motion warps, and the world seems to change scale when you turn.
  - **Oculus:** Ctrl + Numpad 1 while the game runs, or Oculus Debug Tool → ASW Off.
  - **SteamVR:** per-app Motion Smoothing off.
  - **Virtual Desktop:** SSW off.
  - **Pimax Play:** Smart Smoothing off.
- ⚠️ **Use AWRVR alone, installed once.** Don't run other mods for this game at the same time: anti-aliasing / TAA fixes, other VR mods, ASI loaders with other plugins, ReShade, camera / FOV tools. AWRVR already has these settings in its own panel.
- ⚠️ **No FOV cropping.** The Oculus Debug Tool FOV-Tangent Multiplier and similar runtime settings change the field of view the mod is given. Set them back to full.
- ⚠️ **Picture problems:** test without upscaling (DLSS / resolution scale) and at render scale 1.0 first.
- ⚠️ **Reports:** send `AWRVR\awrvr.log` and `AWRVR\xrhost.log` from the same run (they are rewritten at every start; the run before is kept as `*.prev.log`). Include the mod version, your headset and VR software, and what you did when it went wrong. Easiest: **VR panel → View → Make a report folder**, and **Mark an issue in the log** at the moment you see it.
- ⚠️ **Setup check:** search the log for **SETUP**. The problems are framed in ===== lines, and the VR panel's status line shows *SETUP: n problems*. Fix every SETUP problem before reporting.

---

## 🎮 CONTROLS

| Action | 🎮 Gamepad | ⌨️ Keyboard |
|---|---|---|
| VR menu open / close | hold **L3 + R3** | **Insert** |
| Menu: select / change / page / back | D-pad / A / LB RB / B | arrows / Enter / PgUp PgDn / Backspace |
| 🖱️ Menu with the mouse | | move · click = change / run · right click = back / clear · wheel = scroll (on a value: adjust) · ✕ = close |
| Recentre | *(map it)* | **F9** |
| First person on / off | *(map it)* | **End** |
| Next stereo method | *(map it)* | **F8** |
| Hide HUD | *(map it)* | **F7** |
| Flat screen now | *(map it)* | **F6** |
| Screenshot | *(map it)* | **Delete** |

Changes apply at once and are saved when the menu closes.

---

## ⌨️🎮 HOTKEYS TAB

Every important function can be put on **any keyboard key** (with Ctrl / Shift / Alt), the **middle / side mouse
buttons** and / or **any controller button or combo** (e.g. `LB+Y`):

| Device | What can be bound |
|---|---|
| ⌨️ Keyboard | every key, with Ctrl / Shift / Alt |
| 🖱️ Mouse | middle button, side buttons 4 / 5 (with modifiers) |
| 🎮 Xbox / XInput pads (any of the 4 slots, Steam Input, DS4Windows…) | every button, LT / RT, combos |
| 🕹️ Other controllers — DualShock / DualSense, sticks, wheels, generic pads (DirectInput) | buttons 1-32, two hats, axes pushed past 60 % (triggers, pedals, sticks), combos |

- A key without Ctrl / Shift / Alt also works while you hold Shift to sprint.
- A combo wins over its parts: with `LB` and `LB+Y` both bound, pressing `LB+Y` fires only `LB+Y`; `LB` alone then fires
  when you let go of it. The game does not see a combo's buttons while all of them are held.
- When the panel closes, the game sees your controllers again only after every button on every controller is released.
- **The VR panel works with every controller:** Xbox pads as usual. DirectInput controllers use PlayStation layout:
  hat / left stick to move, Cross = select, Circle = back, Triangle = default, L1 / R1 = tabs, L3 + R3 = open / close.

1. Open the VR panel → **Hotkeys** tab.
2. Select a function, press **A / Enter / click** — then press the key, button or combo (**Esc** cancels).
3. **Right click / Left** clears it, **Y** restores the default. While it listens, **Esc** or a **right click** cancels.

About 25 functions: head aim mode, first person, true stereo on / off, stereo method, recenter, HUD, flat screen, screenshot, theater
size, cutscene mode, swap eyes, world scale, first-person eye position, level horizon, camera shake, foveated
rendering, matched view, anti-aliasing, motion blur, frame cap, the panel itself. Only the six that had keys before
come with a default — the rest are empty for you to fill. Saved as `hotkey_<name>` in `awrvr_config.txt`.

---

## 🥽 STEREO METHODS

| Method | World updates | Notes |
|---|---|---|
| 🔵 **True stereo** *(default)* | every frame | The game renders both eyes each frame — full rate, ~2× GPU. TAA and motion blur are off while it runs. Each eye is drawn symmetric around its lens (*Eye view: Pixel perfect*), so the game's lights, flashlight, flares and shadows land right in both eyes, and the part you see is the headset's own pixel grid, copied 1:1. Protected: if the game stops, it is switched off at the next start and the log says exactly where it stopped |
| 🟢 **Synchronized** | every 2nd frame | Both eyes from the same moment — never doubled |
| 🟢 **Alternate eye** | every frame | Smoothest motion, eyes one frame apart |
| ⚪ **Head (mono)** | every frame | One image for both eyes, no depth |
| ⚪ **Flat screen** | every frame | A big virtual screen |

> 💡 Set **Game anti-aliasing → Off** in the menu for the cleanest image (TAA mixes the two eyes).
>
> ⚡ **Frame rate:** *Synchronized* needs two game frames per stereo pair (87 game fps = 43 pairs/s), so on a 72 / 90 Hz
> headset the runtime may drop to half rate. For smooth motion use **Alternate eye** (a new eye every frame) or
> **True stereo** (both eyes every frame, if your GPU has room).

---

## 🔦 HEAD AIM

**Camera tab → Head aim.** Your shots, the flashlight beam and aim assist go where you look - the camera and the view
never turn with it.

| Mode | |
|---|---|
| **While aiming (L2)** *(default)* | Head aim only while L2 / LT (any controller) or the right mouse button is held |
| **Always** | Every shot goes where you look |
| **Off** | The game's own aim (screen centre) |

- **Aim from the eyes** (on): the aim ray starts where your eyes are, so it hits exactly what you look at - from behind
  Alan's shoulder or, in first person, from his head.
- **Head aim smoothing**: 0 = raw head, 1 = steadiest. Turning with the stick or mouse is never delayed.
- The stick and the mouse still turn the camera as always; head aim adds where you look on top.
- Check: look at an enemy off the centre of the camera and shoot. If the shots don't follow your head, send
  `awrvr.log` (the `headaim:` lines).

---

## 🧍 FIRST PERSON

Press **End**. Calibrated defaults: **Eye height 0.60** and **Eye Z (forward) 0.60** — your eyes sit at Alan's.
Fine-tune in **Camera** if needed. *Near clip in first person* hides the inside of the head.

---

## 🎬 CUTSCENES

| Setting | |
|---|---|
| **Cutscenes** | Theater screen (default) or 3D |
| **Theater size** | 40° – 170° — make it huge |
| **Theater distance** | 2 – 30 m |
| **Fade in and out** | Fades from black when a cutscene starts / ends |

---

## ⚙️ MOST USEFUL SETTINGS

All settings are in the VR menu (with help texts) and in `awrvr_config.txt`.

| Key | Default | |
|---|---|---|
| `vr_mode` | synced | synced · native (true stereo) · aer · head · screen |
| `matched_view` | true | effects (flashlight, light shafts, AO, shadows) correct in both eyes |
| `hud_mode` | scale | scale · safe · panel · off |
| `ui_scale` / `ui_depth` | 0.42 / 1.3 | HUD size and distance |
| `first_person` | false | **End** toggles it |
| `fp_eye_height` / `fp_offset_z` | 0.60 / 0.60 | first-person eye position |
| `head_aim` | aiming | off · always · aiming (while L2 / LT / right mouse is held) |
| `head_aim_smoothing` / `head_aim_from_eyes` | 0.3 / true | tremor filter · the aim ray starts at your eyes |
| `camera_shake` | 0.0 | 0 = no camera shake, 1 = the game's own |
| `fov_mode` | unseen | foveated rendering: `unseen` (only what an eye never shows is coarse) · quality · balanced · performance · off |
| `render_scale` | 1.0 | lower it for more speed (restart) |
| `upscaler_fresh_each_eye` | false | DLSS / FSR / XeSS: never blend one eye's picture into the other's |
| `theater_size` | 100 | cutscene screen size in degrees |
| `swap_eyes` | false | turn on if depth looks inside out |

---

## 🛠️ TROUBLESHOOTING

| Problem | Fix |
|---|---|
| 📄 Logs | `AWRVR\awrvr.log` and `AWRVR\xrhost.log` (the run before: `*.prev.log`) — or *View → Make a report folder*. Search the log for **SETUP** first |
| 🔒 Game folder not writable | Config, log and cache go to `%LOCALAPPDATA%\AWRVR` instead (the log says so) |
| ❌ No log at all | DLL not loaded — check the name and folder; Smart App Control can block unsigned DLLs |
| 🌑 Nothing in the headset | Read `xrhost.log` — it names the active runtime |
| 🐌 Low frame rate | Lower *Render scale*, try *Foveated rendering*, try *Alternate eye* |
| 🔁 Depth inside out | Turn on *Swap eyes* |
| 👻 Doubled image | *Game anti-aliasing → Off*; with DLSS / DLAA try *Upscaler: fresh image each eye* |
| 🎞️ Frame generation | Turn it off (DLSS-G / FSR FG / OptiScaler FG) - the panel shows *FRAME GENERATION ON* |

---

## 🧪 STATUS

Built, discovery checked on the real game exe, run through a D3D12 test under Wine, shaders compiled, unit tests and a
speed-hack test pass. **Played in a headset:** synchronized / alternate eye, first person and the theater.
**Not yet confirmed in the headset:** true stereo (it froze in 0.8.1 — 0.10.0 adds protection and a precise log of
where it stops; 0.10.5 adds GPU-loss detection and safer resource handling), the hotkeys tab, the mouse cursor and
the 0.9.0 – 0.10.3 fixes — please send `awrvr.log` and `xrhost.log`.

---

## 🙏 CREDITS

- **Mark-mon VR framework** (XrBridge, vrkit) and the **VR Mod Framework Guide / Performance Manual** — mark-mon
- Camera findings cross-checked with the **Alan Wake Remastered head-tracking mod**
- [MinHook](https://github.com/TsudaKageyu/minhook) (BSD-2) · [Dear ImGui](https://github.com/ocornut/imgui) (MIT) ·
  OpenXR headers (Khronos, Apache-2.0)
- Panel fonts: [Oswald](https://fonts.google.com/specimen/Oswald) and [Barlow Semi Condensed](https://fonts.google.com/specimen/Barlow+Semi+Condensed)
  (SIL OFL 1.1) · [Special Elite](https://fonts.google.com/specimen/Special+Elite) (Apache-2.0)

*Alan Wake is a trademark of Remedy Entertainment. Unofficial fan mod.*
