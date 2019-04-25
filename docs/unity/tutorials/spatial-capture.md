# Using the Spatial Capture toolkit
When creating persistent scenes in a physical location, you cannot always be there to ensure you are positioning and scaling your content accurately in the area. With the Placenote Spatial Capture toolkit, you can scan an area to capture point clouds and ARKit planes and then load this data into the Unity editor at any time as a visual reference.

!!! tip
    The point cloud loaded in Unity is textureless, but it is scaled accurately in meters.

## Download the Spatial Capture app
The Placenote Capture app is currently in beta, you can download it here.

<a href="https://placenote.com/capture"><img src="../../../img/unity/spatial-capture/spatial-capture-logo.png"></a>

You will need to open the link above on your mobile device. Or visit [placenote.com/capture](https://placenote.com/capture).

## Capturing a scan
To start, open the Placenote Capture app to create a map. The app will capture a visual representation of the environment (planes and features) of your map and save it into meta-data in the cloud.

* Once you launch the app, sign-in to the app using your developer account, if you don't have a developer account, get one at http://developer.placenote.com

* After you are signed in, click on 'New Map' to start mapping your environment. <br /> <br /> <iframe width="560" height="315" src="https://www.youtube.com/embed/vSggFxJlX7k" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

* When you are done mapping, click on 'Save Map' to save upload to the server. You can do a quick test of localization with the 'Test Map' button or exit and click on 'My Maps' to get a list of all the maps you have created. Make a note of the Map ID you just created.

## Testing the captured map in Unity
To test the various features of Spatial Capture within Unity you can use a Sample Scene provided with this project. Open the scene at *Placenote/Examples/SpatialCapture/SpatialCapture.Unity* and follow these steps.

* Choose the ***PlaceMesh*** GameObject in your Scene Hierarchy panel and enter your API Key in the Inspector Panel. The *PlaceMesh* object is an empty GameObject with the Placenote/Toolkits/SpatialCapture/PlaceMesh.cs script attached to it. <img src="../../../img/unity/spatial-capture/place-mesh.png">

* With the API Key entered, if you click on *‘Print Map IDs in the console’*, it will print the unique ID’s of all the maps you have captured using the Placenote Capture app. Choose the Map ID you want to view and enter it into the Map ID field.

* Now, if you click on *‘Add Planes and PointCloud’*, the script will download all the visual details you captured using the app and render them in the Unity editor in correct scale. <br /><br /> <iframe width="560" height="315" src="https://www.youtube.com/embed/Q6dGDk0hxkk" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

* You can now design your scene using the point cloud map as a visual reference. In this particular sample app, you can add any scene elements to the empty ‘Models’ GameObject. You can then accurately line up your models and spatial elements with respect to the map. <br /><br /> <iframe width="560" height="315" src="https://www.youtube.com/embed/1JEwgIxwFEc" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

* Once your scene design is done, add the MapID to the Placenote Spatial Capture Sample script attached to the *Canvas > Panel* GameObject. <br /><img src="../../../img/unity/spatial-capture/deploying-place-mesh.png" width="400">

* This will load the map and localize against it. Once localized, it will enable all the GameObjects under the ‘Models’ Parent in the location you set up in the Unity scene. Voila! <br /><br /> <iframe width="560" height="315" src="https://www.youtube.com/embed/PepRi7RNLVg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

## Incorporate Spatial Capture maps in your own app
If you want to design a scene using the visual elements of a map in your own app, simply add the prefab at *Assets/Placenote/Prefabs/PlaceMesh* to your Scene. Enter your API Key and Map ID as before and it will add the planes and features to your scene for you to use for accurate placement of spatial features within the Unity editor.
