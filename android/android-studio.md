# Installing Android Studio - CLI
## Install SDK tools, emulator acceleration and paths
### Download android CLI tools - sdk

Download android command line tools here

- https://developer.android.com/studio/index.html#command-tools

Extract tools and place in ~/android/sdk/tools (tools - not tools/tools)


### Download and install emulator acceleration
Now install emulator acceleration for the android emulator, to speed things up. Instructions [here](https://software.intel.com/en-us/android/articles/speeding-up-the-android-emulator-on-intel-architecture#_Toc358213272).

First check if the computer supports it
```
egrep -c '(vmx|svm)' /proc/cpuinfo # if result is 0, then it doesn't, should be above 0
sudo apt-get insatll cpu-checker -y
kvm-ok # should say that the pc supports kvm
```

If all turned out ok, continue to install!
```
sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils
sudo adduser user kvm
sudo adduser user libvirtd
```

Logout and login again, if user libvrt-qemu shows up on login screen
```
echo -e "[User]\nSystemAccount=true" > /var/lib/AccountsService/users/libvirt-qemu
```

### Add to path enviroment 

Append to `~/.profile`.
```
PATH="$HOME/android/sdk/tools:$HOME/android/sdk/tools/bin:$HOME/android/sdk/platform-tools:$PATH"
ANDROID_SDK_ROOT="~/android/sdk/"
```

## Using Android SDK
### Using SDK manager and installing packages
Use `~/android/sdk/tools/bin/sdkmanager --list` to see installed packages (should not display any errors).
If it displays some errors, be sure that it's placed in the correct folder, or use
```
~/android/sdk/tools/bin/sdkmanager --list --sdk_root=~/android/sdk/
```

This will set use the correct sdk folder, and should run without issues. To install packages, use:
```
~/android/sdk/tools/bin/sdkmanager "system-images;android-23;google_apis;x86_64" platform-tools
```

Install the emulator (avd) and platform-tools (adb etc.), and a system image for emulation of your choice, pick something x86_64.
API level to android version here

-  https://en.wikipedia.org/wiki/android_version_history

### Create a avd device
```
~/android/sdk/tools/bin/avdmanager -v create avd -c 1024M --package "system-images;android-23;google_apis;x86_64" --name avd_name --device 9
```

There are some properties that are important in ~/.android/avd/avd_name.avd/config.ini.
For example is the skin very important, so that the screen is not small an unable to see anything.

```
hw.gpu.enabled=yes
hw.gpu.mode=host
hw.initialOrientation=Portrait

hw.lcd.density=420
hw.lcd.height=1920
hw.lcd.width=1080

showDeviceFrame=yes
skin.dynamic=yes
skin.name=nexus_5x
skin.path=/home/user/android/sdk/skins/nexus_5x
```
Full config from a avd created with android studio.

```
AvdId=test
PlayStore.enabled=false
abi.type=x86_64
avd.ini.displayname=test
avd.ini.encoding=UTF-8
disk.dataPartition.size=800M
hw.accelerometer=yes
hw.audioInput=yes
hw.battery=yes
hw.camera.back=emulated
hw.camera.front=emulated
hw.cpu.arch=x86_64
hw.cpu.ncore=2
hw.dPad=no
hw.device.hash2=MD5:fdsfsf
hw.device.manufacturer=Google
hw.device.name=Nexus 5X
hw.gps=yes
hw.gpu.enabled=yes
hw.gpu.mode=host
hw.initialOrientation=Portrait
hw.keyboard=yes
hw.lcd.density=420
hw.lcd.height=1920
hw.lcd.width=1080
hw.mainKeys=no
hw.ramSize=1536
hw.sdCard=yes
hw.sensors.orientation=yes
hw.sensors.proximity=yes
hw.trackBall=no
image.sysdir.1=system-images/android-23/google_apis/x86_64/
runtime.network.latency=none
runtime.network.speed=full
sdcard.path=/home/user/.android/avd/test.avd/sdcard.img
showDeviceFrame=yes
skin.dynamic=yes
skin.name=nexus_5x
skin.path=/home/user/android/sdk/skins/nexus_5x
tag.display=Google APIs
tag.id=google_apis
vm.heapSize=256
```

### Starting the AVD device
```
~/android/sdk/tools/emulator -avd avd_name
```
If you get errors like 

```
PANIC: Cannot find AVD system path. Please define ANDROID_SDK_ROOT
PANIC: Broken AVD system path. Check your ANDROID_SDK_ROOT value [/home/user/android/sdk/tools/]!
```

Check following:

- export ANDROID_SDK_ROOT path (~/android/sdk)
- that the following folders exists in ~/android, [source](https://stackoverflow.com/a/44386974).
    - emulator
    - platforms
    - platform-tools
    - system-images

## Recompile
```
$ rm app-alligned.apk && java -jar apktool.jar b app/ && zipalign -v -p 4 app/dist/app.apk app-alligned.apk && echo -n 123456 | apksigner sign --ks my-release-key.jks --out app-release.apk app-alligned.apk 
```

## Links
- https://github.com/uw-it-aca/spacescout-android/wiki/1.-Setting-Up-Android-Studio-on-Ubuntu
- https://developer.android.com/studio/index.html
- https://software.intel.com/en-us/android/articles/speeding-up-the-android-emulator-on-intel-architecture#_Toc358213272
- https://developer.android.com/studio/run/emulator-acceleration.html#accel-check
- https://www.youtube.com/watch?v=wLOP--Rxv0c
