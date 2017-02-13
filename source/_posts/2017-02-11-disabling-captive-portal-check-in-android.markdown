---
layout: post
title: "Disabling Captive Portal check in Android"
date: 2017-02-11 19:44:15 +0100
comments: true
categories: 
    - android 
    - networking  
---
I recently had an issue with an Android phone that kept disconnecting from the wifi network after a certain period. 
The wifi was configured to do a captive portal style authentication check, showing me a webpage after connection that asked me to authenticate myself in order to use the network. 
When not authenticating for a certain period of time the Android phone disconnected from the wireless network causing me to wonder what is happening.

It seems that this is the default behavior of Android and this is how you can disable this behavior: 

   - First download and install __adb__ on your machine. 
     [SDK Platform Tools from Google](https://developer.android.com/studio/releases/platform-tools.html)
   - Extract the download and navigate the the folder where the adb executable located. 
   - Connect your Android phone to your machine with the USB cable 
   - Then launch the adb shell  
``` bash   
   adb shell
```
   - Type the following command to turn off captive portal check
``` bash   
settings put global captive_portal_detection_enabled 0 
```   
   - Type to see if the value of the configuration key is 0 
``` bash   
settings get global captive_portal_detection_enabled
```
