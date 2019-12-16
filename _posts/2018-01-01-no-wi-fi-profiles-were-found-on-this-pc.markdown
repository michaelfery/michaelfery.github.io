---
layout: post
title:  "IOT Core Dashboard Device Provisioning: No Wi-fi profiles were found on this PC"
date:   2018-01-01 10:42:00
# categories: blog
description: "IOT Core Dashboard let you set up the wifi profile on a new device only if your computer has already been connected to the wifi network through a profile. If not you will see the error \"No Wi-fi profiles were found on this PC\"."
tags: ["iot", "windows iot core"]
redirect_from: "/blog/no-wi-fi-profiles-were-found-on-this-pc"
---

As I was trying to configure a new Raspberry Pi device with IOT Core Dashboard I faced a Wi-FI profile problem that you could encounter too.

When you're configuring a new IOT Core device you can set up a wifi profile on it. It let the device connect itself to the wifi network automatically when it's available and let you avoid the boring step of wired connecting it to the network and set up the wifi connection through powershell. This feature is especially cool when you want to configure multiple devices and don't want to reapeat the operation on each time.

But the IOT Core Dashboard only let you set up the wifi profile on a new device only if your computer has already been connected to the wifi network through a profile. If not you will see the error "No Wi-fi profiles were found on this PC".

An other limitation is that WPA2-PSK Personal Networks profiles cannot be used without some changes due to the automatic encryption of the password that I will explain in a coming post. Let's assume that you're not using that type of network for this post.

Let's create the profile !

## Export the Wi-Fi profile

First, you have to connect your computer to the Wifi Network with SSID selection and passphrase setting.

When you're done, you can open a command line window and list all the known networks . Run the following command:

`netsh wlan show profiles`

When you get the list of the known networks you can choose one and export it in a file:

`netsh wlan export profile %SSIDName% folder=c:\temp`

![](http://mfery.com/wp-content/uploads/2018/01/wifiprofileslist.png)

Ok, now you have the profile saved in a file but your computer has not connected itslef through this profile file and it cannot be listed in the IOT Core Dashboard application. We have to use it.

## Forget the current network

For using the newly created profile, we have to forget the current network.

Click on the wifi icon in the taskbar. In the list of available networks right-click the current network and select **Forget**.

## Add the Wi-Fi profile

Now you can run the fllowing command int the folder where you saved the profile to use it:

`netsh wlan add profile filename="%SSIDName%.xml" user=all`

## Conclusion

The last thing to do is to restart the IOT Core Dashboard application and you will see the profile in the field : Wi-Fi Network Connection.

You can now use it to configure your new IOT devices.

![](http://mfery.com/wp-content/uploads/2018/01/full-1024x691.jpg)