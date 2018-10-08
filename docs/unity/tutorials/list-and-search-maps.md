# List and search maps

In the [managing your maps](../managing-your-maps.md) section, you learnt how to find and delete your maps through the Placenote developer portal. Similarly, Placenote provides access to a map access API that gives you complete control over your map data.


!!! note
    The maps you create are private to your account and will not be shared with other developers. However, you can share your own maps across multiple API keys you own.

The functions available are:

* `LibPlacenote.Instance.ListMaps()     // list all maps`
* `LibPlacenote.Instance.SearchMaps()   // search maps with metadata`
* `LibPlacenote.Instance.DeleteMap      // delete maps`

## What we're building
In this tutorial we will return to the *RandomShapes* sample that we built and tested at the very beginning of this documentation. On clicking the Load Map button in this sample app, you will find a map list panel pop up that lets your search, select and load a specific map from a list of maps you have created.

## Getting a list of all maps
In *PlacenoteSampleView.cs*, take a look at the `OnListMapClick ()` function. When the *Load Map* button is clicked, we download a list of all maps, along with their respective metadata using the `ListMaps()` function that sends a download complete callback with a list of maps as a return value. We will handle the callback as shown below.

``` csharp
LibPlacenote.Instance.ListMaps ((mapList) => {

  // callback handler function. Access maps here.
  foreach (LibPlacenote.MapInfo mapInfoItem in mapList) {

      Debug.Log("Map ID: " + mapInfoItem.placeId);

      if (mapInfoItem.metadata.userdata != null) {
          Debug.Log(mapInfoItem.metadata.userdata.ToString ());
      }
   }

});
```
The `mapList` return value is a list of items of the type `LibPlacenote.MapInfo`, which contains two members:

``` csharp
public class MapInfo
{
  public string placeId;  // map id string
  public MapMetadata metadata;  // meta data
}
```

## Deleting a map

Refer to the `OnDeleteMapClicked ()` function in *PlacenoteSampleView.cs*. To delete a map with a know map ID, we can call `LibPlacenote.Instance.DeleteMap()` as shown below.

``` csharp
LibPlacenote.Instance.DeleteMap (mapId, (deleted, errMsg) => {

  if (deleted) {
      // successfully deleted
  } else {
      // failed to delete. errMsg is a string
  }
});
```

## Searching for maps
Placenote provides several options for searching maps based on name, location, time of creation or arbitrary `userdata` strings stored in the metadata. The search you use will depend on the needs of your application.

In *PlacenoteSampleView.cs*, you will find one example of map search based on GPS location. The search is implemented in the `OnRadiusSelect()` function that is activated with the distance slider is modified. We will filter maps based on their distance from the users location using the `LibPlacenote.Instance.SearchMaps()` function.

``` csharp
LibPlacenote.Instance.SearchMaps(latitude, longitude, radiusSearch,
		(mapList) => {

    // callback handler function. Access maps here.
    foreach (LibPlacenote.MapInfo mapInfoItem in mapList) {

        Debug.Log("Map ID: " + mapInfoItem.placeId);

        if (mapInfoItem.metadata.userdata != null) {
            Debug.Log(mapInfoItem.metadata.userdata.ToString ());
        }
     }
});
```

Note that the return type of `SearchMaps()` is identical to that of `ListMaps()`.

Other variations of SearchMaps include:

* `SearchMaps(string name, Action<MapInfo[]> listCb)`
* `SearchMaps(DateTime newerThan, DateTime olderThan, Action<MapInfo[]> listCb)`
* `SearchMapsByUserData(string userdataQuery, Action<MapInfo[]> listCb)`
* `SearchMaps (MapSearch search, Action<MapInfo[]> listCb)`

The last variation lets you create a `MapSearch` query object that can contain multiple search options at once.
