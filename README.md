# A Guide To Creating Scaled Resolutions in MacOS

**WARNING:** Try this at your own risk. I am not an expert in display hardware and am not responsible for any damage to components or system files. Additionally, this may place a greater burden on your graphics card.

## Background

Despite being an Apple fanatic and avid Mac user, something has always frustrated me about MacOS. Unlike Windows, MacOS is unable to natively create custom scaled resolutions without losing pixel density. However, there is a catch: If Apple deems the display to be “Retina” then you get a different resolution selector with options for “More Space” and “Less Space” that is effectively scaling the display without losing pixel density.

This can be frustrating when MacOS arbitrarily decides that a high-resolution monitor is not classified as “Retina” and cannot therefore have scaled resolutions. I have a 25” Acer G257HU monitor with a resolution 2560x1440. Running at 1440p, items along the menu bar appear too small and are hardly readable from a significant distance. However, since it is not deemed a “Retina” monitor the only true resolution-preserving options that appear are 2x, 3x, etc. zoom. This would leave me with a scaled resolution of 720p which is too large. What I want is a scaled resolution of around 125% that would give me the scale of 1080p.

![Image of Retina Display Comparison](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/retina_compare.png)

I knew this had to be possible since Apple’s own ancient thunderbolt display can do it. It runs at a resolution of 1440p yet has the Retina scaling options of “More Space” or “Less Space” that allow it to preserve resolution.
Thus began my hunt off-and-on for over a year that resulted in me reading many articles and corrupting many system files in my computer to no avail until I finally figured it out. Because of the time and effort I went through to get this working, I wanted to share the process for any other curious individuals interested in creating scaled resolutions. The steps are actually relatively straightforward although somewhat finnicky.


## Process

### Step 1: Disable SIP

