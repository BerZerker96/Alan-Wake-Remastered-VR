# Alan Wake Remastered VR (AWRVR) 0.8.1

An unofficial VR mod for **Alan Wake Remastered** (PC, D3D12, Remedy's Northlight engine), built on the DA2 VR
framework and the VR Mod Framework Guide. **One DLL** (`dxgi.dll`): the OpenXR host runs inside the game as a thread.
Works with any OpenXR runtime (SteamVR, Virtual Desktop / VDXR, Oculus / Meta, Pimax, WMR). Built and checked against
the current PC build, **1.33** (February 2026: HDR, 10-bit SDR, the "modern" camera style, gameplay UI scale).

- **Six stereo methods** (F8 cycles them):
  - **Synchronized** (default): both eyes from the same game tick. The game clock waits for the second eye.
  - **True stereo** (new, experimental): the game's renderer draws each eye in the same frame - real depth at the full
    world rate, about twice the GPU work. Falls back to synchronized by itself if the renderer does not take it.
  - **Stereo reconstruction**: one view per frame, with both eyes rebuilt from its picture and depth at full rate.
  - **Alternate eye**: each frame is a new tick for one eye.
  - **Head (mono)**: one head-tracked image.
  - **Flat screen**: a virtual screen.
- **Correct geometry**: each eye is drawn with the headset's own off-centre field of view. Culling uses one wide
  symmetric view, so nothing pops in at the sides.
- **HUD**: either scaled into the middle of each eye at a chosen depth, or cut out onto its own panel. It can also be
  hidden (F7). A picker chooses which UI layer counts as the HUD.
- **First person** (End): your view is at Alan's head. The game's own player camera is moved to its pivot, with no
  shoulder offset, no trailing and no toe-in. The view eases in and out when you toggle it.
- **Comfort**:
  - Decoupled pitch keeps the horizon level.
  - A camera shake filter and an idle-sway lock calm the game camera.
  - A head-bob filter smooths the first-person view.
  - The filters follow steady walking, running and turning with no lag.
  - Your own head movement is never filtered.
- **Flat screen, automatically**, for pause menus, loading and full-size video cutscenes.
- **Game effects for VR**, switched only while the mod runs and never saved into your game options:
  - motion blur, film grain and vignette off
  - TAA set to game, off or on
  - occlusion culling on or off
  - the game's own frame cap
- **Performance**:
  - render size from your headset
  - a headset-rate frame limiter
  - full-rate tracking with no runtime half-rate smoothing
  - fixed foveated rendering (VRS tier 2)
  - frame latency control
  - vsync off on the monitor
- **Edge fill** where the headset sees past the picture.
- **Desktop mirror** of the left eye.
- **HDR input** conversion.
- **Screenshots** (Delete).
- **In-headset VR panel** (Insert, or hold L3 + R3): six tabs (View, Camera, HUD, Graphics, Screen, Advanced).
  - The look: dark and see-through, dark-blue tabs, white text and thin dark-grey frames.
  - Each tab's options sit in framed groups with separators between the rows.
  - Each option has a help text.
  - Only the rows that apply to the chosen stereo method are shown.
  - `tests/menupreview.cpp` renders it offline.

> **Status: built and checked offline, not yet played in a headset.** See "What is verified" before you start.

## Version-agnostic by design

The mod contains no hard-coded addresses. At start-up it finds everything in whatever `Game_f_x64_EOS.exe` you have.
If one item is missing it logs which one, and only that feature is switched off.

| What | How it is found | Used for |
|---|---|---|
| `rend::PerspectiveView::getProjection` | RTTI class name -> vtable slot 2 (code pattern as fallback) | per-eye off-centre projection |
| Lens fields (FOV, zoom, aspect, near, far) | read from getProjection's own instructions, cross-checked 4 ways | culling frustum, near clip |
| FOV / aspect setters + dirty flag | patterns built at run time from the field offsets | keeps the game from undoing the lens |
| Camera transform funnel (`Node::setTransform`) | the call at the end of `BaseCamera::applyTransform` | head pose, eyes, offsets, comfort |
| ~1,450 tweakables (`Camera:Near`, `Camera:FOV`, `TAA:*`, `Vector Blur:*`, `Cinematic Effect:Vignette`, `Camera:* Pos`) | the registration calls that name them, then their value field | effects, first person |
| ~124 renderer switches (`TAA`, `Render Vector Blur`, `Film grain`, `Use Umbra`, `Cinematic Mode`, `VSync Enabled` ...) | the debug-variable bind calls that name them | effects, culling |
| Main-loop frame timer (`Timer::lap`) | unique code pattern | synchronized stereo (holds the clock for the 2nd eye) |
| The game's own frame limiter | unique code pattern (one jump) | `game_frame_cap = off` |
| Game / Menu / Loading / Video / Credits modes | RTTI vtables, per-frame update (slot 6) | game state -> flat screen for menus and loading |
| Window focus events | `ModeSwitch` listener vtable (slot 15), confirmed by its log string | keep running without focus |
| Player object | the "Player %.2fm/s" debug print | first-person diagnostics |
| `Camera:Catch Amount`, `Camera:Angle`, `Camera:Modern Velocity Horizontal Offset Scale` | tweakables (as above) | first person: no trailing, no toe-in, no sideways shift |
| The camera-style switch (1.33) | the one function that reads `Modern Central Offset Scale`, `Mid Pos` and `Angle` | first person keeps the player's own third-person values |
| HDR / SDR output | `IDXGISwapChain3::SetColorSpace1` | `hdr_input = auto` (1.33 also renders SDR in 10-bit) |
| Bink video | the exe's own BinkOpen / BinkClose / BinkNextFrame imports | flat screen for video cutscenes |
| D3D12 device / command list / swap chain | vtables of a throw-away device; the exe's `CreateDXGIFactory` import | capture, depth, HUD, VRS |

### Stores and versions

- **Stores:** Alan Wake Remastered is sold on PC through the Epic Games Store (`Game_f_x64_EOS.exe`).
- **Other stores or a renamed exe:** the mod doesn't depend on the store.
  - The game is recognised by its exe name (`Game_f_x64*`) or, if renamed, by its contents.
  - No Epic, Steam or Xbox service is used.
  - `resolution.xml` is searched in Documents (also when redirected, e.g. to OneDrive), Saved Games, AppData and the
    game folder.
- **Versions:**
  - Nothing is found by address.
  - Every hook that relies on exact instruction bytes also has a fallback that finds the same code by what it does: the
    frame timer by its averaging ring, the pivot height by its getters, the action camera by its float argument, the
    focus handler by its log string in any ModeSwitch slot, and tweakables / renderer switches by their registration
    shape.
  - Pre-1.33 builds simply have no "modern" camera style; that part is skipped.
  - `tests/run_wine_tests.sh` proves the fallbacks: it builds a variant of the exe with the primary byte patterns
    changed and checks the same functions are found.

`tools/verify_signatures.py <exe>` runs the same discovery offline on any build (`pip install pefile`). Run it after a
game update.

## Requirements

1. Alan Wake Remastered for PC (`Game_f_x64_EOS.exe`, D3D12).
2. A PC VR headset with an OpenXR runtime set as active (SteamVR, Virtual Desktop/VDXR, Meta, Pimax, WMR).
3. A gamepad is recommended.
4. In the game's options, **HDR off** and **DLSS off**. Windowed mode and motion blur are handled by the mod.

## Install

Copy `mod\dxgi.dll` next to `Game_f_x64_EOS.exe`. That is all.

- **Remove the Alan Wake Remastered head-tracking mod first.** It is a `dbghelp.dll`, and this mod does head tracking
  itself.
- **Already using a `dxgi.dll` mod, such as ReShade?** You have two choices:
  - Rename the other one to `dxgi_chain.dll`. AWRVR loads it in place of the system dxgi, so both run.
  - Or rename AWRVR's file to `dbghelp.dll`: the game imports that DLL too, and it works the same.
- **Ultimate ASI Loader users** can name it `AWRVR.asi` instead.
- `extras\XrBridgeHost.exe` is optional. It is only used with `host_mode = exe` (the old separate-process host, for
  troubleshooting).
- **First start with the VR runtime running.** The mod remembers your headset's per-eye size and writes it into the
  game's `resolution.xml` at the **next** start. It also sets windowed mode.
  - A backup is kept as `resolution.xml.awrvr_backup`.
  - To keep your own settings, set `render_resolution = game` and `force_windowed = false`.
- **To uninstall,** delete `dxgi.dll` (or `dbghelp.dll`), `awrvr_config.txt` and the `AWRVR` folder. To restore your
  original `resolution.xml`, rename the backup back.
- **Linux / Proton:** set `WINEDLLOVERRIDES="dxgi=n,b"` (Steam launch options: `WINEDLLOVERRIDES="dxgi=n,b" %command%`).
  Proton's built-in HLSL compiler cannot build the stereo-reconstruction shaders, so that method falls back to head mode
  unless the native `d3dcompiler_47` is installed (`protontricks <appid> d3dcompiler_47`). The other methods are
  unaffected.

## Controls

| Action | Gamepad | Keyboard |
|---|---|---|
| VR panel open / close | hold **L3 + R3** | **Insert** |
| Panel: select / change / toggle / page / back | D-pad / A / LB RB / B | arrows / Enter / PgUp PgDn / Backspace |
| Next stereo method | | **F8** |
| Recentre | panel | **F9** (`recenter_key`) |
| First person on / off | panel | **End** (`fp_key`) |
| Hide HUD | panel | **F7** |
| Flat screen now | panel | **F6** |
| Screenshot (left eye PNG next to the exe) | | **Delete** (`screenshot_key`) |

Changes apply at once (or are marked *restart*) and are saved to `awrvr_config.txt` when the panel closes.

## Stereo methods - which one?

| Method | Frame rate of the world | Both eyes match | Notes |
|---|---|---|---|
| **Synchronized** (`synced`) | half the headset rate | always (same tick) | Default. For the 2nd eye the game clock is held. The game steps by an average of recent frame times, so that average is shortened to 1 frame while synchronized. If the frame timer is not found on your build, it uses alternate eye instead (logged). |
| **True stereo** (`native`, experimental) | full | always (same frame, each eye really rendered) | The game's renderer draws the scene twice per frame, once from each eye. Real depth at every pixel, no clock trick. About twice the GPU work. TAA and motion blur are switched off while it runs, the HUD goes on its panel. Falls back to synchronized by itself if the renderer does not take it (logged). |
| **Stereo reconstruction** (`recon`) | full | yes | One wide view per frame, both eyes rebuilt from picture + depth on the GPU. Small fringes at close edges. Best with TAA. |
| **Alternate eye** (`aer`) | full, eyes alternate | one tick apart | Each eye every 2nd frame. "Turn the older eye with the camera" helps while turning. |
| **Head** (`head`) | full | mono | No depth, no ghosting. |
| **Flat screen** (`screen`) | full | flat | Virtual cinema screen. |

TAA mixes the eyes in synchronized and alternate eye, which shows as a double image. Set *Game anti-aliasing* to Off
for those two methods. Reconstruction works best with TAA on.

## First start - what to check, in order

1. Start the OpenXR runtime, then the game. `AWRVR\awrvr.log` appears in the game folder. Check its first lines:
   - `camera hooks installed`
   - `game: frame timer hooked`
   - `xrhost: ... session running` (in `AWRVR\xrhost.log`)
2. The main menu appears on a flat screen in front of you. Press F9 if it is behind you.
3. Load a save. The world switches to 3D around you.
4. **If depth looks inside out:** turn on **Swap eyes**.
   - Synchronized: if moving characters differ between the eyes, try *Synchronized: clock lag* 0, 1 or 2.
   - Alternate eye: try *Frame delay* 1 or 2.
   - Tell me what works (with the log) and I'll make it the default.
5. **If the HUD is not scaled:** step *UI element (picker)* 1, 2, 3 ... with the HUD in view until it shrinks, then
   send me the log.
6. **Size:** use **World scale**. For first person, press End; if you see the inside of the head, raise *Eye Z*.

## True stereo (experimental)

**True stereo** (`vr_mode = native`, panel: *Stereo method → True stereo*) renders both eyes for real in every frame.
It uses the game's own renderer entry that draws one view (`RendererD3D12` slot 10, exe+508970 in 1.33), which the
mod calls twice per frame:

1. **First eye:** the view gets the eye's transform (through the game's own setTransform) and the eye's off-centre
   projection (the game's lazy matrices are made to rebuild). The renderer draws it in full: scene, shadows, effects,
   post.
2. **Capture:** when the second eye's render first touches the back buffer (barrier, clear or bind), the mod records a
   copy of the first eye's finished picture into that same command list. On the GPU it runs after everything the first
   eye drew and before the second eye overwrites it.
3. **Second eye:** the same with the other eye. Its picture is the back buffer; the game then draws its HUD on top.

**What it changes while it runs (restored when you leave it):**
- **TAA and motion blur are off** while true stereo draws 3D (not during the theater or flat screen). Both would take their history and velocities from the *other* eye and draw a ghost
  at every edge. `game_aa = on` overrides this if you want to try TAA anyway.
- **The HUD is on its panel.** The game draws the HUD once, into the second eye's picture; the mod splits it off and
  shows it to both eyes at *UI depth*, sized by *UI scale* (*Hide the game HUD* / F7 hides it).
- Culling uses one frustum around both eyes.

**Cost:** about twice the GPU time per frame (everything is drawn twice; CPU game logic is not). Lower *Render scale*
or the game's resolution if the frame rate drops.

**It switches itself back to synchronized** and says why in the log and the panel status line (`Synced - fallback`):
- the renderer's view render is not found on your build;
- the renderer never draws the main view through it for 240 frames;
- the first eye's picture is never captured between the two renders for 120 frames in a row (the renderer finishes
  its picture somewhere else on your build).

