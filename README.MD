# Goblin Box

A small desktop dictation app built with PyQt6 and OpenAI Whisper.

It has two modes:

- **Live Dictation** records from your microphone in short chunks and appends the transcribed text as you speak.
- **Audio Transcription** lets you choose an existing audio file and save the transcript as a `.txt` file.

The transcription runs locally through Whisper. On first use, Whisper will download the selected model, so the first run can take a bit longer than later ones.

## Inspiration

Goblin Box is inspired by [goblin.tools](https://goblin.tools/), especially the idea that useful software can be small, focused, and friendly to people who find everyday tasks harder than they should be.

This project is not affiliated with goblin.tools; it just borrows some of that spirit for local voice dictation and transcription.

## Requirements

- Python 3.10 or newer
- A working microphone for live dictation
- System audio support for `sounddevice`
- Enough disk space for Whisper model files

For some audio file formats, you may also want `ffmpeg` installed. If transcription fails on a file that should be valid, try converting it to `.wav` first or install `ffmpeg` through your package manager.

## Setup

### macOS / Linux

```bash
./install.sh
source .venv/bin/activate
python main.py
```

### Windows

From PowerShell:

```powershell
.\install.ps1
.\.venv\Scripts\Activate.ps1
python main.py
```

If PowerShell blocks the installer, run this for the current terminal session and try again:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

## Installing PyTorch

Whisper depends on PyTorch, and the best install command varies by operating system and GPU setup. If the regular install has trouble, install PyTorch first using the selector on the official site:

https://pytorch.org/get-started/locally/

Then rerun:

```bash
python -m pip install -r requirements.txt
```

## Using The App

Run the app with:

```bash
python main.py
```

From the home screen, choose either live dictation or audio file transcription.

Live dictation records five seconds at a time, sends that chunk to Whisper, then appends any recognized text to the text box. You can pause, resume, stop, or clear the current text.

File transcription opens a file picker for common audio formats, transcribes the selected file, and lets you save the result as plain text.

## Project Layout

```text
main.py                 PyQt application entry point
ui/core.py              Main window and page navigation
ui/home.py              Home screen
ui/dictation.py         Live microphone dictation page
ui/transcription.py     Audio file transcription page
audio/recorder.py       Microphone recorder built on sounddevice
stt/whisper_engine.py   Thin wrapper around the Whisper model
requirements.txt        Python dependencies
install.sh              macOS/Linux installer
install.ps1             Windows installer
```

## Troubleshooting

**Whisper model download fails**

The app sets `SSL_CERT_FILE` to the certificate bundle from `certifi`, which usually fixes Python certificate issues. If it still fails, check that the virtual environment is active and that `certifi` is installed.

**`llvmlite` or `numba` fails to build**

This usually means pip is trying to compile a package that is easier to install as a prebuilt binary. Conda or mamba can help here:

```bash
mamba create -n dictation python=3.12 -y
mamba activate dictation
mamba install -c conda-forge llvmlite numba pytorch -y
python -m pip install openai-whisper sounddevice certifi PyQt6 librosa
```

**No microphone input**

Check your operating system's microphone permissions for the terminal or Python app. On Linux, you may also need PortAudio or the matching system audio packages installed.

**Transcription is slow**

The app uses Whisper's `base` model by default. Smaller models are faster but less accurate; larger models are slower but usually better. The model size is set in `ui/dictation.py` and `ui/transcription.py`.

## Notes

The Dockerfile is included, but this is a desktop GUI app that needs microphone and display access, so running it directly on the host machine is the simpler path for normal use.
