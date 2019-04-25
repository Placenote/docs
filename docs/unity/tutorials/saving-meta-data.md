# Saving meta data with your maps

One of the unique features of Placenote SDK is the ability to save metadata directly with your maps on the Placenote cloud. **You can save up to 16 MB of meta data and asset data with each map.**

You can store any form of serializable data within the metadata holder and Placenote SDK includes a JSON serialization library as well as helper functions to make this extremely easy to use.

## What can meta data be used to store?

* Custom map name, GPS location and time of creation of a map.
* Position, rotation, size, asset URL etc. of 3D objects in persistent AR scenes.
* Points of interest, navigation meshes etc. for indoor navigation apps.
* Mesh vertices or lines in apps with user generated content like AR art.

## What we're building in this tutorial
In this tutorial, we will be using the **SaveMetaData** sample scene as a reference. Try to build this scene to your device. This sample app extends the *SaveAndLoadAMap* sample by letting you drop various 3D models at different points in the area around you, while mapping the area. When you click Save Map, the positions of these models are uploaded to the Placenote cloud. When you click Load Map, the last saved map is reloaded and all your dropped models, get re-initialized in their saved positions.

## The meta data container object
Meta data is saved to Placenote using the `LibPlacenote.MapMetadataSettable` class and it is retrieved using the `LibPlacenote.MapMetadata` class. When setting metadata, there are 3 main parameters we can set:

* `name` - set a custom string for map name
* `location` - set the latitude, longitude and altitude read from GPS
* `userdata` - Set a custom JObject with serializable content

## Saving meta data
The *SaveMetaData* folder contains two scripts, *SaveMetaData.cs*, which is the main controller and *ModelManager.cs* which contains helper functions to read and write to the userdata object. When saving meta data with a map, we first need to generate the `userdata` object and then upload the metadata to the Placenote cloud.

### Creating the userdata object
In ModelManager.cs, there are two classes, `ModelInfo` and `ModelList` that store the position and rotation and any other parameters of the assets to be saved. Note that the elements of the class must be simple serializable objects like `float`, `int`, `string`.

The `OnAddShapeClicked()` function generates a `ModelInfo` object and adds it to a list of models. When saving the data, the `Models2JSON()` function converts the stored list into a JObject to save in `userdata`. The `CreateMetaDataObject()` function in *SaveMetaData.cs*, shows how to set userdata.

``` csharp
JObject userdata = new JObject();
JObject modelList = GetComponent<ModelManager>().Models2JSON();
userdata["modelList"] = modelList;
metadata.userdata = userdata;
```

### Uploading meta data
In this example we save meta data simultaneously while saving the map. This is not neccessary and as long as you have a mapID, you can set meta data at any time. Setting meta data is done with the `SetMetadata()` function as shown below. You will receive a callback when the data is successfully uploaded.

``` csharp
LibPlacenote.Instance.SetMetadata(mapId, metadata, (success) => {
     if (success) {
         // Meta data successfully saved
     }
     else {
         // Meta data failed to save
     }
 });
```

In our example, let's extend the bare bones SaveMap function to include a call to set meta data. The new `SaveMap` definition looks like this:

``` csharp
LibPlacenote.Instance.SaveMap (

    (mapId) => { // map ID callback

        LibPlacenote.Instance.StopSession(); // stop mapping session

        LibPlacenote.MapMetadataSettable metadata = CreateMetaDataObject();

        LibPlacenote.Instance.SetMetadata(mapId, metadata, (success) => {
             if (success) {
                 // Meta data successfully saved
             }
             else {
                 // Meta data failed to save
             }
         });
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

!!! tip
    Meta data can be set at any time, not just when saving a map.

## Downloading meta data

Given a map ID, you can download meta data using the `GetMetaData` function like this:

``` csharp
LibPlacenote.Instance.GetMetadata(savedMapID,(LibPlacenote.MapMetadata result) =>
{
    if (result != null) {

        // result.name and result.location are available
        string name = result.name;
        JToken modelData = result.userdata;
    }
    else {
        // Failed to download meta data
    }
});
```

In this sample app, notice that we download the meta data, within the `completed` callback of ` LibPlacenote.Instance.LoadMap`. This is purely a matter of choice and you could choose to download the meta data at any time. Notice that although we download the meta data here, we load it in the scene only after Placenote localizes, within the `OnStatusChange()` callback, like so:

``` csharp
JToken modelData = downloadedMetaData.userdata;
GetComponent<ModelManager>().LoadModelsFromJSON(modelData);
```
