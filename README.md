# khanplus-live-update
Private live-update hosting for the KHAN PLUS launcher.
# Live update setup

The launcher reads `KhanUpdate.url` beside its EXE. Put the HTTPS URL of the hosted `manifest.txt` on its first line. The configured GitHub URL is `https://raw.githubusercontent.com/viruss2501-arch/khanplus-live-update/main/manifest.txt`.

Run `build.bat` to build `KhanLauncher.LiveUpdate.exe` and prepare `KhanUpdatePackage`. Upload the **contents** of that folder to the root of the public GitHub repository `viruss2501-arch/khanplus-live-update` on its `main` branch. Preserve the relative paths, including `data/` and `Save/`; do not put the files inside an extra package subfolder. The hosted `manifest.txt` must be reachable at the URL entered in `KhanUpdate.url`.

`KhanUpdate.url` is a small settings file, not a launcher window or game report. It can be hidden in File Explorer without affecting updates: right-click it, choose **Properties**, check **Hidden**, then click **Apply**.

The launcher starts the game's Windows executable (`KhanPlus.exe`, with known alternatives) directly, using the game folder as its working directory; it does not ask the user to browse for a separate `.exe`. `data/.kp0` is game data, not a runnable program. The `khan.ico` file is embedded into the launcher for both its file and window icon.

The manifest format is UTF-8 text:

```text
KHANUPDATE/1
<64-character SHA-256><TAB>DeviceInfo.dif
<64-character SHA-256><TAB>data/Msg/EmotiMsg.bin
```

The package builder includes `KhanPlus.exe` and copies the built launcher to `IZRGulid.exe`, then generates manifest entries and hashes for all files, including files found below `data/Effect/` and `data/EffectData/`. Rebuild and re-upload the package whenever source data changes. The launcher requires all 34 fixed paths and permits additional paths only below those two asset directories (up to 1,024 asset files total), downloads changed files over HTTPS, verifies their SHA-256 hashes, stages downloads before replacing files, and does not create `.launcher-backup` files.

## Adding files in a future update

- For a new file under `data/Effect/` or `data/EffectData/`, put it in that source folder and run `build.bat`; the package builder discovers those folders recursively and adds each file and hash to `manifest.txt`.
- For a file at any other path, add its relative path (using Windows `\` separators) both to the `UpdateFiles` array in `Test.Mini.DarkThai.Net40.V4.AutoResize.cs` and to `$relativePaths` in `Build-UpdatePackage.ps1`. Put the source file at that relative path in the project folder, then run `build.bat`.
- Upload the changed/new file(s) from `KhanUpdatePackage` to the same relative paths at the repository root, and upload the new `manifest.txt` last. Do not upload the entire project folder or place package files in an extra nested directory.

SHA-256 detects incomplete or altered downloads relative to the manifest. HTTPS protects the connection, but this design does not authenticate a compromised hosting account or a malicious manifest served by the configured host. Use a protected hosting account; signed manifests should be added before treating the host as untrusted.
