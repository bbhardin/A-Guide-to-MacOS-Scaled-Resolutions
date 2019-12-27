# A Guide To Creating Scaled Resolutions in MacOS

**WARNING:** Try this at your own risk. I don’t know the hardware of retina displays to determine if they have different components to make them better at handling scaled resolutions. I am not responsible for damage that could be done to any components.

## Background
I am an Apple fanatic and avid Mac user, but something has always frustrated me about MacOS. Unlike Windows, MacOS is unable to natively create custom scaled resolutions without losing pixel density. However, there is a catch: If Apple deems the display to be “Retina” then you get a different resolution selector with options for “More Space” and “Less Space” that is effectively scaling the display without losing pixel density.

This can be frustrating when MacOS decides that a high-resolution monitor is not classified as “Retina” and cannot therefore have scaled resolutions. I personally experienced this. I have a 25” Acer G257HU monitor with a resolution 2560x1440p. Running at 1440p, no text items along menu bars appear too small and are hardly readable from a significant distance. However, since it is not deemed a “Retina” monitor the only true resolution-preserving options that appear are 2x, 3x, etc. zoom. This would leave me with a scaled resolution of 720p which is much too large. What I wanted was a nice scaled resolution of around 125% that would give me the scale of 1080p.

I knew this had to be possible since Apple’s own ancient thunderbolt display can do it. It also runs at a resolution of 1440p yet has the Retina scaling options of “More Space” or “Less Space” that allow it to preserve resolution.
Thus began my hunt off-and-on for over a year that resulted in me reading many articles and corrupting many system files in my computer to no avail until I finally figured it out. Because of the time and effort I went through to get this working, I wanted to share the steps to do so for any other curious individuals out there. The steps are actually relatively simple although somewhat finnicky.

## Process
### Step 1: Disable SIP

In order to complete this process, you will need to have full Administrative access to the computer you are using. Follow this link https://www.imore.com/how-turn-system-integrity-protection-macos to disable System Integrity Protection as the first step to modify system files.

Additionally, if you are running MacOS 10.15 Catalina or newer, system files are not modifiable by the user for safety. To bypass this, open terminal and type the following commands:

`sudo mount -uw /`
	
`killall Finder`

Now the system hard drive partition should be modifiable by the user.

### Step 2: Download RDM

If you do not yet have a monitor other than your retina monitor connected to your Mac, selecting scaled resolution in System Preferences will only show the options for “More Space” or “Less Space.”

To bypass this and actually show all options for screen resolution, there are several free utilities that can be used to change your resolution. A free utility I have found to work well is RDM.

Once you have downloaded and installed RDM, it will run as a menu bar application. It will allow you to see all the options for your screen resolution.

Here’s what RDM looks like on my system running a 1440p monitor with some custom HiDPI resolutions I’ve created.
 

### Step 3: Enable HiDPI

In order for your mac to support scaling, it must have the option for HiDPI (a.k.a. scaled) resolutions turned on. Most newer Macs will have it turned on as default.

If HiDPI is enabled you will see lightning bolt emojis next to resolutions in RDM as can be seen in my screenshot from above. This means that these resolutions are full-quality scaled Hi-DPI resolutions.

Another option to check if HiDPI is enabled is under Displays in System Preferences. Under the Display tab, hold the Option key while selecting Scaled. It will show several resolution options that MacOS suggests for your monitor. If HiDPI is enabled on your system, you will likely see a resolution that is ½ the size of your monitor’s native resolution that has “(HiDPI)” next to the resolution. 

What you will notice is that the largest HiDPI option for a 1440p monitor will likely be 720p which is a two times scale. In order to add different ones, we will need to modify the file that contains these values.

### Step 4: Locate your display file

Under Displays in System Preferences, select the tab Color. Select the default color profile for your computer (usually called “Color LCD” or the name of your monitor) and click Open Profile.

