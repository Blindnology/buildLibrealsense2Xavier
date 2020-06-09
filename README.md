# buildLibrealsense2Xavier
Build librealsense 2.0 library on the NVIDIA Jetson AGX Xavier Developer Kit. Intel RealSense D400 series cameras.

This is for version L4T 32.3.1 (JetPack 4.3), librealsense v2.29.0.

**NOTE**

December 2019

> Starting with L4T 32.2.1 (JetPack 4.2.2) on the NVIDIA Jetsons and the Intel RealSense SDK version v2.23.0, it is now possible to do a simple install from a RealSense debian repository (i.e. apt-get install).

Please see https://github.com/jetsonhacks/installRealSenseSDK for more detail.

April 2019

In order for librealsense to work properly, the kernel Image must be rebuilt and patches applied to the UVC module and some other support modules. In addition, for support of the extra features of the D435i camera such as the IMU, extra modules must be built.

Because the AGX Xavier runs kernel version 4.9 and librealsense does not support that kernel revision directly, custom patches have been constructed based on the patches in librealsense. These are located in the 'patches' directory.

In order to build and install the patched kernel Image and modules:

$ ./buildPatchedKernel.sh

The kernel Image on the Jetson AGX Xavier is signed. This means that a generated Kernel Image cannot be used directly on the Jetson without having an application sign the file. Currently, the signing application is an x86 app running on the host machine. The easiest way to apply the Image to the Jetson is to use the host machine that flashed the Jetson to sign and flash the patched kernel. 

This is a two step process. 
First make a copy of the orignal Image file as a backup. The Image file is located on the host in the JetPack Xavier/Linux_for_Tegra/kernel directory. Then, replace the JetPack Xavier/Linux_for_Tegra on the host with the Image file generated by buildPatchedKernel.sh (located in the 'image' directory) on the Jetson. You can use scp or removable media to copy the file from the Jetson to the host.

The second step is to flash the kernel onto the Jetson AGX Xavier. Place the Xavier in recovery mode, connected to the host machine using the supplied USB C to USB A cable. This is the same physical connection and procedure as when flashing the Jetson with JetPack. Then from the Xavier/Linux_for_Tegra/ directory:

$ sudo ./flash.sh -k kernel jetson-xavier mmcblk0p1

This should flash the Jetson. The -k parameter instructs the flash script to only copy the kernel Image. You should then replace the modified Image with the stock Image on the host.

Move back to the Jetson and navigate to this repositories directroy. 

You may remove all the kernel source using the provided convenience script 'removeAllKernelSources.sh'.

You are then ready to install librealsense 2.

Run the convenience script:

$ ./installLibrealsense.sh

This will build the librealsense libraries and examples. The examples will be placed in /usr/local/bin.

Application Patching

Currently there are issues with the realsense-viewer application. The first issue is that sometimes when using large frame sizes, incomplete frames are received. This is a library wide issue, not strictly limited to the realsense-viewer app. The second issue is that one of the processes tends to block in the realsense-viewer app. There are two work around patches provided here. The patches may be applied with:

$ ./patchApplication.sh

Note that you will need to recompile the library and application for these to take effect.

Release Notes:

June, 2020

* Updated for JetPack 4.3, L4T 32.3.1
* Media UVC patch removed as it's already built in
* librealsense's incomplete-frame & model-views patching enabled
* qtcreator removed from dependencies

November, 2019

* Fixed for JetPack 4.2, L4T 32.1
* Updated for librealsense 2.29
* Media UVC patch added (almost resolves frames dropping through USB)
* Librealsense working directory moved to `~/workspace`
* Librealsense cuda config patch added

April, 2019

* Updated for JetPack 4.2, L4T 32.1 and librealsense 2.20
* Tested on D435 Intel Realsense Camera

January, 2019

* Initial Release - v0.8
* NVIDIA Jetson AGX Xavier
* JetPack 4.1.1 (Developers Preview)
* L4T 31.1
* Tested on D435i and D435 Intel Realsense Cameras




