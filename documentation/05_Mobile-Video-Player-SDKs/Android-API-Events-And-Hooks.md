---
layout: page
title: Android Player API - Properties, Events, Notifications

subcat: Android
weight: 190
---

[![Android](https://img.shields.io/badge/Android-Supported-green.svg)](https://github.com/kaltura/player-sdk-native-ios)
## Using the Kaltura Android Player API Methods  

This article describes how to use the Android Player API methods to manage properties, events, and notifications. 

### Android API Events and Hooks  
The Android API supports the following events and hooks.

#### KMediaControl  
The SDK offeres the ability to perform Player operation via the KMediaControl API.
You may send the following operations:
  

| Operation  | Parameters  | Explanation |
|:------------- |:---------------:| :-------------|
| start     |  | Start playing the media         | 
| pause     |  | Pause the current playback         | 
| seek     | (long milliSeconds) | Seek a specific time position          | 
| seek     | (long milliSeconds, SeekCallback callback) | Seek a specific time position and call callback to be called when seek is done         |
| canSeekForward     |  | Check if seek backwards is possible         |
| canSeekBackward     |  | Check if seek forward is possible          | 
| replay     |  | Start over the playback         |
| isPlaying     |  | Check if the Player state is playing         |
| canPause     |  | Check if the Player can be paused         |
| getDuration     |  | Get the current media duration         |
| getCurrentPosition     |  | Get the current media position         |
| state     |  | Get the current Player state         |

***
* Example: Play/Pause button
***

For each native component listener, add one of the operations above.

``` java 
    @Override
    public void onClick(View view) {
       if (mPlayPauseButton.getText().equals("Play")) {
           mPlayPauseButton.setText("Pause");
           getPlayer().getMediaControl().start();
       } else {
           mPlayPauseButton.setText("Play");
           getPlayer().getMediaControl().pause();
       }    
    } 
    
```    

***
* Example: Seek
***

For each native component listener, add one of the operations above.

``` java 
    @Override
    public void onClick(View view){                               
           mPlayer.getMediaControl().seek(100, new KMediaControl.SeekCallback() {
              @Override
              public void seeked(long milliSeconds) {
                 Log.d(TAG, "Do your code here");                     
              }
         });
    } 
    
```    

### Player States Event Listeners  

The Kaltura Player supports the follwing states that the developer can listen to and react upon their change:
    
    - LOADED
    - READY
    - PLAYING
    - PAUSED
    - SEEKING
    - SEEKED
    - ENDED
    - UNKNOWN
 
 
* Example: Listen to state changed event
    
``` java     
    @Override
    public void onKPlayerStateChanged(PlayerViewController playerViewController, KPlayerState state) {
        if (state == KPlayerState.PAUSED && playerViewController.getCurrentPlaybackTime() > 0) {
            Log.d(TAG, "In Pause state");
        } else if (state == KPlayerState.PLAYING) {
           Log.d(TAG, "In playng state");
        }
    }
``` 

### Waiting for a READY Event  
In some cases, the end user will want to wait until the ready event is received and only then to continue.

* Example: Listen to ready state event

``` java  
 mPlayer.registerReadyEvent(new PlayerViewController.ReadyEventListener() {
      @Override
      public void handler() {
           Log.e(TAG, "PLAYER READY");
           mPlayer.getMediaControl().start();
                                }
       });
       
       
```       

### Enable/Diable Configuration on Runtime  
In some cases, the application will want to add or remove configuration attributes according to states or events that are received.
The Player provides an API for enabling and disabling these settings, the setKDPAttribute, which receives three parameters:

- plugin name
- attribute name
- enable/disabe (boolean)
    
* Example: Adding closed caption plugin

``` java 

if (state.equals(KPlayerState.PLAYING)){
   Log.d(TAG, "Playing state");
   mPlayer.setKDPAttribute"closedCaptions","displayCaptions",true);
  }


```

### Player Event Listeners  

If the application is required to react to web events, add the "mPlayerView.addKPlayerEventListener".

See [Supported Events](https://vpaas.kaltura.com/documentation/04_Web-Video-Player/Kaltura-Media-Player-API.html#commonly-used-player-events) for information about the types of supported events.


* Example: Listen to show play controls event

``` java
 mPlayerView.addKPlayerEventListener("showPlayerControls", "showPlayerControls", new PlayerViewController.EventListener() {
       @Override
       public void handler(String eventName, String params) {
       Log.d(TAG, "on showPlayerControls received");
}
});

```
 
### Sending Event Notifications

To change the current player behavoir, use the sendNotification API. This method receives two parameters:
  - action name
  - parameter (string/json)  

#### Examples:  

**Notification without parameters:**
 
``` java 
getPlayer().sendNotification("doPause", null);
```

**Notification with a string parameter:**

``` java
getPlayer().sendNotification("doSeek", Double.toString(seconds));
```

**Notification with a JSON parameter:**

``` java
String entry = "'{\"entryId\":\"" + entryId + "}'";
getPlayer().sendNotification("changeMedia", entry); 
```
                              

 
