# Macintosh MAC spoof
Your MAC address identifies your device when connecting to a network. This address provides some information about your device and may be used for tracking purposes. 

This repo is an instruction of how to set up an app that "spoofs" (changes to an unidentified random) your MAC address on MacOS devices (tested on Sonoma 14.0).

*Use at your own risk*
## Creating the app 
1. Open Automator[^1].
2. Select **Application** for document type.
3. In the actions search-bar[^2], search for **Run AppleScript**.
4. Drag the **Run AppleScript** action onto the workflow window[^3].
5. Replace the content in the **Run AppleScript** action with the following code:
```zsh
on run {input, parameters}
	do shell script "/System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport -z" with administrator privileges
	do shell script "ifconfig en1 ether a0$(openssl rand -hex 5 | sed 's/\\(..\\)/:\\1/g')" with administrator privileges
	do shell script "networksetup -detectnewhardware"
	return input
end run
```
6. Click the hammer icon, the code should change colour.
7. Finally, save your application[^4]: select **Application** as the file type, place it in the **Application** folder, and give it an appropriate name.
8. You can close Automator.

Now, whenever you want to spoof your MAC address, just run[^5] this application you made.

*NOTE: Running this application will ask you to enter your laptop password, as the script has to be executed with administrator privileges to work.*

## Setup Auto-Run
Your MAC address resets to its Network Interface Controller default value[^6] every time you reboot your system. In order to automatically spoof it on log in, follow these instructions:

1. Go to System Preferences > General > Login Items
2. The **Open at Login** table will show you a list of applications that run when you log in.
3. Click the plus sign in the bottom-left corner of the table.
4. Look for the application you just created and select it.

That's it!

---
#### Issue
This application disconnects you from any WiFi connection you may have, and you need to connect back to it manually after it has run. If you would like the app to connect back to a particular WiFi network (say, your home one) after running, follow these steps:
1. Open Automator[^1].
2. Click on **Open an Existing Document...** & choose your application.
3. Edit the code of the **Run AppleScript** action, adding the following line above ```return input```:
```zsh
do shell script "networksetup -setairportnetwork en1 YOUR_WIFI_NAME YOUR_WIFI_PASSWORD"
```
4. Click the hammer icon, the code should change colour.
5. Finally, save your application[^4].
6. You can close Automator.

*NOTE: This option requires you to write out your WiFi password in the Automator application, which is a security risk & I would not recommend it.*

[^1]: Cmd+space to open Spotlight Search, and then search for Automator
[^2]: This is located in the top-left corner of the editor
[^3]: The area of the editor to the right that says "Drag actions or files here to build your workflow."
[^4]: Press Cmd+s 
[^5]: Either double-click on it in the Application folder from Finder, or search for it in Spotlight Search and hit Enter
[^6]: ...so the one that could be used for tracking
