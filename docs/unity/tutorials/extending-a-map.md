# Extending a previously created map

We are continuing to use the **SaveAndLoadAMap** scene as a reference for this section.

Placenote gives you the ability to extend a previously created map, after a successful localization. To extend a map you need to change the `LoadMap()` function by calling `LibPlacenote.Instance.StartSession(true)` within the `complete` block of the download progress callback. Here's an example:

``` csharp
LibPlacenote.Instance.LoadMap (selectedMapID,
    (completed, faulted, percentage) => {

        if (completed) { // finished downloading the map

          LibPlacenote.Instance.StartSession(true); // true flag extends map
        }
        else if (faulted) { // download failed
        }
        else { // download percentage (0-1)
        }
    }
);
```

When `StartSession(true)` is called Placenote begins a new mapping session after a successful localization. We still activate our content in the same way in the OnStatusChange function when `LibPlacenote.MappingStatus` changes to `RUNNING`.

However, we now need a SaveMap button to re-save the map that is being extended. SaveMap can be called in the exact same way as before. Placenote will save the extended map as a new map and return a fresh map ID from the server.

!!! question "Coding Challenge"
    Try modifying the SaveAndLoadAMap example app to extend maps after loading and re-save them. You will need to make the `StartSession(true)` change, as well as make the Save Map button visible upon localization. If you have any doubts, feel free to <a href="https://placenote.com/slack" target="_blank">ask us a question on slack</a>.
