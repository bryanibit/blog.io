---
layout: post
title: Older Version iOS APP Download
date: 2019-5-16
categories: blog
tags: [个人总结]
description: Older Version iOS APP Download
---

You can refer to the [medium link](https://medium.com/@dixitakansha15/how-to-download-older-version-of-latest-ios-app-from-appstore-91c28d2407d9), the following is the simplified version.

**Step 1**: Download **Charles Proxy 3.12**, install it and move it to application folder. Launch Charles from applications, grant permissions as required.

**Step 2**: Open iTunes →app store →search your app →go to description and open both charles window and iTunes window simultaneously.

**Step 3**: Click *DOWNLOAD* in itunes,watch the download activity in the Structure section inside Charles. You should see a “buy” server, like```p<num>-buy.itunes.com```.

**Step 4**: Right Click buy server, **ENABLE SSL PROXYING**.Select and stop downloading of your app in iTunes.

**Step 5**(option): In Charles go to “help” -> SSL Proxying -> Install Charles Root Certificate -> add then Open “Keychain Access” -> search for “Charles Proxy CA” -> double click it -> change to always trust(mac). If Windows, just add it to certificated root dir.

**Step 6**: Search for the app again in iTunes, and download the app again. **Cancel the download** again.Click the disclosure triangle by the “buy” server and click buyProduct.

**Step 7**: Click the Content tab, right click on buyProduct and click Export. Select the Desktop as the Export location, select XML for format, and click Save.

**Step 8**: Open the XML file using a text editor, and scroll down to locate the integer numbers of key called **softwareVersionExtenalIdentifiers**.  
Note:These are the version numbers of the app.The last one is the latest one.I want oldest one,so i copied the first one.You can choose according to your requirement.Close the text editor once copied the text.

Right now, right here, we have all version keys, so the following can be found in link on the top of the page.