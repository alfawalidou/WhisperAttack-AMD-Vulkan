# Build Guide - WhisperAttack AMD Vulkan Edition

This guide explains how to build the AMD/Vulkan edition of WhisperAttack on Windows.

This fork uses `whisper.cpp` with Vulkan for speech recognition and keeps the original VoiceAttack socket workflow unchanged.

## Overview

The build uses:

- Windows 11
- Python 3.11
- Conda or Miniconda
- PyInstaller
- `whisper.cpp` built with Vulkan support
- `ggml-base.en.bin` as the default included model

The Git repository does not include generated runtime binaries, DLLs, build folders, or Whisper model files. These files are copied manually into the final release folder and distributed through the GitHub Release ZIP.

## Requirements

Install the following tools before building:

- Git
- Conda or Miniconda
- CMake
- Visual Studio Build Tools with **Desktop development with C++**
- Python 3.11 Conda environment
- GitHub CLI, only if you want to publish a GitHub release

## 1. Clone the repository

Replace `<your-github-user>` with your GitHub username.

```powershell
cd C:\Path\To
git clone https://github.com/<your-github-user>/WhisperAttack-AMD-Vulkan.git
cd WhisperAttack-AMD-Vulkan
```

## 2. Create and activate the Conda environment

```powershell
conda create -n whisperattack-amd python=3.11 -y
conda activate whisperattack-amd
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install pyinstaller
```

## 3. Build whisper.cpp with Vulkan

Clone and build `whisper.cpp` separately from the WhisperAttack project folder.

```powershell
cd C:\Path\To
git clone https://github.com/ggml-org/whisper.cpp.git
cd whisper.cpp
cmake -B build -DGGML_VULKAN=1
cmake --build build --config Release -j
```

Expected output files include:

```text
build\bin\Release\whisper-cli.exe
build\bin\Release\ggml-base.dll
build\bin\Release\ggml-cpu.dll
build\bin\Release\ggml-vulkan.dll
build\bin\Release\ggml.dll
build\bin\Release\whisper.dll
```

## 4. Copy whisper.cpp runtime files into the project

Return to the WhisperAttack project folder and create the runtime folder.

```powershell
cd C:\Path\To\WhisperAttack-AMD-Vulkan

mkdir whisper_cpp -Force
mkdir whisper_cpp\models -Force

copy C:\Path\To\whisper.cpp\build\bin\Release\whisper-cli.exe .\whisper_cpp\
copy C:\Path\To\whisper.cpp\build\bin\Release\ggml-base.dll .\whisper_cpp\
copy C:\Path\To\whisper.cpp\build\bin\Release\ggml-cpu.dll .\whisper_cpp\
copy C:\Path\To\whisper.cpp\build\bin\Release\ggml-vulkan.dll .\whisper_cpp\
copy C:\Path\To\whisper.cpp\build\bin\Release\ggml.dll .\whisper_cpp\
copy C:\Path\To\whisper.cpp\build\bin\Release\whisper.dll .\whisper_cpp\
```

## 5. Add the whisper.cpp model

The default release uses:

```text
whisper_cpp\models\ggml-base.en.bin
```

Download `ggml-base.en.bin` from the official `whisper.cpp` model source, then copy it into the project:

```powershell
copy C:\Path\To\ggml-base.en.bin .\whisper_cpp\models\
```

The final local runtime structure should look like this:

```text
WhisperAttack-AMD-Vulkan
├─ whisper_cpp
│  ├─ whisper-cli.exe
│  ├─ ggml-base.dll
│  ├─ ggml-cpu.dll
│  ├─ ggml-vulkan.dll
│  ├─ ggml.dll
│  ├─ whisper.dll
│  └─ models
│     └─ ggml-base.en.bin
```

## 6. Check settings.cfg

The default AMD/Vulkan configuration should be:

```ini
# WhisperAttack AMD Vulkan configuration

whisper_backend=whisper_cpp_vulkan
whisper_cpp_exe=whisper_cpp\whisper-cli.exe
whisper_cpp_model=whisper_cpp\models\ggml-base.en.bin

theme=default
```

## 7. Run from source

Before compiling, test the app from source:

```powershell
python whisper_attack.py
```

Expected startup log:

```text
Using whisper.cpp Vulkan backend for AMD GPU
whisper.cpp Vulkan backend ready
Server started and listening on 127.0.0.1:65432
```

Test Start/Stop from VoiceAttack and confirm that recognized text is sent back to VoiceAttack.

## 8. Build the EXE with PyInstaller

Clean old build files first:

