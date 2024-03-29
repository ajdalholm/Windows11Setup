# Windows11Setup
Quickly get a new windows 11 setup the way I like it.

1. Install git
    ```powershell 
    # Install winget
    $URL = "https://api.github.com/repos/microsoft/winget-cli/releases/latest"
    $URL = (Invoke-WebRequest -Uri $URL).Content | ConvertFrom-Json |
            Select-Object -ExpandProperty "assets" |
            Where-Object "browser_download_url" -Match '.msixbundle' |
            Select-Object -ExpandProperty "browser_download_url"
    $ProgressPreference='SilentlyContinue'
    Invoke-WebRequest -Uri $URL -OutFile "Setup.msix" -UseBasicParsing
    $ProgressPreference='Continue'
    Add-AppxPackage -Path "Setup.msix" -ForceApplicationShutdown #install
    Remove-Item "Setup.msix" -Confirm #remove installer

    #install git
    winget install Git.Git --accept-package-agreements --accept-source-agreements
    #Refresh Path
    Invoke-Command -ScriptBlock {$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User") }
    #clone this repository
    Invoke-Command -ScriptBlock {powershell.exe -Command "& {Push-Location $env:USERPROFILE ; & 'git.exe' clone https://github.com/ajdalholm/Windows11Setup; Pop-Location}"}
    #Prompt for gitconfig configuration
    Invoke-Command -ScriptBlock {powershell.exe -Command "& {Push-Location -Path (Join-Path -Path $env:USERPROFILE -ChildPath Windows11Setup) -ErrorAction Stop; Copy-Item -Path ./Assets/.gitconfig -Destination $env:USERPROFILE; & 'notepad.exe' $env:USERPROFILE\.gitconfig; Pop-Location}"}
    #Set execution policy to RemoteSigned
    Invoke-Command -ScriptBlock {$sh = new-object -com Shell.Application; $sh.ShellExecute('powershell', '-Command "Set-ExecutionPolicy RemoteSigned"', '', 'runas')}

    ```
1. Apps
   ```powershell
   #install apps 
    Push-Location -Path (Join-Path -Path $env:USERPROFILE -ChildPath 'Windows11Setup\assets') -ErrorAction Stop
        & .\AppHandler.ps1
    Pop-Location
   ```
