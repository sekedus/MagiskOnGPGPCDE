# Troubleshooting

This page provides troubleshooting steps for common issues that may arise when using Google Play Games on PC (GPGPC).

If you encounter any problems, refer to the sections below for potential solutions.

<br/>

## Exit Google Play Games on PC

1. In the **taskbar notification area** or **system tray icon**, right-click Google Play Games on PC.
2. Click **Exit**.

<br/>

## Restart Google Play Games on PC

1. In the **taskbar notification area** or **system tray icon**, right-click Google Play Games on PC.
2. Click **Exit**.
3. Reopen **Google Play Games on PC**.

<br/>

## Update Google Play Games on PC

1. In the **taskbar notification area** or **system tray icon**, right-click Google Play Games on PC.
2. Click **Check for updates**.
3. Follow on-screen instructions to finish updating.

<br/>

## Open advanced settings in Google Play Games on PC (Prod)

1. Open Google Play Games on PC (Prod).
2. At the top right, click your **Gamer name**.
3. Click **Settings**.
4. Under "About," click the **App version number** 7 times.
5. Click **Continue**.

<br/>

## Temporarily disable sudo password prompt (WSL2)

1. Open your WSL2 distribution (e.g., Ubuntu).
2. Create a file and set the correct permissions:

   ```bash
   username=$(whoami)
   echo "$username ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/gpgpc_patcher_tmp
   sudo chmod 0440 /etc/sudoers.d/gpgpc_patcher_tmp
   sudo visudo -cf /etc/sudoers.d/gpgpc_patcher_tmp

   ```
3. Run the patcher.
4. When finished, remove the temporary sudoers file to restore normal behavior:

   ```bash
   sudo rm /etc/sudoers.d/gpgpc_patcher_tmp
   ```

<br/>

## Save patch session

Record a full patch session and save it to `patch-log.txt` in the patcher folder.

Capture full session with timestamps:
```powershell
$log = "logs\patch-log-$(Get-Date -Format 'yyyyMMdd-HHmmss').txt"
Start-Transcript -Path $log -Force
# ...run patcher...
Stop-Transcript
```

Example usage with **Dev** version:
```powershell
$log = "logs\patch-log-$(Get-Date -Format 'yyyyMMdd-HHmmss').txt"
Start-Transcript -Path $log -Force
.\GPGPC-patcher.ps1 -Dev -MagiskApk "resources\Magisk_30.7.apk"  # Dev version
Stop-Transcript

```

<br/>

> [!IMPORTANT]  
> - Make sure to stop the transcript after the patch completes so the log file is saved.
> - Do not include sensitive information (usernames, passwords, private keys, API keys, etc.) when sharing logs.

<br/>

## Change userdata location

> [!CAUTION]  
> This process will completely **delete** all your user data from Google Play Games on PC.
> - Make sure all games are synced to your Google account, otherwise your game progress will be lost.
> - For all apps, use [Android-DataBackup](https://github.com/XayahSuSuSu/Android-DataBackup?tab=readme-ov-file) or similar tools to create complete app-data backups, then save the backup files to an external drive.

<br/>

1. Exit Google Play Games on PC completely (see "[Exit Google Play Games on PC](#exit-google-play-games-on-pc)" section above).
2. Identify the GPGPC data location:

   - Prod: `%LocalAppData%\Google\Play Games`
   - Dev: `%LocalAppData%\Google\Play Games Developer Emulator`
3. In the data folder above, locate the **userdata** folder (named like `userdata_xxxxxxxx.xxx`, often hundreds of GB).
4. Delete the **userdata** folder from the GPGPC data location.  
   ⚠️ This is destructive; ensure backups are complete.
5. Move the GPGPC data folder (Prod or Dev) to the target drive.

   Example using PowerShell (adjust names/paths):
   ```powershell
   # Move the entire "Play Games" folder
   Move-Item -LiteralPath "$env:LocalAppData\Google\Play Games" -Destination "D:\PlayGamesData"

   # For dev version
   Move-Item -LiteralPath "$env:LocalAppData\Google\Play Games Developer Emulator" -Destination "D:\PlayGamesDataDev"
   ```

6. Create a directory symbolic link from the original location to the new location.

   Open Command Prompt as administrator and run (adjust names as needed):
   ```shell
   mklink /d "%LocalAppData%\Google\Play Games" "D:\PlayGamesData"

   # For dev version
   mklink /d "%LocalAppData%\Google\Play Games Developer Emulator" "D:\PlayGamesDataDev"
   ```
7. Open Google Play Games on PC.  
   The app will follow the symbolic link and use the data on the target drive.

<br/>

> [!NOTE]  
> The target drive must have at least 100 GB of free space to store game data.

<br/>

## Userdata wiped or reset

If your Google Play Games on PC userdata was completely wiped or reset, try the steps below and proceed carefully:

1. Exit Google Play Games on PC completely (see "[Exit Google Play Games on PC](#exit-google-play-games-on-pc)" section above).
2. Remove the `GPGPC_Disable_HPE` module if installed.  

   Uninstall it from Magisk app and [restart the Google Play Games on PC](#restart-google-play-games-on-pc).
3. Move the GPGPC data folder to another drive (example: `D:\PlayGamesDataDev`).  

   See the "[Change userdata location](#change-userdata-location)" section above for examples.
4. Make sure the target drive has at least 100 GB of available storage for game data.

<br/>

---

<br/>

Source:
- [Google Play Games on PC Help Center](https://support.google.com/googleplay/topic/13019131)
  - [11359390](https://support.google.com/googleplaygamesonpc/answer/11359390)
  - [11507425](https://support.google.com/googleplaygamesonpc/answer/11507425)
- [r/EmulationOnPC #mn5yejh](https://www.reddit.com/r/EmulationOnPC/comments/zi3x02/comment/mn5yejh/)
