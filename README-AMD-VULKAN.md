# WhisperAttack AMD Vulkan Edition

This is an unofficial community AMD/Vulkan build of WhisperAttack.

It uses:

- whisper.cpp
- Vulkan
- AMD-compatible GPU runtime
- VoiceAttack plugin compatibility

The VoiceAttack plugin remains compatible because the socket protocol is unchanged:

- `127.0.0.1:65432`
- `127.0.0.1:65433`
- `start`
- `stop`
- `shutdown`

## Release Package

The release ZIP includes runtime files needed to run transcription:

- `WhisperAttack.exe`
- `settings.cfg`
- `fuzzy_words.txt`
- `word_mappings.txt`
- `whisper_cpp\whisper-cli.exe`
- `whisper_cpp\*.dll`
- `whisper_cpp\models\ggml-base.en.bin`

The Git repo does not include large binaries or models. Keep `whisper_cpp` out of source control and include those files only in release packages.

## Configuration

```ini
# WhisperAttack AMD Vulkan configuration

whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin

theme=default
```

If `whisper_backend` is missing, WhisperAttack defaults to `whisper_cpp_vulkan`. If another value is configured, WhisperAttack logs an error and still uses `whisper_cpp_vulkan`.

## Startup Logs

Successful startup shows:

```text
Using whisper.cpp Vulkan backend for AMD GPU
whisper.cpp Vulkan backend ready
Server started and listening on 127.0.0.1:65432
```

## Credits

Based on the original WhisperAttack project by nikoelt:

https://github.com/nikoelt/WhisperAttack
