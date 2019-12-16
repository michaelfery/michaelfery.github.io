---
layout: post
title:  Choose your NFC device in ProximityDevice API
date:   2015-09-09 21:29:00
# categories: blog
description: "When your application turns on a phone, you have only one reader. When your application turns on a PC, you probably have none but you can plug an external reader. But, what if the laptop already has an embedded reader and you want to use an external one ?
There is a method for that : ProximityDevice.FromID(string deviceID)"
tags: ["uwp", "nfc"]
redirect_from: "/blog/choose-you-nfc-device-in-proximitydevice-api"
---

A lot of samples explain how to use the **ProximityDevice** API. I wrote about it too.

So what you read is that when you want to use a ProximityDevice like a NFC reader, you have to call the ProximityDevice API and get the default device. It works in 99% of situations especially in phone applications.

But a recent mission for a client put me in a case that we forgot to mention because it's rare.

When your application turns on a phone, you have only one reader. When your application turns on a PC, you probably have none but you can plug an external reader. But, what if the laptop already has an embedded reader and you want to use an external one ?

There is a method for that :

```csharp
ProximityDevice.FromID(string deviceID)
```

Ok let's find this ID !

You'll think it's easy because the API will give you a beautiful static method **ProximityDevice.GetAllDevices**, or something like that ? You're wrong !

You will need to use another API called **DeviceInformation**.

This API exposes a method **FindAllAsync** that lists all the devices on your machine. That's a bit rude because we only want the proximity devices.

The **FindAllAsync** method allows you to pass a request string so you can filter the devices by types.

You read correctly. You have to know the string request that filter the devices. To get this string, the trick is not really hard because the first API we use can give it to use :

```csharp
ProximityDevice.GetDeviceSelector()
```

A last tricky point is that when you request the device list you will get a DeviceInformationCollection which is not a Collection of DeviceInformation so don't try to bind a listview directly on it.

So, in conclusion, when you want to list the devices you have to use this code :

```csharp
string selectorString = ProximityDevice.GetDeviceSelector();
// Get all proximity devices
var deviceInfoCollection = await DeviceInformation.FindAllAsync(selectorString, null);
if (deviceInfoCollection.Count == 0) {
    //TODO indicates that no nfc devices are available and disable the nfc checkbox
    ViewModel.IsUsingNfcReader = false;
} else {
    // Set all proximity devices in a list
    ViewModel.ProximityDevices = new ObservableCollection();
    foreach (var device in deviceInfoCollection); {
        ViewModel.ProximityDevices.Add(device);
        // you can use the device.Name for display but you'll need the ID for proximity usage
    }
```

And when you want to use the selected device of your choice, use these lines :

```csharp
myProximityDevice = ProximityDevice.FromID(selectedDeviceInformation.Id);
```
