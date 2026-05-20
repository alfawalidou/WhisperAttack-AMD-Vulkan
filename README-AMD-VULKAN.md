# WhisperAttack AMD Vulkan Edition

This fork adds an optional whisper.cpp Vulkan backend to WhisperAttack.

It is intended for AMD GPU users on Windows who cannot use the original CUDA/NVIDIA GPU backend.

## What changed?

The original VoiceAttack socket protocol is unchanged.

This fork only changes the transcription backend:

- Original backend: faster-whisper / CUDA / CPU
- Added backend: whisper.cpp / Vulkan

## Tested setup

- Windows 11
- AMD Radeon RX 6950 XT
- whisper.cpp Vulkan build
- Original VoiceAttack plugin compatible

## Configuration

Example settings.cfg:

```ini
whisper_model=small.en
whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin
theme=default

## VoiceAttack plugin compatibility

The original `WhisperAttackServerCommand.dll` remains compatible.

This fork does not change:

- `127.0.0.1:65432`
- `127.0.0.1:65433`
- `start`
- `stop`
- `shutdown`

## Runtime files

The release ZIP should include:

- `WhisperAttack.exe`
- `settings.cfg`
- `fuzzy_words.txt`
- `word_mappings.txt`
- `whisper_cpp\whisper-cli.exe`
- `whisper_cpp\*.dll`
- `whisper_cpp\models\ggml-base.en.bin`

The Git repository does not include whisper.cpp binaries or models because they are large generated/runtime files.

## Credits

Based on the original WhisperAttack project by nikoelt:

https://github.com/nikoelt/WhisperAttack