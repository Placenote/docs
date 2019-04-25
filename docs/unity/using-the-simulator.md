# Placenote Unity Simulator
Building a Unity project to a mobile device over and over is very time consuming. Placenote SDK includes a built-in simulator so that Placenote functions are useable directly within the Unity editor. The Simulator provides almost all core Placenote functionality you need to iterate on your app several times before you ever build to a device.

## How to test the Simulator
Download the latest unity package and navigate to *Assets/Examples/RandomShapes*. Now open the *RandomShapes* scene and hit the "Play" button. You will notice a living room scene loads in the editor. You can move your camera around with the "A", "W", "S", "D", "R" and "F" keys. Click "Drop Shape" during mapping to save shapes to the map meta data. The preview video below shows what you should be seeing.

<iframe width="560" height="315" src="https://www.youtube.com/embed/rsy-tXilzrg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Adding the simulator in your project
The Simulator is automatically built into `LibPlacenote.cs`, so it's very easy to set up. To add the simulated world to your project, add the *SimulatedWorld* prefab (from the *Assets/Prefabs* folder) to your Scene hierarchy.

## What is the Simulator Useful for?
* Quickly prototyping your app, and testing out basic AR functionality.
* Understanding how Placenote’s workflow of mapping and localization will fit within your app.

!!! tip
    Remember that the Simulator does not communicate with the Placenote Cloud. Simulator maps and asset data do not get uploaded to your developer portal. Rather we save simulated maps in a local file in the Assets folder called "jsonMaps.txt".
