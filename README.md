# MCSR-Ranked-On-Windows-ARM
Play MCSR Ranked on a Snapdragon X1 or X2 laptop, with no lag, using prism launcher. **These instructions are specific to Windows ARM-based devices. If your device has an Intel or AMD processor, it is NOT ARM-based and you do NOT need to follow these steps.** If you are running Mac or Linux, the first couple steps may still be relevant. If you are running ChromeOS or Android, try your luck with Zalith launcher.

Note: I am writing this guide using a Lenovo Slim 5x with Snapdragon X2.

# 0 - What is an arm/Snapdragon?
ARM is a processor architecture. While ARM is historically the architecture used in mobile devices, it is starting to become more popular for computers, as ARM processors are typically more power-efficient than the traditional x86-64 processors made by Intel and Amd. The Snapdragon X1 and X2 are a new-ish series of ARM processors developped specifically by Qualcomm for Windows Laptops. (Note: Apple computers have been using ARM since the first M1 macbook).

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

## 6. Why is it lagging, and what's mesa/zink
This paragraph simply explains the issue if you're curious. If you just want the steps to fix it, then skip to the next paragraph.
Older versions of Minecraft Java Edition famously uses the very outdated OpenGL graphics API. As this API is old, it is not natively supported by the graphics drivers of some newer GPUs, such as the Qualcomm Adreno GPUs shipped with Snapdragon X devices. Instead, running OpenGL apps on these GPUs involves a translation layer, which translates OpenGL API calls to another, natively supported graphics API such as Vulkan or DirectX (A.K.A. Dx12, or D3D12). A translation layer is typically pre-installed on windows laptops, and translates these OpenGL calls to Dx12. And at the time of writing this, the default compatibility layer SUCKS. It REALLY REALLY sucks for older OpenGL apps.

The proposed workaround here is to use Mesa Zink instead, which converts OpenGL API calls to Vulkan API calls, entirely bypassing whatever is horribly wrong in this OpenGL-D3D12 pipeline.
