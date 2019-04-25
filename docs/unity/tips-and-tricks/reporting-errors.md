# Reporting errors
Placenote is a persistent AR SDK with one of the most advanced computer vision systems available on mobile devices today. However, working with cutting edge technology means that you will occasionally find problems with mapping, localization and tracking in the form of failed localizations or tracking breakages.

To make issues easier to resolve, we have included functions in Placenote that will allow you to send us an error report containing a dataset of your device session. This will let us specifically debug and observe your issue through a live dataset.

To report issues, you can include this block of code just below your call to `LibPlacenote.Instance.StartSession ()` during both mapping and localization. You can find an example of this function in *PlacenoteSampleView.cs* in the *RandomShapes* example.

``` csharp
LibPlacenote.Instance.StartRecordDataset (
  (datasetCompleted, datasetFaulted, datasetPercentage) => {

    if (datasetCompleted) {
      // Dataset Upload Complete
    } else if (datasetFaulted) {
      // Dataset Upload Faulted
    } else {
      // datasetPercentage is the upload percentage (0-1)
    }
  });
```

Calling `StartRecordDataset()` after `StartSession()` will automatically upload the dataset to Placenote when you call `SaveMap()`. You will receive the dataset upload callback after the map upload callback and you do not need to change your `SaveMap()` function at all.

!!! warning
    Please do not upload datasets for every session. The size of datasets can be large since they contain images. (>1GB sometimes). Place a flag that you can set whenever you want to upload datasets and use it sparingly. See how we use the `mReportDebug` flag in *PlacenoteSampleView.cs*.
