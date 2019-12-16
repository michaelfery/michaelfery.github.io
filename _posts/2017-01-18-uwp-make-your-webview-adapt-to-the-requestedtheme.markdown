---
layout: post
title:  "UWP : Make your WebView adapt to the RequestedTheme"
date:   2017-01-18 07:14:38
# categories: blog
description: "In UWP applications WebView do not care at all about the requested theme you set.
It’s not a big problem if the webview is navigating to an online content because this content will probably set a specific design for the background, fonts, etc.
But, what if you have a local html file you want to display inside your app ?"
img: posts/windows-banner.png
tags: ["uwp", "apps", "navigatetostring", "requestedtheme", "webview"]
redirect_from: 
  - /blog/uwp-make-your-webview-adapt-to-the-requestedtheme
  - /blog/uwp-make-your-webview-adapt-to-the-requestedtheme/
---

In UWP applications WebView do not care at all about the requested theme you set.

It's not a big problem if the webview is navigating to an online content because this content will probably set a specific design for the background, fonts, etc.

But, what if you have a local html file you want to display inside your app ?

If you set a local html content, you probably want this one to adapt to your layout and not set a full blank background in your dark layout for example.

To achieve a theme aware webview, two steps are required :

First, we need to override the default white background of the webview.

For this, the property **DefaultBackgroundColor** of the WebView control is doing the job. That's simple !

```xml
    <WebView x:Name="WebView" DefaultBackgroundColor="Transparent" HorizontalAlignment="Stretch" VerticalAlignment="Stretch"/>
```

If we launch our application in Dark theme, we will see a full black page, because the font color is set to black as default.

We need to override this.

In the code-behind of our page, before calling the NavigateToString method, we are going to add a style to our content.

```csharp
    var head = "<head><meta charset=\"UTF-8\"></head>";
    var styles = "<style>html{font-family:'Segoe UI';}</style>";
    var html = "Your html content";
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame != null && rootFrame.RequestedTheme == ElementTheme.Dark) {
        styles += "";
    }
    this.WebView.NavigateToString(head + styles + html);
```

This style override the default font color but will be also overrided for specific styles like "a" links.

Now you can run your application with the two defaults required themes and see that the webview content is adapted as well.

![Theme Aware Webview UWP](http://mfery.com/wp-content/uploads/2017/01/Untitled-1-1024x177.png)
