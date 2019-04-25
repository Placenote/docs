# Creating a new Placenote iOS app
In this project we will set up a brand new ARKit app in XCode and add Placenote to the project. You can also use these instructions to add Placenote to an existing Xcode project.

##


This tutorial will guide you through an example of how to integrate Placenote SDK into a native iOS app. After you install Placenote, make sure you continue to the next tutorial where we build a simple persistent AR app using Placenote. We will use *Swift* as our language and *SceneKit* to render 3D content.

In this tutorial we will show you 2 ways to add Placenote to your *Swift* project.

1. Install using Cocapods
2. Install without Cocoapods

!!! note
    Currently, we do not have an installation example for *Objective C* projects but some developers have implemented this themselves. Feel free to [message us on slack to ask about it](https://placenote.com/slack).

## Install with Cocoapods

### Configure your Pod file
1. If you aren’t already using CocoaPods, [follow the directions here](https://guides.cocoapods.org/using/getting-started.html) to install it.
2. In your project folder run the command  pod init to create the initial Podfile.
3. Open your Podfile and add the line `pod 'PlacenoteSDK'` within your target’s do statement.
4. We recommend automatically disabling bitcode by adding this to the end of your Podfile:

``` python
post_install do |installer|
   installer.pods_project.targets.each do |target|
       target.build_configurations.each do |config|
           config.build_settings['ENABLE_BITCODE'] = 'NO'
       end
   end
end
```

### Install the Pod
1. Run `pod install` to install the Placenote SDK pod.
2. You will notice that your project now has a *.xcworkspace* file in addition to your original *.xcodeproj* file. Open the *.xcworkspace* file to open your project in Xcode.
3. Now add your API key, to your *AppDelegate.swift* file like this.

``` objectivec
// add this to the top of your file
import PlacenoteSDK

// initialize PlacenoteSDK in your application function like this
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

    LibPlacenote.instance.initialize(apiKey: "Enter your API key here")
    return true
}
```

## Install without Cocoapods

!!! note
    Coming soon! [Contact us on slack](https://placenote.com/slack) to find out more.


## Building and Running your app
Go through the [build instructions checklist](../build-instructions.md) to ensure you build your app correctly.

## Next Steps
This process should install your ARKit project with an integrated Placenote SDK onto your device. Of course it will not do anything since we haven’t actually written any code yet but you have set up the infrastructure you need to start working with Placenote!

For your next step, go through the tutorial on building a [basic app to save and load a map](save-and-load-map.md).
