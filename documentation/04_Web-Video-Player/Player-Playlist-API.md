---
layout: page
title: Configuring and Customizing Kaltura Player Playlists
weight: 405
---

The Kaltura Player provides a robust playlist plugin that allows rendering a playlist of entries with many configuration options, such as:   

* Render the playlist to the right, left, top or bottom of the Player
* Render a vertical or horizontal playlist
* Render the playlist on the publisher's page outside of the Player iFrame
* Auto-play, auto-continue, loop, custom header
* Multiple playlists for the same Player
* Custom renderer for playlist items
* Hidden playlists provide playlist functionality without rendering the playlist itself
* Next / Play buttons
* Robust API allowing communicating with the playlist from Javascript

While you can create a playlist functionality on your page by calling the Kaltura list API and using the Kaltura player `changeMedia` notification, using the Player's built-in playlist plugin will shorten your time to launch, simplify the management of your code, and provide better playback control.

## Playlist Config Parameters

The playlist plugin exposes the following Flashvars configuration parameters to configure the behavior of the playlist:

* `containerPosition`: Sets the playlist position to left, right, top and bottom
* `layout`: Sets the playlist layout to vertical or horizontal
* `autoPlay`: Starts playing the first clip upon load
* `autoContinue`: Continues to the next clip when the currently playing clip ends
* `loop`: Loops the playlist playback (jumps to the first clip when the last clip ends)
* `hideClipPoster`: Hides the clip poster when switching to another clip
* `initItemEntryId`: Indicates that the entryId that should be played first

#### Configure Playlist via Player Studio UIVars 
When configuring the playlist via the Player Studio UIVars, remember that each new line is key=value pair:

```
playlistAPI.containerPosition=bottom
playlistAPI.layout=horizontal
playlistAPI.autoContinue=true
```

#### Configure Playlist via embed Flashvars

```javascript
flashvars: {
	"playlistAPI": {
		"containerPosition" : "bottom",
		"layout" : "horizontal",
		"autoContinue" : true
	}
}
```

## Common Player Playlist Best Practices

Below you will find common best practices to customize and configure your playlist Player.

### Size Relationship of Player and its Playlist UI

When adding a playlist to your Player, the playlist UI will take screen space from the dimensions of your Player. As such, you should set your Player dimensions accordingly to accommodate for the added playlist UI dimensions. 

* For vertical playlist on the right or left, add the playlist width to the player width. Default playlist width is 320px.
* For horizontal playlists on the top or bottom, add the playlist height to the player height. Default playlist height is 113px.
* When using onPage playlists (playlists that render outside of the player iFrame) you don't need to change the player size.

### Customizing the Playlist Item Renderer UI

You can use a custom CSS with your playlist in order to change playlist item CSS definitions such as color and fonts.  
This is done by setting the playlistAPI `iframeHTML5Css` property to your custom CSS file. 

