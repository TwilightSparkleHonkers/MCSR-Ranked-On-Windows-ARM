# Video: https://youtu.be/tkizM-59HXo

# MCSR-Ranked-On-Windows-ARM
Play MCSR Ranked on a Snapdragon X1 or X2 laptop, with no lag, using prism launcher. **These instructions are specific to Windows ARM-based devices. If your device has an Intel or AMD processor, it is NOT ARM-based and you do NOT need to follow these steps.** If you are running Mac or Linux, the first couple steps may still be relevant. If you are running ChromeOS or Android, try your luck with Zalith launcher.

Note: I am writing this guide using a Lenovo Slim 5x with Snapdragon X2.

# DISCLAIMER: I AM NOT A GRAPICHS PIPELINE DEVELOPER/EXPERT. THE TECHNICAL EXPLANATIONS GIVEN HERE MAY LACK DETAIL/BE PLAINLY WRONG. PLEASE TAKE THEM WITH A PINCH OF SALT. IF YOU UNDERSTAND THIS BETTER THAN ME, PLEASE CONTACT ME ON DISCORD @TWINKLESPRINKLE

# 0 - What is an arm/Snapdragon?
ARM is a processor architecture. While ARM is historically the architecture used in mobile devices, it is starting to become more popular for computers, as ARM processors are typically more power-efficient than the traditional x86-64 processors made by Intel and Amd. The Snapdragon X1 and X2 are a new-ish series of ARM processors developed specifically by Qualcomm for Windows Laptops. (Note: Apple computers have been using ARM since the first M1 macbook).

While these new Snapdragon chips work great for most use cases, they come with some challenges when it comes to compatibility, especially with older programs. Modern versions of minecraft run just fine for example, however 1.16.1 requires some tinkering.

# 1 - Let's play MCSR Ranked!
## 1. Install Prism launcher
Find it on the official website: https://prismlauncher.org/download/windows/  
**Be sure to install the ARM64 version, and not the x86 one!!**

## 2. Create an MCSR Ranked instance
The steps are explained on the official website: https://mcsrranked.com/download  
Simply copy the link to the modpack, then in Prism select "Add Instance" -> "Import", and paste the link.

## 3. Download Java version
MCSR Ranked is an old version of minecraft (1.16.1), which defaults to the very old Java 8, not available on ARM. You need to manually select a modern version of Java that works with ARM.  
Go to your instance settings -> Java. Check "Java Installation", then Click "Open Java Downloader". At the bottom, uncheck "Recommended" to show other versions. I have selected Java 21 from Mojang (java-runtime-delta at the time of writing this guide).  
Once you have downloaded it, click "Detect" and select it. Finally, check "Skip Java compatibility checks", otherwise the game will not launch and complain that it wants Java 8.

## 4. Select JWJGL Version 3.3.3
At this point the game still does not launch. You will get an error in the logs, something like `Failed to locate library: lwjgl.dll`. Again, this old minecraft version defaults to LWJGL 3.2.2, which is not available for arm. You need to select version 3.3.3, the latest version that will work for MCSR.  
Go to your instance, select "Version". You should see LWJGL, Minecraft, Fabric and Intermediary Mappings. Select LWJGL, then "Change Version" on the right side, and double click 3.3.3. Hit "Download All", bottom right.

## 5. Launch the instance
At this point the game may start. You may be prompted to update the MCSR Ranked mod, which you should do. Once it is up to date, re-open the instance and start a creative world. **If the game runs smoothly, you're done! Update your settings and queue up :)**  
However, if your game is running at 4 fps - like me, or worse, the game is still not launching, then keep reading...

## 6. Why is it lagging, and what's mesa zink
This paragraph simply explains the issue if you're curious. If you just want the steps to fix it, then skip to the next paragraph.
Older versions of Minecraft Java Edition famously uses the very outdated OpenGL graphics API. As this API is old, it is not natively supported by the graphics drivers of some newer GPUs, such as the Qualcomm Adreno GPUs shipped with Snapdragon X devices. Instead, running OpenGL apps on these GPUs involves a translation layer, which translates OpenGL API calls to another, natively supported graphics API such as Vulkan or DirectX (A.K.A. Dx12, or D3D12). By default, the translation layer on my laptop was Mesa D3D12. However something is horribly wrong somewhere in this pipeline (probably the translation layer itself?), and that causes older miencraft versions to "run" with impressively bad performance.

The proposed workaround here is to use Mesa Zink instead, which converts OpenGL API calls to Vulkan API calls, entirely bypassing whatever demon haunts this OpenGL-D3D12 pipeline.

## 7. Let's download Mesa Zink
You can find it on ~~the official we..~~ this github page https://github.com/mmozeiko/build-mesa/releases  
(The official Mesa website/github does not provide builds for windows arm64 afaict.)  
Select the latest release (26.2.2 at the time of writing), and make sure to download the **zink** archive for **arm64**! In my case, the file name is `mesa-zink-arm64-26.2.2.7z`. You will need to click the little "Show all 20 assets" at the bottom to have it show up. It is very important that you select the correct one, that is **zink and arm64**!!

## 8. Let's patch our java installation to use it
Unzip the 7z archive into a directory. Inside, you will find a couple libraries and dll files:
```
-a----        03/09/2026     05:36       17164288 libEGL.dll
-a----        03/09/2026     05:36          11676 libEGL.lib
-a----        03/09/2026     05:36         119808 libGLESv1_CM.dll
-a----        03/09/2026     05:36          29768 libGLESv1_CM.lib
-a----        03/09/2026     05:36         145408 libGLESv2.dll
-a----        03/09/2026     05:36          78598 libGLESv2.lib
-a----        03/09/2026     05:36       17138176 opengl32.dll
```
Find the location where your java is installed (javaw.exe). You can find it in your prism isntance settings, under java. Mine looks like this:
`C:/Users/twi/AppData/Roaming/PrismLauncher/java/java-runtime-delta/bin/javaw.exe -- YOURS WILL BE DIFFERENT!`  
Open this directory in the file explorer and simply paste all the dll/lib files in there, alongside your java install.

> NOTE: This might be a bad way of doing this. If you're a developer and find this truly awful, please yell at me on discord @twinklesprinkle

~~## 9. For MCSR Ranked, add the following JVM argument
In your instance settings, check "Java Arguments" and add this: `-Djava.awt.headless=true`  
This argument was suggested to me by Ranked developer Vibzz on the official MCSR Ranked Discord. I have no technical explanation for this step, except that it is a known bug of the MCSR Ranked mod. It may be fixed in a future release and this may not be needed in the future. Thank you Vibzz!!~~
EDIT: This JVM argument is no longer necessary in the newest version of the MCSR Ranked mod.

## 10. Change your settings and queue up!
If you've done everything correctly, you should see 'zink' somewhere in the f3 menu on the right side (adjust GUI scale to see every line fully). You should not see any mention of D3D12 anymore. Most importantly, you should notice better performance :)

> NOTE: If you have a better workaround, PLEASE let me know. While this is playable, I am getting only getting ~50-60 fps. Still far below what I'd expect from a modern mid-range laptop, and half of what I get in modern versions of minecraft, even without this workaround.
