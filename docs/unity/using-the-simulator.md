# Placenote Unity Simulator
Building a Unity project to a mobile app over and over is time consuming and truly annoying. That's why Placenote includes a simulator so that Placenote functions are useable right within the Unity editor. This Simulator is automatically built into `LibPlacenote.cs`, so it's very easy to set up.

## Preview video
<iframe width="560" height="315" src="https://www.youtube.com/embed/rsy-tXilzrg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## How to use the Simulator
To try the simulator just download the latest unity package, open the sample scene and hit play. You can move your camera around with the "A", "W", "S", "D" keys. Click "Drop Shape" during mapping to save shapes to the map meta data. You can also change the default simulated environment in Unity by modiying the game objects under "Sample Terrain" in the projects Scene Hierarchy.

## What the Simulator provides
The Simulator provides almost all core Placenote functionality you need to iterate on your app several times before you ever build to a device. The functions currently supported by the Simulator are:

* StartSession
* StopSession
* SaveMap
* LoadMap
* DeleteMap
* ListMaps (SearchMaps is not currently available)
* OnStatusChange
* OnPose
* SetMetaData
* GetMetaData

## What is the Simulator Useful for?
* Quickly prototyping your app, and testing out basic functionality as your app interacts with Placenote.
* Understanding and testing how Placenote’s workflow of mapping and localization will fit within your app.

## What the Simulator does not provide
* The Simulator does not provide any communication with the Placenote servers. All maps generated with the Simulator are stored locally, and cannot be uploaded to the Placenote servers.
