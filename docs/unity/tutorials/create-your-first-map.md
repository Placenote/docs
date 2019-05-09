title: Creating your first Map
description: Creating your first Persistent AR Map with Placenote

# Save and load your first map
This first tutorial will walk you though an example of how to create, save and reload a single world map. To follow this tutorial, open the ***SaveAndLoadAMap*** Scene in the *Assets/Examples* folder. The script *SaveAndLoadAMap.cs* is the main controller for this project. We'll use this as a reference to learn about basic Placenote functions.

## What we're building in this tutorial
If you build the *SaveAndLoadAMap* scene to your device you will find that the app lets you scan an area and save a map. The rocket model in the scene is placed at an arbitrary static position in front of you. When you load the app in a different area you will find that the rocket first initializes in the scene at an arbitrary location, relative to the device, but upon localization the rocket moves to the correct previously initialized position.

## Step 1: Start an ARKit session
Placenote extends ARKit functionality by providing a cloud-based mapping engine to help you build persistent AR apps. This means, before you set up Placenote, you will need to know how to create a basic ARKit app.

Setting up ARKit is easy. Take a look at the *SaveAndLoadAMap.cs* script. The ARKit session is started in the `StartARKit()` function. The code segments below help set up the ARKit session. You will need this for any Placenote app your build for Apple's ARKit enabled devices.

``` csharp

// Import ARKit Library
using UnityEngine.XR.iOS;

// Add a Unity ARKit Session handler in your class definition
private UnityARSessionNativeInterface mSession;

// In the script's Start() function, initialize the ARKit session
mSession = UnityARSessionNativeInterface.GetARSessionNativeInterface ();
StartARKit ();

// The Initialize ARKit function.
private void StartARKit ()
{
    Application.targetFrameRate = 60;
    ARKitWorldTrackingSessionConfiguration config = new ARKitWorldTrackingSessionConfiguration ();
    config.planeDetection = UnityARPlaneDetection.Horizontal;
    config.alignment = UnityARAlignment.UnityARAlignmentGravity;
    config.getPointCloudData = true;
    config.enableLightEstimation = true;
    mSession.RunWithConfig (config);
}
```

!!! note
    We're assuming here that you have some knowledge of ARKit development in Unity and will not be going into the details of that each of the functions above does. If you're new to Unity and ARKit, <a href="https://www.youtube.com/watch?v=S7kKQZuOdlk" target="_blank">you can check out this video tutorial</a> to get up to speed.

## Step 2: Initialize Placenote SDK

To start setting up Placenote, add *PlacenoteListener* to your class definition like so.

``` csharp
public class MainController : MonoBehaviour, PlacenoteListener {
```

In our `start()` function, in addition to the ARKit initialization, we now initialize Placenote and register its listener function.

``` csharp
FeaturesVisualizer.EnablePointcloud (); // Optional - to see the debug features
LibPlacenote.Instance.RegisterListener (this);
```

Finally, we add two Placenote listener functions to your class like this. We will discuss what these are later in this tutorial.

``` csharp
// Runs when a new pose is received from Placenote.    
public void OnPose (Matrix4x4 outputPose, Matrix4x4 arkitPose) { }

// Runs when LibPlacenote sends a status change message like Localized!
public void OnStatusChange (LibPlacenote.MappingStatus prevStatus, LibPlacenote.MappingStatus currStatus)
{ }
```

## Step 5: Create, Save and Reload a Map

!!! warning
    Placenote version 1.6.8 introduces a breaking change in the LibPlacenote SDK. We have simplified the SDK initialization process by abstracting the sendFrame() function within LibPLacenote. To learn more check the [Migrating to SDK 1.6.8 from an earlier version](migrating-to-sdk-1-6-8.md) on Unity page.

### Start a mapping session
In the example code, we start a mapping session when the *New Map* button is clicked. In the `OnNewMapClick()` function we start a placenote mapping session like this.

``` csharp
LibPlacenote.Instance.StartSession ();
```

### Save a map
We save the map in the *OnSaveMapClicked()* function. Notice that the `SaveMap()` function takes two callback function arguments - the "map ID callback" and the "upload progress callback". The map ID callback function returns a unique map ID from the Placenote Cloud that you can use to reload the map in the future. The upload progress callback function returns the status of the map upload, including percentage completion.

The skeleton of the SaveMap function is shown below.
``` csharp
    LibPlacenote.Instance.SaveMap (

        (mapId) => { // map ID callback
            },
        (completed, faulted, percentage) => { // upload progress callback
            if (completed) { // upload is completed
            }
            else if (faulted) { // upload failed
            }
            else { // upload percentage  (0 - 1)
            }
        }
    );
```

Notice that in the *SaveAndLoadAMap.cs* sample code, we call these two functions within the map ID callback.

``` csharp
LibPlacenote.Instance.StopSession();
WriteMapIDToFile(mapId);
```

`LibPlacenote.Instance.StopSession()` stops the Placenote mapping session. You need to call StopSession whenever you want to end a Placenote mapping or localization session. `WriteMapIDToFile(mapId)` saves the mapID in a text file so that we can retrieve it in a future session.

!!! tip
    It's a good practice to always check that `LibPlacenote.Instance.Initialized()` is true before calling a Placenote function.

### Reloading a map
To reload a map, we need a function that takes a previously created map ID and loads the corresponding map. This is achieved in the `OnLoadMapClicked()` function with `LibPlacenote.Instance.LoadMap()`. The LoadMap function takes two arguments, a map ID to load and a download progress callback. The skelon structure of the LoadMap function is given below.

``` csharp
LibPlacenote.Instance.LoadMap (selectedMapID,
    (completed, faulted, percentage) => {
        if (completed) { // finished downloading the map
        }
        else if (faulted) { // download failed
        }
        else { // download percentage (0-1)
        }
    }
);
```
!!! note
    When LoadMap is called, it first checks the app cache to ensure the map wasn't already downloaded. If it is available, Placenote directly loads the map from your app's cache to avoid unnecessary downloads. The map is downloaded from the Placenote cloud if it is not available locally, in cases where a map is being loaded on a device it was not created on.

Notice that in the sample code, we call `LibPlacenote.Instance.StartSession();` within the `completed` block of the download progress callback. This starts a new Placenote session, but this time it is not a mapping session. When `StartSession()` is called after `LoadMap()`, Placenote will start trying to localize your map.

### Detecting a successful relocalization

Placenote returns a series of status change events that we can use to trigger certain actions. Each status change event returns the new, as well as previous status as a `LibPlacenote.MappingStatus` and we can use this to identify the state of a Placenote session.


``` csharp
public void OnStatusChange (LibPlacenote.MappingStatus prevStatus, LibPlacenote.MappingStatus currStatus) {

  if (currStatus == LibPlacenote.MappingStatus.RUNNING && prevStatus == LibPlacenote.MappingStatus.LOST) {
      // Placenote has successfully localized your map
  }
  else if (currStatus == LibPlacenote.MappingStatus.RUNNING && prevStatus == LibPlacenote.MappingStatus.WAITING) {
      // Started a new mapping Session

  } else if (currStatus == LibPlacenote.MappingStatus.LOST) {

    // Lost map, trying to localize again
  } else if (currStatus == LibPlacenote.MappingStatus.WAITING) {

    // After stop session is called.
    }
  }

}
```

The state we use most frequently is within

``` csharp
if (currStatus == LibPlacenote.MappingStatus.RUNNING && prevStatus == LibPlacenote.MappingStatus.LOST)
```

We will see in the next tutorial on Saving Meta Data, how we can use this status change to load our persistent AR content upon localization.
