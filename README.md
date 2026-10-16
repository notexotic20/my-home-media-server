# My-Home-Media-Server
The idea is to use an old Chromebook to build a media server that I, or whoever I grant access to, can use wherever they wish.

### Tools Used:
I have a Samsung Chromebook 3 (XE500C13) that I will be using. This Chromebook has a broken physical display but has working hardware. \
I have a PNY USB thumb drive that I use to make an Ubuntu Server bootable. \
I have an external SSD that will hold all physical media. \
I will be using Navidrome to manage my media, and Tailscale to set up a way for people to access this server from anywhere. 


## Phase 1: Media Preparation & Filesystem Setup

### Formatting external SSD:
To start, I formatted an external SSD to exFAT and named it MusicServer using the Windows Partition Tool.\
exFAT ensures cross-compatibility between Linux and Windows. 

### fre:ac - Installation:
I downloaded a program called fre:ac to convert FLAC files to Opus, which will help me save space throughout this project.\
fre:ac can be downloaded here: https://www.freac.org/downloads-mainmenu-33 by clicking the link associated with your operating system. 

### fre:ac - Application FLAC --> Opus:
When opening fre:ac, I first had to get it to recognize all my FLAC files, which I did by selecting File/Add/Audio Files/By Pattern, with the pattern set to *.FLAC and searching subfolders. \
In General-Settings/Encorders I selected the Opus Audio Encoder with a variable bitrate, which will convert my FLAC files to Opus while retaining their quality.\
In General-Settings/Output-Files, I changed the file pattern name to <directory>\<filename>. This preserves the original directory structure when transferring files.\
Now I'm able to start encoding, converting FLACS to Opus.

### MP3 transfer:
Using robocopy, I was able to transfer all MP3s to the external SSD while preserving the current formatting and merging them with the newly converted FLAC files.\
Such command:  robocopy "directory_from" "directory_to" *.mp3 /S /XF *.flac /NDL /NFL /IPG:5.

## Phase 2: Operating System Selection & Rationale

### Ubuntu Server bootable:
I started by getting my Linux distro; in my case, I am using Ubuntu. It can be downloaded here: https://ubuntu.com/download/server. \
Next, I used Rufus to create a bootable thumb drive from the ISO I downloaded. One change I made was setting the partition scheme to GPT.

### Enable Developer Mode:
To trigger Developer Mode and install Ubuntu on the Chromebook, I had to power cycle, hold ESC + Refresh, and release on the next power-on. \
This will boot me into the recovery screen, where I then press Ctrl+D and Enter. \
This process wipes the internal storage and disables Google's operating system checks, which should allow me to install Ubuntu. 

### Difficulties: 
As the Chromebook I am using has a broken display, I am using a secondary display. During installation, both screens are useless: one is broken, and the other doesn't receive a display. \
During the intended process to wipe ChromeOS and install Ubuntu, ChromeOS wasn't wiped, which prevented me from installing Ubuntu. 

### Flash Script 1: 
Logging in as a guest, I hit Ctrl + Alt + T, which opens crosh; typing shell into crosh gives me the capability to run commands. \
Running this command: **cd; curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh**. \
This command will let me replace the old firmware with a new one that supports installing Ubuntu. 

### Difficulties:
Currently, on the Chromebook, write protection is enabled, preventing me from flashing and installing new firmware. \
From what I found, on older Chromebooks like mine, if you boot without a battery and use only a wall charger, it should disable write protection. \
Another way this could be enabled/disabled is with a Physical Write-Protect Screw, but I am going to test the disconnected battery first. 

### Disconnecting the battery:
I start by unscrewing all the screws on the back of the Chromebook, then remove the backboard to access the battery. \
To disconnect the battery, I just need to unplug the main power harness that connects the battery to the motherboard. \
Next, I plugged the Chromebook into the wall and repeated my Flash Script steps, hoping the write protection is now off. \
Rerunning the Flash Script steps, I can see that write protection is still on, forcing me to remove the Write-Protect Screw

### Removal of the Write-Protect Screw:
For my Chromebook model, the write-protect screw is on the bottom of the motherboard, so I have to do a teardown to access it. \
To start, I removed the battery for more room. As of now, the battery is held down by just screws. \  
Next, I unplugged all cables connected to the motherboard, then unscrewed it to access the other side. \
The Write-Protect Screw is identified with a small arrow pointing at it. Removing it and the surrounding film lets me run the Flash Script as needed. \
Next, I reassemble the Chromebook for use. 