In order to complete this process, you will need to have full Administrative access to your computer. iMore has a [helpful article](https://www.imore.com/how-turn-system-integrity-protection-macos) detailing the steps to disable System Integrity Protection which is necessary to modify system files.

Additionally, if you are running MacOS 10.15 Catalina or newer, system files are not modifiable by the user for safety. To bypass this, open terminal and type the following commands:

`sudo mount -uw /`
	
`killall Finder`

![Image of Terminal Commands](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/terminal.png)

Now the system hard drive partition should be modifiable by the user.


### Step 2: Download RDM

Sometimes System Preferences does not show all of the resolutions possible with your monitor. To bypass this, there are several free utilities that can be used. One I have found to work well is [RDM](https://github.com/avibrazil/RDM).

Once you have downloaded and installed RDM, it will run as a menu bar application. It allows you to see all the options for your screen resolution.

Here’s what RDM looks like on my system running a 1440p monitor with some custom HiDPI resolutions I’ve created:

![Image of RDM](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/RDM.png)


### Step 3: Enable HiDPI

In order for your Mac to support scaling, it must have the option for HiDPI (a.k.a. scaled) resolutions turned on. Most newer Macs will have it turned on as default.

If HiDPI is enabled you will see lightning bolt emojis next to resolutions in RDM as can be seen in my screenshot from above. This means that these resolutions are full-quality scaled Hi-DPI resolutions.

Another option to check if HiDPI is enabled is under Displays in System Preferences. Under the Display tab, hold the Option key while selecting Scaled. It will show several resolution options that MacOS suggests for your monitor. If HiDPI is enabled on your system, you will likely see a resolution that is ½ the size of your monitor’s native resolution that has “(HiDPI)” next to the resolution. 

![Image of Display Options in System Preferences](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/sys_pref_display.png)

The largest HiDPI option for a 1440p monitor will likely be 720p which is a 2x scale. In order to add different resolutions, we will need to modify the file that contains these values.

If HiDPI is not enabled, you can run:

`sudo defaults write /Library/Preferences/com.apple.windowserver.plist DisplayResolutionEnabled -bool true`

Restart the system to enable.


### Step 4: Locate the Display File

Under Displays in System Preferences, select the tab Color. Select the default color profile for your computer (usually called “Color LCD” or the name of your monitor) and click Open Profile.

A ColorSync Utility window opens with the selected profile. Scroll to the last line in the file which should have a description of “Apple display make and model information.”

The pieces of information we want from this screen are the Manufacturer and Model tags. This will allow us to find in the system what file is being used to supply the resolutions.

![Image of Display Color Profile](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/color_profile.png)
 
Record the non-zero values in the Manufacturer and Model fields. For example, my Acer monitor with my Hackintosh is 472 and 416 or my monitor with my MacBook Pro is 620 and A022.

Navigate to `System/Library/Displays/Contents/Resources/Overrides/`. Then navigate to the folder that is DisplayVendorID- with the Manufacturer number of your color profile on the end. Inside this folder should be a file that is DisplayProductID- with the Model number of your color profile on the end. This file will contain the resolutions that your computer can display.

![Image of Display File Location](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/file_location.png)
 
However, if your monitor is a cheaper, less-popular model like mine it is likely that a file with the specified name does not exist. In this case, you will have to create the file. Thankfully, someone has made a website for this.


### Step 5: Generate Resolutions

Here’s the most important part that was hindering me for a long time because I couldn’t seem to find it posted anywhere. **You need to create BOTH the scaled HiDPI resolution that you want and DOUBLE that resolution.**

For instance, I wanted my 1440p monitor to scale to 1080p so I needed to create entries for 1920x1080p HiDPI as well as for 3840x2160p HiDPI. Without the 2160p entry, my monitor would not accept 1080p as a HiDPI option. This had me stuck for an obnoxiously long time. Weird. I suspect this tricks MacOS into believing it is a true 2x scaling and thus is allowed instead of a weird scaling value.

So let’s create the resolutions. Head to https://comsysto.github.io/Display-Override-PropertyList-File-Parser-and-Generator-with-HiDPI-Support-For-Scaled-Resolutions/.

Type the color profile model that we found earlier under the DisplayProductID field and the color profile manufacturer under the DisplayVendorID field. Next add desired resolutions. Make sure all resolutions have HiDPI selected.

Here’s what I generated for my system based on my monitor and the resolutions I desired:

![Image of Generator Website](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/generator.png)

 
If your system already has the file for your display, I recommend creating only the new resolutions you want and copying those lines into the existing file. Otherwise, click the button in the bottom right to download the file. Save it somewhere memorable. Do not change the name, as the generator creates the correct name for your monitor.


### Step 6: Change Display File

**If the file already exists:**

Before you change the display file, make sure to create a copy. Create a copy on your desktop of the original file in case something goes wrong and you needed to reset to the original. Duplicate this newly created copy. This will be the file to change.

Open the file with a text editor and locate the section titled `<key>scale-resolutions</key>`. Under this is the section `<array></array>` which holds all of the values for the resolutions that appear in the scaled resolutions options in System Preferences. Between the array tags copy the resolutions from the website. There should already be some resolutions there.
This section of the file should look similar to the below image although you may not have as many resolutions listed.

![Image of Resolutions plist](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/res_plist.png)

 	
**If the file does not already exist:**

Copy the downloaded file into the system folder we found in Step 4 that should contain the file. If the information is put into the website correctly, the downloaded file will be readable by the computer and no editing is necessary.


### Step 7: Reboot
Reboot the machine for the changes to take effect. If the file’s name and location are correct for your system, then MacOS should automatically recognize the file and see the new resolutions. Open RDM and select the resolution you wish to use. And voila! Now you can use scaled resolutions.

After a reboot the system files are no longer modifiable. You will need to run the terminal command from Step 1 after each reboot if you would like to modify the file. System Integrity Protection (SIP) will likely stay disabled until the next MacOS update modifies the recovery partition. If you would like to reenable SIP, the link from Step 1 details the process to reenable SIP.

## Final Thoughts

Feel free to contact me if you find this helpful or have more questions. I’d love to hear your feedback!

Email: 

![Image of email](https://github.com/bbhardin/A-Guide-to-MacOS-Scaled-Resolutions/blob/master/Images/email.png)

[Twitter](https://twitter.com/Ben_Hardin1)

[Instagram](https://www.instagram.com/ben__hardin/)

**One huge shout-out to this article:**

https://comsystoreply.de/blog-post/force-hidpi-resolutions-for-dell-u2515h-monitor

This developer created the tool to generate the scaled resolutions. Their knowledge helped me immensely when getting this working.