Choose another method and then *True stereo* again to retry.

**Log lines to send:** `true stereo: renderer view renders ...` (eye pairs, first eye captured, missed) and
`true stereo: back buffer binds ...`. The stereo diagnostics line is always written while true stereo is selected.

## How the 3D picture is kept correct (from the game's own code)

- **Each eye's projection.** The game rebuilds a view's projection only when its projection-dirty flag is cleared. The
  mod clears it for every new eye, so each eye gets its own off-centre projection. TAA jitter is applied by the game on
  top of it.
- **Culling.** Culling uses the view's lens fields, which hold one wide frustum covering both eyes. Nothing pops in at
  the sides.
- **Render snapshots.** Every frame the game clones the camera view for the renderer.
  - Each snapshot carries the eye it was taken for.
  - When the renderer rebuilds a snapshot on the Present thread, the eye shown at Present is known exactly. That also
    measures the pipeline lag.
  - The log's `xr: eye sync:` line shows how many frames were matched exactly.
- **Main camera.** The camera the mod drives is the view the game clones every frame (with the back buffer's aspect),
  not a guess from projection calls.

## Cutscene theater

In-game cutscenes and the pre-rendered cinematics play on a big cinema screen in front of you. The settings are under
Screen, Cutscenes.

- **Theater size** sets how much of your view the screen fills, in degrees across: 60 is a big TV, 100 a cinema (the
  default), and 140 or more wraps around beyond your headset's view.
- **Theater distance** sets how far away the screen is. The size stays the same, so a farther screen looks calmer.
- **Fade in and out** fades in from black whenever a cutscene starts or ends.
- **Cutscenes = 3D in the scene** keeps in-game cutscenes around you, with head tracking, instead.

The game draws in-game cutscenes through its own `CinematicCamera` class; that is how the mod recognises them.
Pre-rendered cinematics are recognised as full-size Bink videos.

## Camera shake

**Camera shake** (Camera tab, default 0) sets how much of the game's own shake you get in VR, from 0 (none) to 1 (the
game's own). It covers hits, explosions, weapons and script shakes.

It is removed at the source. The game's shake controller (`CameraShakeController::Apply`) runs on a copy of the camera,
so its timers behave exactly as normal, and the mod keeps only the chosen fraction.

## HUD

- **Scaled into view** (default): the game's HUD is moved into the middle of each eye and shrunk to *UI scale*,
  converged at *UI depth*.
  - Only blended 2D draws (sprites and text) into the final picture count as HUD, so the 3D picture itself is never
    shrunk.
- **Game safe area**: the game itself lays out its HUD closer to the centre, using its own safe-area setting
  (`hud_safe_x` / `hud_safe_y`).
  - It stays at full sharpness; the mod only moves it to each eye's centre with convergence.
  - The safe area also insets subtitles and menus.
- **Own panel**: the HUD split off the picture onto its own head-locked panel at *UI depth*. *UI scale* sets its
  size (the same size in your view as the scaled HUD), *UI height* raises or lowers it. True stereo always uses it.
- **Game layout**: the game's own HUD placement, untouched.
- **This mod's menu** shares the head-locked panel while it is open, at its own distance and width. On the flat screen
  and the theater it keeps the same size in your view whatever the screen's size. *This menu's opacity* sets how much
  of the game shows through.
- The game's own "gameplay UI scale" option (added in 1.33) works with every mode.

## First person

Press **End**, or use the panel (Camera, then First person).

**What it changes, only while first person is on:**
- The game's own player camera is moved to its pivot: the four `Camera:* Pos` offsets are set to the *Eye X / Y / Z*
  values.
- The camera's trailing is switched off (`Camera:Catch Amount` 0), so your viewpoint never lags behind Alan.
- The shoulder camera's toe-in is switched off (`Camera:Angle` 0), so the view looks straight along the aim.
- The 1.33 modern style's sideways shift with speed is switched off.

The game blends to the new values on its own, so the view eases in and out. When you turn first person off, every
value goes back to the game's own value.

**It is safe with the game's own options.** If you change the camera style in the game's options while in first
person, the mod lets the game apply the switch to your third-person values, then keeps first person.

**Cutscenes, the car camera and scripted cameras keep the game's camera.** First person only touches the player
camera.

**Eye height:**
- The game's camera pivot is Alan's feet plus a per-character height, which the mod reads from Alan each second.
- *Eye height* (default 1.68 m above the feet) puts your eyes there exactly, whatever that height is. The difference is
  applied along world up on the player camera, so it does not tilt with the camera's pitch. *Eye Y* is a fine-tune on
  top.
- The log's `firstperson: camera pivot ... m above Alan's feet, eye at ... m` line shows both values every 10 s.

**Body and head:**
- You will see Alan's body when you look down.
- *Near clip in first person* defaults to the game's own 0.2 m. That hides the inside of his head.
- If you still see the inside of the head, raise *Eye Z* (forward).

**Head bob filter:** smooths the height only. Walking, stairs and jumps are followed without delay.

**Action cameras** (Camera tab, default *No camera swing*): the game's slow-motion moments (dodge, melee, throw) swing
the camera around Alan. In VR that swing is skipped and the slow motion stays. Set it to *As the game* to keep the
swing.

## Settings

All settings are in the VR panel with help texts, and in `awrvr_config.txt`. The file is written with every key and its
default on the first start and grouped like the panel. These keys exist only in the file:

- `camera_axes`
- `render_max_side`
- `paced_timeout_ms`
- the key names
- `camera_hooks`, `ui_hooks`, `xr_output` (diagnostics switches)
- `menu_opacity`
- `host_mode`
- `static_camera_refresh`

Config migration: a 0.1 config is upgraded once and logged.

- `vr_mode = stereo` becomes `aer`.
- `frame_lag` becomes `aer_frame_delay`.
- `fov_margin` becomes `cull_margin`.
- Keys that no longer exist are removed.

No other value of yours is changed.

**Not applicable to this game** (guide options left out on purpose):

- `skip_launcher`: there is no launcher.
- `exact_render_size`: the game renders at the `resolution.xml` size.
- `fp_hide_body`: there is no head-mesh switch.
- `game_upscaling`: DLSS is not hookable without its SDK; turn it off in the game.
- The guide's depth-of-field and radial-blur switches: no such tweakables or renderer switches exist in this exe.
- The performance manual's "render twice per tick" full stereo: now available as **True stereo** (experimental, see
  above). Synchronized remains the default until true stereo is confirmed in the running game.

## Known limitations

- **Synchronized** halves the world's update rate. Animation runs at half the headset rate, while head tracking stays
  at the full rate.
- **Cutscene black bars:** `no_black_bars` switches the game's "Cinematic Mode" variable. It is not verified that this
  is the letterbox, so it is off by default and marked test.
- The mouse pointer is drawn on the desktop, not in the headset.
- Head-relative controls: the head pose goes where the game moves its camera, so walking and aiming follow where you
  look.

## What is verified

| | |
|---|---|
| Built with mingw-w64 x64, no warnings except formatting. Exports checked: 3 own DXGI factory functions, 17 forwarded DXGI exports and 85 forwarded dbghelp exports. The DLL does not import dxgi.dll itself (checked by the build) | yes |
| Installed as **dxgi.dll** in a D3D12 test program under Wine + vkd3d + lavapipe: factory, swap chain (Present, Present1, ResizeBuffers/1, SetFullscreenState, SetColorSpace1) and all 14 D3D12 device / command-list hooks installed; the compositor and in-process OpenXR host start; 300 frames rendered and presented with every hook active | yes |
| Installed as **dbghelp.dll**: loads, forwards dbghelp calls, survives having no D3D12 device | yes |
| Every hook above found in the supplied `Game_f_x64_EOS.exe` (timestamp 697455b7), by the mod's own C++ code run against the real exe under Wine, and independently by `verify_signatures.py` | yes |
| Focus handler: disassembled. Event byte +4 = 0 is "Lost" (the only event the mod drops) | yes |
| Shaders (crop / edge fill / sharpen / HDR, UI split, reconstruction splat + resolve) compile with Microsoft DXC. The display shaders also compile with Wine's d3dcompiler. The mod uses Windows' d3dcompiler_47 at run time, which was not run here | DXC yes, fxc no |
| The VR panel's layout (all six tabs), rendered offline from the real code | yes (images in `docs/`) |
| Unit tests: pose / projection maths, handedness, decoupled pitch, refresh-rate detection (Pimax / VDXR), and the comfort filters. A 1.5 m/s walk is followed within 2 mm, a 90°/s turn within 0.1°, an 8 Hz 3 cm shake drops to 0.5 cm and a 2 Hz 4 cm head bob to 2.1 cm | yes |
| First person, worked out from the exe's own camera code (disassembled): what the offsets, Catch Amount, Angle and the style switch do, how and when the camera reads them (live, blended every frame), and where the pivot is (Alan's feet + character height) | yes, from the code |
| Tracking: setTransform's input is the camera's world transform (rows right / up / forward, then position; no parent node, re-orthonormalised by the game) - the same layout the mod composes the head pose into | yes, from the code |
| HUD: the game's getSafeArea (exe+1e0710, 59 HUD / subtitle / menu callers) and the safe-area tweakables; HUD = HUDMode atlas sprites (blended 2D) | yes, from the code |
| The projection pipeline, from the code: getProjection's matrix layout, the lazy rebuild (dirty flags), TAA jitter multiplied on top, culling from the lens fields, the per-frame render snapshots (clone) | yes, from the code |
| Synchronized stereo against a model of the game's frame-time code: the old method moved the world 11.2 ms between the eyes; the new one 0.1 ms, with game time keeping up | yes (`tests/test_syncclock.cpp`) |
| Every new signature (view clone, frame-time ring, pivot height, action camera, style switch) found on the real exe, both by the mod (Wine) and by `verify_signatures.py` | yes |
| Fallbacks: on a variant exe with the primary byte patterns changed, the frame timer, dt ring, pivot height and action camera are found at the same addresses; all 27 tweakables / switches resolve identically with the tolerant index | yes |
| Theater / shake / renderer hooks found on the real exe (Wine discovery + verifier): CameraShakeController::Apply exe+172db0, RendererD3D12 view render exe+508970 | yes |
| 0.8.1 audit: swap eyes in every method, HUD panel placement and size, menu size on the flat screen / theater, the reconstruction fade, true stereo verdicts and fall-back switches - code reviewed, built, the new shader compiled with DXC and Wine's compiler, the Wine D3D12 run and discovery repeated | yes (code + build + tests); **in the headset: no** |
| Theater and shake in the running game | **no** |
| True stereo, from the code: the renderer's view render (exe+508970) takes only register arguments (view in rdx, kept in r14), its one caller (exe+4a661b) ignores the return value, it rebuilds the view's matrices itself (exe+46c890) and renders the view in full (exe+5090b0). Found by the mod on the real exe (Wine) and by the verifier | yes, from the code |
| True stereo in the running game: two renders per frame without side effects, the first-eye capture point, the frame-time cost | **no - the mode falls back to synchronized by itself if it does not work; please send the `true stereo:` log lines** |
| Other store builds / older versions of the game | **no - none available here; the log names any hook not found** |
| In the running game: exact eye sync rate, measured lag, first-person eye height, body / head visibility | **no - send the log's `xr: eye sync:` and `firstperson:` lines** |
| In the real game with a headset: D3D12 capture, the six methods, HUD scaling, depth reconstruction, VRS, first person, the in-process OpenXR host, Bink detection | **no - not tested yet** |

## Troubleshooting

- Logs are `AWRVR\awrvr.log` (mod, a summary every 10 s per section) and `AWRVR\xrhost.log` (OpenXR). Attach both to
  bug reports.
- **The game does not start after installing:** another `dxgi.dll` was overwritten, or a DXGI tool conflicts. Rename
  AWRVR's file to `dbghelp.dll`.
- **No log:** the DLL was not loaded. Check the name (`dxgi.dll` or `dbghelp.dll`) and the location. Smart App Control can block
  unsigned DLLs.
- **`camera NOT usable on this build`:** a game update changed the code. The log names the step. The game still runs
  on the flat screen.
- **Nothing in the headset:** read `xrhost.log`. It lists the active runtime (registry `ActiveRuntime`) and the API
  layers. Try `host_mode = exe` with `extras\XrBridgeHost.exe` next to the game.
- **Stutter:**
  - Check the `perf:` line.
  - Set *Headset smoothing* to Runtime.
  - Lower *Render scale*.
  - Try foveated rendering.
  - Use reconstruction instead of synchronized.

## Building

`scripts/build_mingw.sh` builds it (Linux / WSL: `apt install mingw-w64`).

Tests:

- `tests/run_wine_tests.sh <Game_f_x64_EOS.exe>` runs these under Wine:
  - proxy loading as dbghelp.dll and as dxgi.dll
  - a D3D12 render loop through the mod (needs `xvfb-run` and a Vulkan driver such as Mesa's lavapipe)
  - full discovery on the real exe
- `tests/test_math.cpp` and `tests/test_comfort.cpp` are native unit tests.
- `tests/shadertest.cpp` compiles the shaders with D3DCompile.
- `tests/menupreview.cpp` renders the VR panel's tabs to PNGs.

## Credits

- DA2 VR framework (XrBridge host + client, vrkit, proxy loader) and the VR Mod Framework Guide / Performance Manual
  by mark-mon.
- The original Alan Wake VR mod (fholger) was used as a feature reference only; no code was taken from it.
- Camera findings were cross-checked with the Alan Wake Remastered head-tracking mod.
- Researched, but no code used:
  - the "HUD and invisible character toggler" (Nexus #9, ReShade Shader Toggler). It shows the player mesh cannot be
    hidden alone: it also hides NPCs and doors.
  - the published Cheat Engine tables and trainers (gameplay cheats only, no camera data).
- [MinHook](https://github.com/TsudaKageyu/minhook) (BSD-2), [Dear ImGui](https://github.com/ocornut/imgui) (MIT),
  OpenXR headers (Khronos, Apache-2.0).
- Alan Wake is a trademark of Remedy Entertainment. This is an unofficial fan mod.
