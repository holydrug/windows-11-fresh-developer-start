## This guide provides a step-by-step approach to enhance your Windows environment 🚀🛠️🖥️🌐💻


### Install Chocolatey:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

### Install WSL (need to update Windows before):
```powershell
wsl --update
```

### Set Explorer Settings (PowerShell Admin):

Deletes previous settings:
```powershell
ri 'HKCU:\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU' -Recurse
ri 'HKCU:\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags' -Recurse
```

Set list view:
```powershell
$FT = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderTypes'
gci $FT -Recurse | ? Property -Contains 'LogicalVIewMode' | %{
    $_.Name -match 'FolderTypes\\(.+)\\TopViews' | out-null
    $matches[1]
} | select -unique | %{
    New-Item "HKLM:\SOFTWARE\Microsoft\Windows\Shell\Bags\AllFolders\Shell\$_" -Force |
    Set-ItemProperty -Name 'Mode' -Value 3 -PassThru |
    Set-ItemProperty -Name 'LogicalViewMode' -Value 4
}
```

Disable grouping folders:
```powershell
$RegExe = "$env:SystemRoot\System32\Reg.exe"
$File = "$env:Temp\Temp.reg"
$Key = 'HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\FolderTypes\{885a186e-a440-4ada-812b-db871b942259}'
& $RegExe Export $Key $File /y
$Data = Get-Content $File
$Data = $Data -Replace 'HKEY_LOCAL_MACHINE', 'HKEY_CURRENT_USER'
$Data = $Data -Replace '"GroupBy"="System.DateModified"', '"GroupBy"=""'
$Data | Out-File $File
& $RegExe Import $File
$Key = 'HKCU\SOFTWARE\Classes\Local Settings\Software\Microsoft\Windows\Shell'
& $RegExe Delete "$Key\BagMRU" /f
& $RegExe Delete "$Key\Bags" /f
Stop-Process -Force -ErrorAction SilentlyContinue -ProcessName Explorer
```

Remove OneDrive via PowerShell:

```powershell
## Kill the OneDrive process
taskkill /f /im OneDrive.exe

# Uninstall OneDrive
# For x64 system (I tested it on my machine)
cmd -c "%SystemRoot%\SysWOW64\OneDriveSetup.exe /uninstall"

# For x86 machines
cmd -c "%SystemRoot%\System32\OneDriveSetup.exe /uninstall"
```

### Install all needed packages (chocolaty + winget)
Chocolaty Packages: ~3GB+

```powershell
choco install `
    qbittorrent `
    clavier-plus `
    temurin17 `
    nodejs `
    python `
    firefox `
    googlechrome `
    git `
    vlc `
    winrar `
    curl `
    k-litecodecpackfull `
    sumatrapdf `
    kubernetes-helm `
    kubernetes-cli `
    vim `
    discord `
    multimc `
    postman `
    yt-dlp `
    dotnet `
    telegram `
    bulk-crap-uninstaller `
    obs-studio `
    steam `
    docker-engine `
    docker-desktop `
    adb `
    gradle `
    maven `
    intellijidea-ultimate `
    wsl-ubuntu-2204 `
    playnite `
    obsidian -y
```

Microsoft Store Programs:
(whatsapp only)
```powershell
winget install -e -i --id=9NKSQGP7F2NH --source=msstore
```
