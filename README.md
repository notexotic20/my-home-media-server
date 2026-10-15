# my-home-media-server
The idea is to use an old Chromebook to build a media server that I, or whoever I grant access to, can use wherever they wish.

## Phase 1: Media Preparation & Filesystem Setup

Formatting external SSD -
To start, I formatted an external SSD to exFAT and named it MusicServer using the Windows Partition Tool. 
exFAT ensures cross-compatibility between Linux and Windows. 

fre:ac - Installation -
I downloaded a program called fre:ac to convert FLAC files to Opus, which will help me save space throughout this project. 
fre:ac can be downloaded here: https://www.freac.org/downloads-mainmenu-33 by clicking the link associated with your operating system. 

fre:ac - Application FLAC --> Opus -
When opening fre:ac, I first had to get it to recognize all my FLAC files, which I did by selecting File/Add/Audio Files/By Pattern, with the pattern set to *.FLAC and searching subfolders.
In General-Settings/Encorders I selected the Opus Audio Encoder with a variable bitrate, which will convert my FLAC files to Opus while retaining their quality. 
In General-Settings/Output-Files, I changed the file pattern name to <directory>\<filename>. This preserves the original directory structure when transferring files. 
Now I start the encoding process, converting the FLACS into Opus.

MP3 transfer -
Using robocopy, I was able to transfer all MP3s to the external SSD while keeping the current formatting and merging with the now-converted FLAC files 
Such command:  robocopy "directory_from" "directory_to" *.mp3 /S /XF *.flac /NDL /NFL /IPG:5

### Phase 2: Operating System Selection & Rationale

Ubuntu Server bootable -
I started by getting my Linux distro; in my case, I am using Ubuntu.
This can be downloaded here: https://ubuntu.com/download/server.
Next, I used Rufus to create a bootable thumb drive from the ISO I downloaded. 1 change I made was setting the partition scheme to GPT. 

Enable Developer Mode -
To trigger Developer Mode and install Ubuntu on the Chromebook, I had to power cycle, hold ESC + Refresh, and release on the next power-on.
This will boot me into the recovery screen, where I then press Ctrl+D and Enter.
This process wipes the internal storage and disables Google's operating system checks, which should allow me to install Ubuntu. 

Difficulties -
The Chromebook I am using for this project has a broken screen and doesn't display. I am using a secondary display, but when going through the recovery screen and trying to install Ubuntu, that screen is useless, as it either won't receive power or won't display. 
During the intended process to wipe ChromeOS and install Ubuntu, ChromeOS wasn't wiped, which prevented me from installing Ubuntu. 

Flash Script -
Logging in as a guest, I hit Ctrl + Alt + T, which opens crosh; typing shell into crosh gives me the capability to run commands. 
Running this command: cd; curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
This command will let me replace the old firmware with a new one that supports installing Ubuntu. 

Difficulties -
Currently, on the Chromebook, write protection is enabled, which prevents me from flashing the firmware or installing a new one. 
From what I found, on older Chromebooks like mine, if you boot without a battery and use only a wall charger, it should disable write protection. 
Another way this could be enabled/disabled is with a Physical Write-Protect Screw, but I am going to test the disconnected battery first. 
