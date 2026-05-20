# WhisperAttack AMD Vulkan v1.2.2

Unofficial AMD/Vulkan build of WhisperAttack.

This release is intended for Windows users with AMD GPUs who cannot use the original CUDA/NVIDIA GPU backend.

## What changed

- Added a whisper.cpp Vulkan backend for AMD GPUs.
- Keeps the original VoiceAttack socket protocol unchanged.
- The original VoiceAttack plugin remains compatible.
- Tested on Windows 11 with AMD Radeon RX 6950 XT.
- Uses whisper.cpp Vulkan runtime files.
- Default included model: ggml-base.en.bin.

## Included files

This release includes two ZIP files:

### 1. WhisperAttack-AMD-Vulkan-v1.2.2.zip

Main AMD/Vulkan application package.

It includes:

- WhisperAttack.exe
- settings.cfg
- fuzzy_words.txt
- word_mappings.txt
- whisper_cpp\whisper-cli.exe
- whisper_cpp\*.dll
- whisper_cpp\models\ggml-base.en.bin

### 2. WhisperAttackServerCommand_VAplugin.zip

Original VoiceAttack plugin package.

The plugin is included separately for convenience.

## VoiceAttack compatibility

The original WhisperAttackServerCommand.dll remains compatible.

This fork does not change:

- 127.0.0.1:65432
- 127.0.0.1:65433
- start
- stop
- shutdown

Only the transcription backend was changed.

## Configuration

Default settings.cfg:

```ini
whisper_model=small.en
whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin
theme=default

## Notes

This is an unofficial community AMD/Vulkan build.

The Git repository does not include whisper.cpp binaries or Whisper models because they are large runtime files. They are provided in the release ZIP instead.

Additional whisper.cpp models can be placed in:

```text
whisper_cpp\models\

Then update settings.cfg to point to the desired model.

## Credits

Based on the original WhisperAttack project by nikoelt.

## Original CUDA/NVIDIA source project:

https://github.com/nikoelt/WhisperAttack