> Review: {% extlink Playlist with custom template and paging http://player.kaltura.com/modules/KalturaSupport/tests/PlaylistPagingTemplate.html %} for a well-developed example of using a custom template to customize the playlist plugin UI and behavior.

### Customizing the Playlist Item Renderer Displayed Data

You can change the data displayed for each clip by setting your own custom template for the playlist item renderer.  
The custom template overrides the base playlist template and defines which data to show and the HTML markup used to render the data.   

> {% extlink Review the base playlist template on GitHub https://github.com/kaltura/mwEmbed/blob/master/modules/KalturaSupport/components/playlist/playList.tmpl.html %}.

You can override this template with your own template by setting the `playlistAPI.templatePath` property to link to your custom template. 

### OnPage Playlist (Rendered on the Hosting Page)

You can decide to render the playlist UI on the web page in which the player is embeded. 
To render the playlist on the hosting page, provide the div element id into which you'd like the playlist to render, by setting the `playlistAPI.clipListTargetId` property. If you don't specify a div element id, the playlist will default to render below the player.

> Review: {% extlink Playlist on page http://player.kaltura.com/modules/KalturaSupport/tests/PlaylistOnPage.qunit.html %} for an example of rendering the playlist UI on the hosting page.

When rendering a playlist on the hosting page, consider the following:

* OnPage playlist will work only when using dynamic Javascript embed because iFrame embeds prevent access to the hosting page.
* You can add your own logic to the playlist div element using Javascript. For example, add an expand / collapse functionality.
* If you define a custom CSS for your playlist, by setting the `playlsitAPI.onPageCss1` property and link to your CSS file.

## Playlist API, Properties, and Events

The playlistAPI plugin exposes Javascript API that can be used to communicate with the playlist using the Kaltura Player API. 

### Playlist API Properties 

| Property | Description |
|:---|:---|
| `dataProvider` |	Holds all of the playlist items |
| `selectedIndex` |	Current clip index |
| `tabBar.selectedIndex`	| Current playlist index when using multiple playlists |

#### Examples

* Play the second clip of the current playlist: 

```javascript
kdp.setKDPAttribute("playlistAPI.dataProvider", "selectedIndex", 1 );
```

* To get the currently selected clip index:

```javascript
kdp.evaluate( "{playlistAPI.dataProvider.selectedIndex}" ) );
```

> Review: {% extlink Using Playlist Properties examples http://player.kaltura.com/modules/KalturaSupport/tests/PlaylistOnPage.qunit.html %}.

### Playlist API Methods

| Method | Description |
|:---|:---|
| `playlistPlayNext` |	Play next clip |
| `playlistPlayPrevious` |	Play previous clip |

#### Examples

* Play the next entry in the playlist:

```javascript
player.sendNotification("playlistPlayNext");
```

* Play the previous entry in the playlist: 

```javascript
player.sendNotification("playlistPlayPrevious");
```

### Playlist API Events (Hooks)

| Event | Description |
|:---|:---|
| `playlistReady` |	Playlist is ready to play |
| `playlistFirstEntry` | 	First clip in the playlist starts playing | 
| `playlistMiddleEntry` |	Any middle clip in the playlist starts playing |
| `playlistLastEntry` |	Last clip in the playlist starts playing |
| `playlistPlayNext` |	Next clip requested |
| `playlistPlayPrevious` |	Previous clip requested |
| `playlistSelected` |	When using multiple playlists, the user selects a specific playlist |
| `EmptyPlaylistCustomError` |	Playlist was loaded without entries |
 
#### Examples

* Hook to when the next entry in the list will be played: 

```javascript
player.kBind("playlistPlayNext", function(){
	console.log("the next entry will now play");
});
```

* Detect when a faulty playlist is loaded: 

```javascript
player.kBind("EmptyPlaylistCustomError", function(){
	console.log("faulty playlist, let's switch playlist and/or report error");
});
```

> Review: {% extlink Registering to Playlist Events http://player.kaltura.com/modules/KalturaSupport/tests/PlaylistEvents.qunit.html %}.

## Playing a Stitched Video Playlist

The Kaltura backend provides the capability to  stitch manual playlists dynamically, by combining video entries into a single continuos video stream.  
The Kaltura Player leverages the ability to play stitched playlists as if they were a single video entry.  
This is an important capability that supports use cases of dynamically personalized video experiences.
To play a stitched playlist, simply pass the ID of the manual playlist you would like to stitch as the entryId parameter.

For example, if the playlist id `0_kh14ze3t` consists of 6 entrie, instead of loading it as a playlist player where the end user loads each video on its own, you can instead stitch the videos and play them as one. To achieve this option, simply pass the ID of the playlist (`0_kh14ze3t`) in the entryId parameter of the embed code:

```javascript
kWidget.embed({
	"targetId": "myTargetEmbedDiv",
	"wid": "_811441",
	"uiconf_id": 33991361,
	"entry_id": "0_kh14ze3t"
});
```
