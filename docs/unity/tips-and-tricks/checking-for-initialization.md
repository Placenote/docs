# Checking for initialization

In general, it is good practice to check that LibPlacenote has been initialized before any call you make to Placenote. Placenote initializes the SDK with your API key at the start of the app session and you will need an active internet connection to run any Placenote function.

You can check for initialization at the start of any function as follows.

``` csharp
if (!LibPlacenote.Instance.Initialized()) {
  Debug.Log ("SDK not yet initialized");
  return;
}
```