```powershell
Remove-Item -Recurse -Force .\build -ErrorAction SilentlyContinue
Remove-Item -Recurse -Force .\dist -ErrorAction SilentlyContinue
Remove-Item -Force .\*.spec -ErrorAction SilentlyContinue
```

Build the application:

```powershell
pyinstaller --onedir --noconsole .\whisper_attack.py --name WhisperAttack
```

The compiled application will be created in:

```text
dist\WhisperAttack\
```

## 9. Copy required files into dist

Copy configuration and support files:

```powershell
copy .\settings.cfg .\dist\WhisperAttack\
copy .\fuzzy_words.txt .\dist\WhisperAttack\
copy .\word_mappings.txt .\dist\WhisperAttack\
copy .\whisper_attack_icon.png .\dist\WhisperAttack\
copy .\add_icon.png .\dist\WhisperAttack\
```

Copy the `whisper.cpp` runtime files:

```powershell
mkdir .\dist\WhisperAttack\whisper_cpp -Force
mkdir .\dist\WhisperAttack\whisper_cpp\models -Force

copy .\whisper_cpp\whisper-cli.exe .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\ggml-base.dll .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\ggml-cpu.dll .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\ggml-vulkan.dll .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\ggml.dll .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\whisper.dll .\dist\WhisperAttack\whisper_cpp\
copy .\whisper_cpp\models\ggml-base.en.bin .\dist\WhisperAttack\whisper_cpp\models\
```

Do not copy larger models unless you intentionally want a larger release package.

## 10. Test the compiled EXE

Run:

```powershell
.\dist\WhisperAttack\WhisperAttack.exe
```

Expected startup log:

```text
Using whisper.cpp Vulkan backend for AMD GPU
whisper.cpp Vulkan backend ready
Server started and listening on 127.0.0.1:65432
```

Then test Start/Stop from VoiceAttack again.

## 11. Verify the build does not include unwanted heavy runtime files

Run this check:

```powershell
Get-ChildItem .\dist\WhisperAttack\_internal -Recurse -File |
Where-Object { $_.Name -match "cuda|cublas|cudnn|torch|ctranslate|onnx|tokenizers|nvidia" } |
Select-Object FullName
```

The ideal result is no output.

Check total package size:

```powershell
$size = (Get-ChildItem .\dist\WhisperAttack -Recurse -File | Measure-Object Length -Sum).Sum
"{0:N2} MB" -f ($size / 1MB)
```

## 12. Create the release ZIP

```powershell
Compress-Archive `
  -Path .\dist\WhisperAttack\* `
  -DestinationPath .\dist\WhisperAttack-AMD-Vulkan-v1.2.2.zip `
  -Force
```

Verify the ZIP:

```powershell
dir .\dist\WhisperAttack-AMD-Vulkan-v1.2.2.zip
```

## 13. Optional: publish a GitHub release

This step requires GitHub CLI and an authenticated GitHub account.

```powershell
gh auth login
```

Create a release:

```powershell
gh release create v1.2.2-amd-vulkan `
  .\dist\WhisperAttack-AMD-Vulkan-v1.2.2.zip `
  .\WhisperAttackServerCommand_VAplugin.zip `
  --repo <your-github-user>/WhisperAttack-AMD-Vulkan `
  --title "WhisperAttack AMD Vulkan v1.2.2" `
  --notes-file RELEASE_NOTES.md
```

If the release already exists and you only want to replace the uploaded assets:

```powershell
gh release upload v1.2.2-amd-vulkan `
  .\dist\WhisperAttack-AMD-Vulkan-v1.2.2.zip `
  .\WhisperAttackServerCommand_VAplugin.zip `
  --repo <your-github-user>/WhisperAttack-AMD-Vulkan `
  --clobber
```

## 14. Git hygiene

Do not commit generated runtime files or model files.

The repository should not include:

```text
whisper_cpp\
dist\
build\
__pycache__\
*.spec
*.bin
*.dll
*.exe
```

These files belong in the GitHub Release ZIP, not in the source repository.

Recommended `.gitignore` entries:

```gitignore
__pycache__/
*.pyc
build/
dist/
*.spec
whisper_cpp/
*.log
*.tmp
```

## 15. VoiceAttack compatibility

The original VoiceAttack plugin remains compatible because the socket protocol is unchanged.

This fork keeps:

- `127.0.0.1:65432`
- `127.0.0.1:65433`
- `start`
- `stop`
- `shutdown`

## Credits

Based on the original WhisperAttack project by nikoelt:

https://github.com/nikoelt/WhisperAttack
