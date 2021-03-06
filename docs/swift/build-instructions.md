# Build Instructions
Use this checklist whenever you are building a Placenote app. You can also use this as a checklist when troubleshooting any build issues you might face. If you're still having trouble building your app, <a href="https://placenote.com/slack" target="_blank">send us a message on slack</a>.

## Build Checklist

* Under your project’s *'Build Settings’* set *‘Enable Bitcode’* to ***‘NO’***.
* Like any ARKit app, make sure you have added a *‘Privacy – Camera Usage Description’* and a *‘Privacy – Location Usage Description’* to your Info.plist file. Add some text like "For AR" in the descriptions.
* Make sure your Deployment Target is set to iOS 11 or higher.
* Plug in your iOS device, select it as your target device and build. If everything goes well you should see the app running on your device. This should function exactly like the demo app you installed in [Try the sample app](install-sample.md).

## Troubleshooting common errors

Here are a few troubleshooting tips if you're seeing compiler or linker errors while trying to build Placenote.

| Error | How to fix it |
| ----------- | --------- |
| "Built for unsupported file format" | Check that you have the Placenote binary in your project. In your project folder, navigate to Pods/PlacenoteSDK and check the size of the *Placenote.framework* package. It should exist and should be > 6MB. If it's smaller, you don't have the right library. Try the following fixes: <ul><li><b>If you're building the Sample App:</b> Make sure you download the app bundle from the [link on this page](install-sample.md). Do not download a zip file from github because it does not contain the core binaries.</li><li><b>If you're using the Placenote cocoapod: </b> Make sure you have *git lfs* installed on your system before installing the cocoapod. <a href="https://git-lfs.github.com/" target="_blank">Instructions on installing git lfs are here</a>.</li> |
| "Placenote.framework/Placenote' does not contain bitcode" | Go to your *Build Settings* tab in XCode and check that *Enable Bitcode* (it's under *Build Options*) is set to **NO** |
|App crashes on opening | Check that *Privacy - Camera Usage Description* and *Privacy - Location Usage Description" fields exist in the Info.plist under the Info tab. |
| Other tips | Make sure you're building to a device. You cannot build ARKit apps to an XCode simulator.  |

Still having issues? <a href="https://placenote.com/slack" target="_blank">Join our slack community</a> to get help.
