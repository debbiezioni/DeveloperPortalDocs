---
layout: page
title: Configuring the Player to use Google Ads in Android Devices
subcat: Android
weight: 120
---

[![Android](https://img.shields.io/badge/Android-Supported-green.svg)](https://github.com/kaltura/player-sdk-native-ios)

## Overview
This article explains how to configure the Player to use Google ads Android devices.

## Configuring the Player
Add the following to your `KPPlayerConfig`:

```
config.addConfig("doubleClick.plugin", "true");
config.addConfig("doubleClick.adTagUrl", "your ad tag URL");
```
# Listening to Ad Events
To listen to ad events, you can use the following:

[Ad events test page](http://player.kaltura.com/modules/DoubleClick/tests/DoubleClickAdEvents.qunit.html)  

Click here for a [list of commonly used Player ad events](https://github.com/kaltura/DeveloperPortalDocs/blob/master/documentation/04_Web-Video-Player/Kaltura-Media-Player-API.md).

```
mPlayer.addKPlayerEventListener("adClick", "some_id", new PlayerViewController.EventListener() {
                @Override
                public void handler(String eventName, String params) {
                    // Do your stuff here..
                }
            });
```