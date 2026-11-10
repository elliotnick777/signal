# NULL//SIGNAL

An offline analog horror photo editor for Windows and Linux. Built with Python,
PySide6, Pillow and NumPy. No network calls, accounts, model downloads or services.

## Features

- Seven stackable effects with individual intensity controls.
- Automatic preview updates and before/after comparison.
- Zoom, pan and fit-to-window controls.
- Repeatable random combinations of 2–4 effects.
- Full-resolution export without overwriting the source.
- Background processing to keep the controls responsive.
- A dark terminal interface with subtle CRT details and fictional system logs.

## Install and launch

Use Python 3.10 or newer (64-bit recommended). Extract this folder and open a
terminal inside `NULL_SIGNAL`.

```sh
pip install -r requirements.txt
python main.py
```

An optional virtual environment keeps the dependencies separate:

```sh
python -m venv .venv
```

Activate it with `.venv\Scripts\activate` on Windows, or
`source .venv/bin/activate` on Linux, then run the installation and launch commands
above. Installation requires packages to be available from the internet or a local
wheel cache; the installed application works entirely offline. Nothing installs
itself when the application starts.

On Linux, a working desktop session and the system libraries used by Qt are needed.
If Qt reports a missing `xcb` platform dependency, install the corresponding Qt/X11
runtime packages through your distribution. A Wayland desktop can also run Qt
natively. Windows users should launch from a terminal to see dependency/startup errors.

## WSL

On WSL, a working GUI setup such as WSLg is required to display the window.
Follow the Linux installation steps inside your WSL distribution. Windows drives
are accessible under `/mnt`; for example, your Downloads folder may be at
`/mnt/c/Users/<username>/Downloads`.

For later launches, activate the environment from the project directory:

```sh
source .venv/bin/activate
python main.py
```

## Edit an image

1. Click **LOAD IMAGE** and choose a JPG, JPEG, PNG or BMP file.
2. Enable effects on the right and adjust their 0–100 intensity sliders. Changes
   automatically render after a short pause. **PROCESS IMAGE** renders immediately.
3. Use **BEFORE** / **AFTER** to compare the original with the current output.
   **FIT** fits the image to the monitor; **+**, **−**, or the mouse wheel zoom.
   Drag the image to pan when zoomed in.
4. **RANDOMIZE** selects 2–4 channels with new intensities and a new signal seed.
   **RESET** disables every effect and restores the original pixels in the output.
5. **SYSTEM LOG** changes the fictional transmission and quote. These also change
   occasionally while the application is open.

| Channel | Processing |
| --- | --- |
| VHS | Scanlines, RGB separation, analog noise and horizontal tape drift |
| STATIC | Monochrome noise, film grain and random interference bands |
| GLITCH | Horizontal band displacement, RGB splitting and shifted pixel blocks |
| GHOST FRAME | Semi-transparent exposure with horizontal offset |
| CCTV | Grayscale, high contrast, vignette, grain and scanlines |
| DISTORTION | Horizontal waves and subtle vertical warping |
| DARKROOM | Desaturation, deeper shadows, contrast and warm vintage grain |

Effects run in the listed order. Each render starts from the original, so processing
never accumulates. The current settings and seed reproduce the same pixels during
this session; clicking PROCESS again does not generate new noise. The seed changes
only when RANDOMIZE is used. Settings are session-only.

## Export

Click **EXPORT IMAGE**, choose a destination and select PNG, JPEG or BMP. The export button becomes available once the current settings have finished rendering. The saved image retains
full dimensions; the preview may use a smaller display copy. Export uses the current
effect output even while BEFORE is selected.

PNG retains transparency. JPEG and BMP flatten transparency against black. JPEG
uses quality 95 and is lossy. Camera orientation is applied on load; portrait camera
files may therefore show swapped width and height relative to their raw sensor
layout. Camera metadata and embedded color profiles are not copied to exports.

The original path and aliases of it are protected, even if selected in the export
dialog. Choose a different name. Existing export files require confirmation.
Writes use a temporary file and replace the destination only after encoding succeeds.

## Responsiveness and errors

Loading, processing and exporting run in a background thread. Rapid slider changes
are combined, and outdated renders are discarded. Full-resolution processing can
use substantial memory on large photos, particularly with several effects. Closing
during a job waits for that job to finish safely. Unsupported, damaged or excessively
large images and failed exports show an error dialog.

The monitor has a very faint CRT overlay. It is interface decoration and does not
appear in exports. Enabled effect scanlines do appear in exports.

## Project files

- `main.py`: window, controls, preview and background jobs.
- `effects.py`: deterministic image processing, decoding and safe export.
- `requirements.txt`: the three runtime dependencies.
- `test_app.py`: effect, file and GUI workflow checks using standard `unittest`.

Run checks from this directory:

```sh
python -m unittest -v
```

GUI tests use Qt's offscreen platform and are skipped when PySide6 is unavailable.
They exercise launch, loading, all channels, randomization, comparison, reset,
export and invalid-file feedback. No extra test dependency is required.

