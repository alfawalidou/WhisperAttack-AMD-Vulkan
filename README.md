# WhisperAttack AMD Vulkan

This repository is an unofficial AMD/Vulkan community fork of WhisperAttack. It uses whisper.cpp with Vulkan for local speech recognition, then sends the recognized text into VoiceAttack.

The original VoiceAttack plugin remains compatible because the socket protocol is unchanged:

- Python listens on `127.0.0.1:65432`
- VoiceAttack receives recognized text on `127.0.0.1:65433`
- Commands remain `start`, `stop`, and `shutdown`

## Features

- Records microphone audio on demand through the existing VoiceAttack workflow.
- Runs transcription through `whisper_cpp\whisper-cli.exe`.
- Uses `whisper_cpp\models\ggml-base.en.bin` by default.
- Sends recognized text into VoiceAttack.
- Preserves word mappings, fuzzy word correction, phonetic correction, and the DCS kneeboard clipboard workflow.
- Displays startup and transcription logs in the application window and log file.

## Requirements

- Windows
- VoiceAttack with plugin support enabled
- AMD-compatible GPU runtime with Vulkan support
- whisper.cpp Vulkan runtime files beside the application

Expected runtime files:

```text
whisper_cpp\whisper-cli.exe
whisper_cpp\*.dll
whisper_cpp\models\ggml-base.en.bin
```

The Git repository does not include large binaries or models. They are provided in release packages or can be added manually.

## Configuration

Default configuration lives in `settings.cfg` beside the application. User overrides can be placed in:

```text
C:\Users\username\AppData\Local\WhisperAttack
```

Clean AMD/Vulkan configuration:

```ini
# WhisperAttack AMD Vulkan configuration

whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin

theme=default
```

- `whisper_backend` defaults to `whisper_cpp_vulkan` when missing. Any other value is logged as an error and `whisper_cpp_vulkan` is still used.
- `whisper_cpp_exe` points to the whisper.cpp CLI executable.
- `whisper_cpp_model` points to the whisper.cpp model file.
- `theme` can be `default`, `dark`, or `light`.

## Running WhisperAttack

From a release package, run `WhisperAttack.exe`.

From source:

```powershell
cd F:\ED-backup-binding\WhisperAttack_vulkan\WhisperAttack
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
python .\whisper_attack.py
```

Startup is ready when the window/log shows:

```text
Using whisper.cpp Vulkan backend for AMD GPU
whisper.cpp Vulkan backend ready
Server started and listening on 127.0.0.1:65432
```

Logs are written to:

```text
%LOCALAPPDATA%\WhisperAttack\WhisperAttack.log
```

## VoiceAttack Plugin Usage

Enable plugin support in VoiceAttack, place the original `WhisperAttackServerCommand` plugin in the VoiceAttack Apps folder, then create two profile commands:

- `Start Whisper Recording`: execute the plugin with context `Start Whisper Recording`.
- `Stop Whisper Recording`: execute the plugin with context `Stop Whisper Recording`.

Assign the start command to your push-to-talk press action and the stop command to the matching release action.

Closing VoiceAttack sends `shutdown` to WhisperAttack through the same protocol.

## Word Mappings

`word_mappings.txt` replaces recognized words or phrases with preferred output text. Multiple aliases can map to the same replacement by separating aliases with semicolons:

```text
gulf;gold=Golf
inter=Inter
```

New mappings can also be added from the WhisperAttack window.

## Fuzzy Words

`fuzzy_words.txt` contains DCS-related terms that should be corrected when recognition is close but not exact. Add one term per line. Custom entries can be placed in the user configuration directory.

## DCS Kneeboard

Say `Note` followed by the text to send to the DCS kneeboard workflow. WhisperAttack strips the trigger phrase, formats the remaining text, copies it to the clipboard, and sends the configured keyboard shortcut.

## VAICOM Integration

Instructions for integrating with VAICOM are in [VAICOM INTEGRATION](./VAICOM%20PRO/VAICOM_INTEGRATION.md).

## Credits

Based on the original WhisperAttack project by nikoelt:

https://github.com/nikoelt/WhisperAttack
