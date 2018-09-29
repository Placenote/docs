# Migrating to SDK 1.6.8 from an Earlier Version

SDK 1.6.8 includes a change that simplifies the SDK interface for Unity developers, to make is easier to get started with Placenote development. This change is a **rare breaking change** for Unity developers and we have included documentation below on how to update your app for 1.6.8.

!!! warning
    This must only be done after you update your SDK to 1.6.8 or later. [Download the latest version of the SDK here](https://placenote.com/unitysample). You can find release notes on the [Placenote Unity github page](https://github.com/Placenote/PlacenoteSDK-Unity/releases).

## Migration Steps
If you have built your project with an earlier version of Placenote, here are the steps to move your app to the latest Placenote version.

**Step 1.** In your main controller script, find the area where you defined these variables, and **delete** these lines.

``` csharp
private bool mFrameUpdated = false;
private UnityARImageFrameData mImage = null;
private UnityARCamera mARCamera;
```

**Step 2.** Similarly, look for this definition and **delete** it.

``` charp
UnityARSessionNativeInterface.ARFrameUpdatedEvent += ARFrameUpdated;
```

**Step 3.** **Delete** this function

``` csharp
private void ARFrameUpdated (UnityARCamera camera)
{
    mFrameUpdated = true;
    mARCamera = camera;
}
```

**Step 4.** Your update function will contain this code. **Delete** everything within and including the `if(MframeUpdated){}` block.

``` csharp
void Update () {
    if (mFrameUpdated) {
        mFrameUpdated = false;
        if (mImage == null) {
            InitARFrameBuffer ();
        }

        if (mARCamera.trackingState == ARTrackingState.ARTrackingStateNotAvailable) {
            // ARKit pose is not yet initialized
            return;
        }

        Matrix4x4 matrix = mSession.GetCameraPose ();
        Vector3 arkitPosition = PNUtility.MatrixOps.GetPosition (matrix);
        Quaternion arkitQuat = PNUtility.MatrixOps.GetRotation (matrix);

        LibPlacenote.Instance.SendARFrame (mImage, arkitPosition, arkitQuat, mARCamera.videoParams.screenOrientation);
    }
}
```

**Step 5.** **Delete** the `InitARFrameBuffer()` function that looks like this.

``` csharp
private void InitARFrameBuffer ()
{
    mImage = new UnityARImageFrameData ();

    int yBufSize = mARCamera.videoParams.yWidth * mARCamera.videoParams.yHeight;
    mImage.y.data = Marshal.AllocHGlobal (yBufSize);
    mImage.y.width = (ulong)mARCamera.videoParams.yWidth;
    mImage.y.height = (ulong)mARCamera.videoParams.yHeight;
    mImage.y.stride = (ulong)mARCamera.videoParams.yWidth;

    // This does assume the YUV_NV21 format
    int vuBufSize = mARCamera.videoParams.yWidth * mARCamera.videoParams.yWidth/2;
    mImage.vu.data = Marshal.AllocHGlobal (vuBufSize);
    mImage.vu.width = (ulong)mARCamera.videoParams.yWidth/2;
    mImage.vu.height = (ulong)mARCamera.videoParams.yHeight/2;
    mImage.vu.stride = (ulong)mARCamera.videoParams.yWidth;

    mSession.SetCapturePixelData (true, mImage.y.data, mImage.vu.data);
}
```

And that's it! You have successfully migrated to the latest Placenote. As you can see, you do not need the above code to work with Placenote anymore. This is a major simplification of the SDK interface and although this is a breaking change, it will speed up development and reduce errors significantly when working with Placenote. Thanks for your understanding and if you have any questions regarding this change, please reach out to us on the [Placenote slack community](https://placenote.com/slack). 