A ColorSync Utility window opens with the selected profile. Scroll to the last line in the file which should have a description of “Apple display make and model information.”

The important pieces of information we want from this screen are the Manufacturer and Model tags. This will allow us to find in the system what file is being used to supply the resolutions.
 
Record the non-zero values in the Manufacturer and Model fields. For example, my Acer monitor with my Hackintosh is 472 and 416 or my monitor with my MacBook Pro is 620 and A022.

Navigate to the highest folder in your hard drive and then to System > Library > Displays > Contents > Resources > Overrides. Then navigate to the folder that is DisplayVendorID- with the Manufacturer number of your color profile on the end. Inside this folder should be a file that is DisplayProductID- with the Model number of your color profile on the end. This file will contain the resolutions that your computer can support for monitors.
 
However, if your monitor is like mine and is a cheaper, less-popular model it is likely that a file with the specified name does not exist. In this case, you will have to create the file. Thankfully, someone has created a website for this.

### Step 5: That website for resolutions

Here’s the key that was hindering me for the longest time because I couldn’t seem to find it posted anywhere. You need to create BOTH the scaled HiDPI resolution that you want and DOUBLE that resolution. For instance, I wanted my 1440p monitor to scale to 1080p so I needed to create entries for 1920x1080p HiDPI as well as 3840x2160p HiDPI. Without the 2160p entry, my monitor would not accept the 1080p as a HiDPI option. This had me stuck for an obnoxiously long time. Weird. I suspect this tricks MacOS into believe it is a true 2x scaling and thus is allowed instead of a weird scaling.

So let’s create the resolutions. Head to https://comsysto.github.io/Display-Override-PropertyList-File-Parser-and-Generator-with-HiDPI-Support-For-Scaled-Resolutions/.

Type the color profile model that we found earlier under the DisplayProductID field and the color profile manufacturer under the DisplayVendorID field. Next add desired resolutions. Make sure all resolutions have HiDPI selected.

Here’s what I generated for my system based on my monitor and the resolutions I desired:
 
If your system already has the file for your display, I would recommend creating just the new resolutions you want and copying those lines into the existing file. Otherwise, click the button in the bottom right to download the file. Save it somewhere that you will remember. Do not change the name, as the generator creates the correct name for the device.

### Step 6: Change display file

**If the file already exists:**

Before you change the display file, make sure to create a copy.
Create a copy on your desktop of the original file in case something was to go wrong and you needed to reset to the original.
Duplicate this newly created copy. This will be the file that we change.
Open the file with a text editor and locate the section titled <key>scale-resolutions</key>. Under this is the section <array></array> which holds all of the values for the resolutions that appear in the scaled resolutions options in System Preferences. Between the array tags copy the resolutions from the website. There should already be some resolutions there.
This section of the file should look similar to the below image although you may not have as many resolutions there.
 	
**If the file does not already exist:**

Copy the downloaded file into the system folder we found in Step 4 that should contain the file. If the information is put into the website correctly, the downloaded file will be readable by the computer and no editing is necessary.

### Step 7: Reboot
Reboot the machine for the changes to take effect. If the file’s name and location are correct for your system, then MacOS should automatically recognize the file and see the new resolutions. Open RDM and select the resolution you wish to use. And viola! Now you can create scaled resolutions.

After a reboot the system files are no longer modifiable. You will need to run the terminal command from Step 1 after each reboot if you would like to modify system files. System Integrity Protection (SIP) will likely stay disabled until the next MacOS update modifies the recovery partition. If you would like to reenable SIP, the link from Step 1 details the process to reenable SIP.

Feel free to contact me if you find this helpful or have more questions. I’d love to hear your feedback!

One huge shout-out to this article:
https://comsystoreply.de/blog-post/force-hidpi-resolutions-for-dell-u2515h-monitor
This developer created the tool to create the scaled resolutions and their knowledge helped me immensely when getting this working.