### Flash Script 2:
Now, gaining the ability to run the command: **cd; curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh**, I run through the prompts as follows. \
I will be running option 2: Install/Update UEFI (Full ROM) Firmware. \
I confirm everything and deny the creation of a backup. \
After the firmware downloads and installs, it confirms it was successful and takes me to the main menu, where I press R to restart. 

## Phase 3: Ubuntu Install

### Ubuntu Server Install and Configuration 1:
Once the Chromebook starts restarting, I plug in the Ubuntu Server bootable I made earlier and wait for my external display to receive a signal, as that will indicate the Ubuntu Server bootable has worked and is now running. 

### Difficulties:
I couldn't tell if the installation was properly completed, as I found that the Ubuntu Server installer doesn't support dual monitors. \
To fix this, I disconnected the connector for the broken Chromebook display, forcing the Ubuntu Server installer to display on my external monitor. 

### Ubuntu Install and Configuration 2:
After booting into the UEFI, we can select our bootable USB drive and start installing Ubuntu. \
I selected the English version and the base Ubuntu Server install for this project. \
Within Network configuration, I selected w1p2s0, which gives me the option to edit the wifi. \
Within that option, I entered my network name and its password, successfully connecting to our wifi. \
For this project, the proxy address field can be left blank. \
For storage configuration, I used the entire local disk, which is 15 GB. I left LVM unchecked as it is unnecessary for this project. \
For profile configuration, I entered my desired name and password in the given fields. I labeled the server music_server. \
I skipped upgrading to Ubuntu Pro as it is unnecessary for this project. \
I checked the box to install the OpenSSH server, as this will let me fully control the server from another device later on. \
I skipped installing server snaps because one of this project's goals is to become more comfortable working with Linux and networking. \
Now that the installation is complete, I am prompted to reboot to continue and to remove the installation media, which is the bootable USB drive. 

### Difficulties:
When going to log in, it appears that my username and password do not work. To fix this, I used my bootable USB drive to access the Emergency Shell. \
In the shell, I ran these commands. **sudo mkdir /mnt/system**; **sudo mount /dev/mmcblk0p2 /mnt/system**; **sudo chroot /mnt/system**. \ 
These commands took me to my Chromebook's server filesystem, where I could find my username with the command ls /home. \
Then, using that username, I could run the **passwd** command to reset my password; it looks like this. **passwd username** \
Afterward, I am prompted to enter and confirm a password. Upon completion, it says the password was updated successfully. \ 
Then I go back to the login page, where I find I still cannot log in. \
After looking around for a solution, I believe the problem stems from a keyboard layout difference, so I will try resetting the password again to a more universal one. \
Now, with a more universal password, I can log in and start working. This universal password will be replaced with a stronger one to improve login security. 

## Phase 4: Setting up SSH

### First steps:
Using the command **sudo apt update && sudo apt upgrade -y**, I can fully refresh my package database and install newer versions of the software I have. \
Using the command **sudo apt autoremove**, I can remove older packages that I no longer need. \
Using the command **ls -l /var/run/reboot-required**, I can determine whether I need to reboot the server; if the file exists, I have to reboot. In this case, I do not.

### Server side:
I first make sure the SSH service is running. This can be done through the following commands: **sudo systemctl enable ssh**, **sudo systemctl start ssh**. \
Next, I have to get my Server's Network IP address, and this can be done through the command **ip a** 

### Home side:
Now, on my main PC, I can open CMD and run the following example command: **ssh username@thegivenipaddress**. \
I am then prompted with Are you sure? I say yes, type the server's login, and then I have successfully set up SSH. \
I proceed to change the password to ensure a more secure login, since I should no longer encounter any keyboard layout differences, as I will be using my main pc to log in.


### Convenience:
As of right now, the Chromebook is sitting on a small desk with me and all my stuff. \
For convenience purposes, I will configure the Chromebook so that I can shut the lid and it'll stay on, letting me move it to a more permanent position. \
I can do this since I no longer need physical access to the Chromebook via SSH, and it'll free up space on my desk. \
Before I do any configuration or moving, I will plug the external SSD that carries all the media/songs for this project into the side of the Chromebook. \
Now, using the command: **sudo nano /etc/systemd/logind.conf**, I get a screen full of configuration text where I look for #HandleLidSwitch=suspend \
Changing that line of text to HandleLidSwitch=ignore will fix my problem with shutting off when the lid is closed. \
Changing HandleLidSwitchExternalPower from suspend to ignore will let the Chromebook run on battery for a while without going to sleep. \
To save the text editor, press Ctrl+O followed by Enter; Ctrl+X will exit. \
To make these changes without restarting, I have to restart the Power Service: **sudo systemctl restart systemd-logind**.






































 

