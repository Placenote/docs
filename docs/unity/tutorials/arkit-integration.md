# Integrate with ARKit features
When using Placenote, you can continue to use all ARKit functionality to interact with the environment, such as Hit Testing and Plane detection. In this tutorial, we will use the sample scene **ARKitHitTesting** as a reference.

## What we're building in this tutorial
The *ARKitHitTesting* example extends the *SaveMetaData* sample by changing the way 3D objects are instantiated in the scene. We will use an ARKit Hit Test to add markers to various points in the environment using a tap gesture on the screen.

## How to perform an ARKit Hit Test
An ARKit HitTest uses a ray cast to find the closest intersection with any surface around us. Take a look at *MarkerManager.cs*. In the `Update()` function we check if the screen is touched and if so, we create two variables like this.

``` csharp
ARPoint point = new ARPoint
{
    x = screenPosition.x,
    y = screenPosition.y
};

// prioritize results types
ARHitTestResultType[] resultTypes = {
  ARHitTestResultType.ARHitTestResultTypeExistingPlaneUsingExtent, // plane hittest
  ARHitTestResultType.ARHitTestResultTypeFeaturePoint // feature hittest
};
```

Now we perform the hittest within the `HitTestWithResultType()` function using the ARKit `HitTest(point, resultTypes)` function.

## Transforming ARKit to the Placenote coordinate frame
The most crucial part of integrating Placenote with ARKit Hit Testing is transforming a hit test pose result into the Placenote coordinate frame. Remember that ARKit initializes the coordinate frame relative to the device's starting position, while Placenote uses a persistent real-world coordinate frame. Unless you transform the ARKit Hit Test pose result, objects will instantiate in the wrong positions.

Placenote includes a helper function to make it easy to transform poses. Given a `ARHitTestResult`, the pose transformation is done as follows.

``` csharp
Vector3 position = UnityARMatrixOps.GetPosition(hitResult.worldTransform);
Quaternion rotation = UnityARMatrixOps.GetRotation(hitResult.worldTransform);

//Transform to placenote frame of reference (planes are detected in ARKit frame of reference)
Matrix4x4 worldTransform = Matrix4x4.TRS(position, rotation, Vector3.one);
Matrix4x4? placenoteTransform = LibPlacenote.Instance.ProcessPose(worldTransform);

Vector3 hitPosition = PNUtility.MatrixOps.GetPosition(placenoteTransform.Value);
Quaternion hitRotation = PNUtility.MatrixOps.GetRotation(placenoteTransform.Value);
```
