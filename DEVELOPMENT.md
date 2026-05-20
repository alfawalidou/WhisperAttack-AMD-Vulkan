# WhisperAttack AMD Vulkan Development

These notes describe how to run and package the AMD/Vulkan community fork from source.

## Requirements

- Python 3.11 recommended
- VoiceAttack for end-to-end plugin testing
- whisper.cpp built with Vulkan support
- `whisper_cpp\whisper-cli.exe`
- Required whisper.cpp runtime DLLs in `whisper_cpp`
- `whisper_cpp\models\ggml-base.en.bin`

## Source Layout

The whisper.cpp runtime files are expected beside the Python application:

```text
whisper_cpp\
  whisper-cli.exe
  *.dll
  models\
    ggml-base.en.bin
```

The repository should not commit `whisper_cpp`, `build`, `dist`, generated spec files, or Python cache files.

## Run From Source

Create and activate a virtual environment:

```console
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install dependencies:

```console
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Run WhisperAttack:

```console
python whisper_attack.py
```

Startup is ready when the window and log show:

```text
Using whisper.cpp Vulkan backend for AMD GPU
whisper.cpp Vulkan backend ready
Server started and listening on 127.0.0.1:65432
```

Logs are written to:

```text
%LOCALAPPDATA%\WhisperAttack\WhisperAttack.log
```

## VoiceAttack Protocol

The VoiceAttack plugin protocol remains unchanged:

- Python listens on `127.0.0.1:65432`
- VoiceAttack receives recognized text on `127.0.0.1:65433`
- Commands are `start`, `stop`, and `shutdown`

## Build The Executable

Install PyInstaller in the active virtual environment:

```console
pip install pyinstaller
```

Build the app:

```console
pyinstaller --onedir --noconsole whisper_attack.py
```

Copy these files into the generated application folder beside the executable:

- `settings.cfg`
- `fuzzy_words.txt`
- `word_mappings.txt`
- `whisper_attack_icon.png`
- `add_icon.png`
- `whisper_cpp\whisper-cli.exe`
- required `whisper_cpp\*.dll`
- `whisper_cpp\models\ggml-base.en.bin`

Rename the generated executable to `WhisperAttack.exe` for release packaging.

## Cleanup

After a build, remove local generated artifacts before preparing source changes:

```console
deactivate
```

Then delete:

- `.venv`
- `build`
- `dist`
- generated `.spec` files
- `__pycache__`
