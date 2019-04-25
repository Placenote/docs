# Understanding Placenote Statuses

Let's dive into the `OnStatusChange()` callback function and understand the different Placenote statuses and how to handle them.

In the *RandomShapes* example, take a look at *PlacenoteSampleView.cs*. The `OnStatusChange()` function looks like this.

``` csharp
public void OnStatusChange (LibPlacenote.MappingStatus prevStatus,
                            LibPlacenote.MappingStatus currStatus) {  

}
```

Each time the Placenote session status changes, the `OnStatusChange()` function returns the current and previous status of the session. The return values are an enum of type `LibPlacenote.MappingStatus`. The complete list of statuses is:

``` csharp
public enum MappingStatus
{
  WAITING, // no mapping/localization session is running at the moment
  RUNNING, // mapping/localization session is currently running
  LOST // could not relocalize a map during localization
}
```

We will use the change in status from previous to current to identify what state our Placenote session is currently in. Let's look at some examples.

## Localization success status
The most commonly used status change is the `LOST` to `RUNNING` change, which signifies that Placenote has successfully localized a map. We check for this status using an if statement within the `OnStatusChange()` function as follows.

``` csharp
if (currStatus == LibPlacenote.MappingStatus.RUNNING && prevStatus == LibPlacenote.MappingStatus.LOST) {

    // localization successful
}
```

This is a good time to load any persistent content into your scene. Keep in mind though, that Placenote will repeatedly switch between `LOST` and `RUNNING` through the localized session since it is constantly checking for tracked features in the camera frame.

When triggering your content to render after a successful localization, you will need to ensure that you only render the content once, after the first status change from `LOST` to `RUNNING`. We recommend using a flag like this:

``` csharp
// in your class variable definitions
localizedFirstTime = false;

// in the OnStatusChange() function
if (currStatus == LibPlacenote.MappingStatus.RUNNING && prevStatus == LibPlacenote.MappingStatus.LOST)
{
   if (localizedFirstTime = false)
   {
        localizedFirstTime = true;
        renderYourSceneHere();
   }
}
```
