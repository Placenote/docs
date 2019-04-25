# Working with Point Clouds
When running all previous sample apps, you will have noticed the green feature points that are generated during the mapping and localization process. This is a debug visualization to help you understand how Placenote works under the hood.

You can control the visualization of these points as well as access the positions of every detected feature point during mapping and localization.

## Controlling the feature visualization
To enable the feature point visualization, call this function at any time. The function will render all current points in the map.

``` csharp
FeaturesVisualizer.EnablePointcloud ();
```

You can stop the visualization and clear all rendered points like so.

``` csharp
FeaturesVisualizer.DisablePointCloud (); // stop generating new points
FeaturesVisualizer.clearPointcloud (); // clear existing points
```

## Accessing the point cloud
You can access the point cloud as a list of points of type `PNFeaturePointUnity`. This is a custom Placenote type and its class definition is as follows.

``` csharp
public struct PNFeaturePointUnity
{
  public int idx;
  public int measCount;
  public float maxViewAngle;
  public PNVector3Unity point;
}

public struct PNVector3Unity
{
  public float x;
  public float y;
  public float z;
}
```

You can access two lists of points in the Placenote map.

* List of all points in the map so far
* List of points being tracked in the current frame (subset of the above)

### Get all points in the map

``` csharp
PNFeaturePointUnity[] pointCloud = LibPlacenote.Instance.GetMap();
```

### Get currently tracked points

``` csharp
PNFeaturePointUnity[] pointCloud = LibPlacenote.Instance.GetTrackedFeatures();
```
