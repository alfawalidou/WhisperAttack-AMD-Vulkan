# WhisperAttack AMD Vulkan v1.2.2

Unofficial community AMD/Vulkan build of WhisperAttack.

## Release Files

Two ZIP files are provided:

1. `WhisperAttack-AMD-Vulkan-v1.2.2.zip`
2. `WhisperAttackServerCommand_VAplugin.zip`

## Application Package

`WhisperAttack-AMD-Vulkan-v1.2.2.zip` includes:

- `WhisperAttack.exe`
- `settings.cfg`
- `fuzzy_words.txt`
- `word_mappings.txt`
- `whisper_cpp\whisper-cli.exe`
- `whisper_cpp\*.dll`
- `whisper_cpp\models\ggml-base.en.bin`

Included model:

```text
whisper_cpp\models\ggml-base.en.bin
```

Users can add other whisper.cpp models manually into:

```text
whisper_cpp\models\
```

Then update `whisper_cpp_model` in `settings.cfg`.

## VoiceAttack Plugin Package

`WhisperAttackServerCommand_VAplugin.zip` includes the VoiceAttack plugin package.

The plugin remains compatible because the socket protocol is unchanged:

- `127.0.0.1:65432`
- `127.0.0.1:65433`
- `start`
- `stop`
- `shutdown`

## Default Configuration

```ini
# WhisperAttack AMD Vulkan configuration

whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin

theme=default
```

## Notes

The Git repository does not include large binaries or models. Runtime files are included in the release package.

## Credits

Based on the original WhisperAttack project by nikoelt:

https://github.com/nikoelt/WhisperAttack
