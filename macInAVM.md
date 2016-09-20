So I initially got stumped when OSX own disk utilities didn’t do the job; still stumped on that one.  So I went old school.  Here is the script to create the iso.  First need to go out to the apple store and download the El Capitan software.  When it tries to initialize after downloading to upgrade or install on your computer, just cancel it.  Now it is in your Applications folder and technically, you can now move the .app any where you want.  If you do, you will have to repath the ‘Mount installer image’.  You will also have to change the command in the last line in the script to put the ISO where you want to put it.

#!/bin/bash  

# Create bootable El Capitan ISO

# Mount the installer image  
hdiutil attach "/Applications/Install OS X El Capitan.app/Contents/SharedSupport/InstallESD.dmg" -noverify -nobrowse -mountpoint /Volumes/esd

# Create empty cdr image  
hdiutil create -o ElCapitan.cdr -size 8000m -layout SPUD -fs HFS+J

# Mount the cdr image  
hdiutil attach ElCapitan.cdr.dmg -noverify -nobrowse -mountpoint /Volumes/iso

# Restore Base System to the cdr image 
asr restore -source /Volumes/esd/BaseSystem.dmg -target /Volumes/iso -noprompt -noverify -erase

# Remove Package link and replace with actual files  
rm /Volumes/OS\ X\ Base\ System/System/Installation/Packages

# Copy Base System  
cp -rp /Volumes/esd/Packages /Volumes/OS\ X\ Base\ System/System/Installation
cp -rp /Volumes/esd/BaseSystem.chunklist /Volumes/OS\ X\ Base\ System/
cp -rp /Volumes/esd/BaseSystem.dmg /Volumes/OS\ X\ Base\ System/

# Unmount the installer image  
hdiutil detach /Volumes/esd

# Unmount the cdr image  
hdiutil detach /Volumes/OS\ X\ Base\ System

# Convert the cdr to ISO/CD master 
hdiutil convert ElCapitan.cdr.dmg -format UDTO -o ElCapitan.iso

# Rename the ISO and move it to the desktop  
mv ElCapitan.iso.cdr /Users/$USER/Desktop/ElCapitan.iso



Now Create a new OS X El Capitan from Wizard.
Change the ‘System -> chipset” to PIIX3, then mount the created ISO and boot up.
Note: If you only see the CD/DVD as the installation target within the installation program, choose from the upper menu “Utilities > Disk Utility” and erase the Virtual Box disk.  This will give you an empty HFS+ Journaled disk.  Quit the Disk Utility and go back to the installation screen where you will now see the disk as an option for a target.  Proceed with normal installation of the OS.

Thomas Burnes
Senior Systems Engineer
12101 Airport Way, Suite 100
Broomfield, CO 80021
Cell: 720-347-9682
