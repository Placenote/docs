# Saving and Loading a Map
In this tutorial, we will build a simple app that will let us map an area and save a sphere persistently in the same position. Alternatively, feel free to use the sample app you built in the [build sample app section](../install-sample.md) as a starting point for your Unity projects.

## Before you begin
This tutorial assumes basic knowledge of ARKit and Swift development. If you're new to ARKit, here are a few resources to get you up to speed:

* <a href="https://developer.apple.com/documentation/arkit/building_your_first_ar_experience" target="_blank">Building your first ARKit app (By Apple)</a>
* <a href="https://www.youtube.com/watch?v=f3xFpRWZEz8" target="_blank">Introduction to ARKit (first 15 minutes of the video)</a>

## Step 1: Create a new AR project and install Placenote
1. Create a new empty project in Xcode.

## Step 2: Build a basic ARKit app
We’ll assume you already have a simple working ARKit app that renders a sphere at the world origin (0,0,0) on the click of a UI Button. To set up this app, add a button to your Main Storyboard and within its *IBAction*, call this function to render a sphere.

``` objectivec
// Create a sphere at (0,0,0) and apply a diffuse green color to it

func renderSphere() {
    let geometry:SCNGeometry = SCNSphere(radius: 0.05) //units, meters
    let geometryNode = SCNNode(geometry: geometry)
    geometryNode.position = SCNVector3(x:0.0, y:0.0, z:0.0)
    geometryNode.geometry?.firstMaterial?.diffuse.contents = UIColor.green
    sceneView.scene.rootNode.addChildNode(geometryNode)
}
```
You will notice that each time you open the app the sphere gets rendered in a new position. This is because in ARKit, the point *(0,0,0)* refers to where the latest ARKit session was initiated. It was easy to create the sphere, but it is not persistent.

We can use Placenote SDK here to make the sphere (or any SCNode) appear in the same place every time.

## Step 2: Integrate Placenote SDK

Install Placenote into your project [using our installation instructions](installing-placenote.md).
In your *ViewController.swift* file, add the line `import PlacenoteSDK` to the top.

Make your ViewController class a delegate of *PNDelegate* by adding this to your class definition.

``` objectivec

class ViewController: UIViewController, ARSCNViewDelegate, ARSessionDelegate, PNDelegate {
```

As you add your ViewController to be a PNDelegate you will also need to add the following delegate functions to receive information from Placenote SDK. We’ll make use of these later.

``` objectivec
//Receive a pose update when a new pose is calculated  
func onPose(_ outputPose: matrix_float4x4, _ arkitPose: matrix_float4x4) -> Void

//Receive a status update when the status changes
func onStatusChange(_ prevStatus: LibPlacenote.MappingStatus, _ currStatus: LibPlacenote.MappingStatus) -> Void
```

## Step 3: Initialize Placenote SDK

Let’s modify our viewDidLoad() or viewDidAppear() function to initialize Placenote.

Within viewDidLoad(), add your ViewController to be the multiDelegate of PlacenoteSDK as the ViewController initializes.

``` objectivec
 LibPlacenote.instance.multiDelegate += self
```

Make sure you have also added ViewController as a session delegate for ARKit like this:

``` objectivec
 sceneView.session.delegate = self
```

Next, Placenote SDK has a utility called CameraManager that allows the PlacenoteSDK to manage the position of the ARKit Camera between ARKit Sessions. You activate it by simply declaring the object and passing in the ARKit Scene and Camera Object.

``` objectivec
//Initialize the camManager variable in your class
private var camManager: CameraManager? = nil;

//Add this to the end of your viewDidLoad function
if let camera: SCNNode = sceneView?.pointOfView {
    camManager = CameraManager(scene: sceneView.scene, cam: camera)
}
```

Finally, to visualize point clouds created by Placenote, you can initialize a feature point visualizer in the class like this

``` objectivec
private var ptViz: FeaturePointVisualizer? = nil;
```

and initialize it within your viewDidLoad() function.

``` objectivec
// Add this to your viewDidLoad() function
ptViz = FeaturePointVisualizer(inputScene: sceneView.scene);
ptViz?.enableFeaturePoints()
```

When it’s all put together, your viewDidLoad() or viewDidAppear() should look something like this

``` objectivec
override func viewDidLoad() {
    super.viewDidLoad()

    // configuration
    LibPlacenote.instance.multiDelegate += self

    // set up AR session
    self.sceneView.session.run(configuration)
    self.sceneView.autoenablesDefaultLighting = true
    self.sceneView.debugOptions = [ARSCNDebugOptions.showFeaturePoints]

    sceneView.session.delegate = self

    ptViz = FeaturePointVisualizer(inputScene: sceneView.scene);
    ptViz?.enableFeaturePoints()

    if let camera: SCNNode = sceneView?.pointOfView {
        camManager = CameraManager(scene: sceneView.scene, cam: camera)
    }

}
```

## Step 4: Mapping and Localizing
You are now ready to render your sphere and use PlacenoteSDK to remember where it was! Doing this is a simple 3 step process.

1. Start a Mapping Session and place content
2. Save the map and retrieve a relevant Map ID
3. Load a map with a Map ID and start a Localization Session.

After Step 3, PlacenoteSDK attempts to relocalize your device with with the loaded map and when it finds a match, it shifts the ARCamera to the correct position. Let’s look at how to get this working.

### Start a mapping session
Let’s first declare a variable to help us track the status of Placenote. We will use it through this example. Declare it like this in your ViewController class:

``` objectivec
private var placenoteSessionRunning: Bool = false
```

Now to start the mapping session, call startSession() and place the sphere where you want it. Set placenoteSessionRunning to true when you start a Placenote session.

``` objectivec
// put this within your buttons IBAction

LibPlacenote.instance.startSession()
renderSphere()
placenoteSessionRunning = true
```

Now during a running session, as we move the phone around Placenote will start scanning the area around you to build a map. To do this we must send Placenote the image frames coming from the camera. We’ll do this in the ARKit session call back. As a reminder, we initialized this using `sceneView.session.delegate = self` inside *viewDidLoad()*.

``` objectivec
// send AR frame to placenote
func session(_ session: ARSession, didUpdate: ARFrame) {

    let image: CVPixelBuffer = didUpdate.capturedImage
    let pose: matrix_float4x4 = didUpdate.camera.transform

    if (placenoteSessionRunning) {
        LibPlacenote.instance.setFrame(image: image, pose: pose)
        //print("sent placenote a frame")
    }
}
```

### Save the map

Next, you’ll want to save the map so we can recall it later. You do this by calling saveMap. In this example we will use the same UIButton to start and stop mapping. We’ll toggle the function of the button using the placenoteSessionRunning flag. So our final IBAction would be:

``` objectivec
@IBAction func buttonClick(_ sender: Any) {

     // start mapping session.
     if (!placenoteSessionRunning)
     {
       placenoteSessionRunning = true
       renderSphere()
       LibPlacenote.instance.startSession()
     }
     else
     {
       placenoteSessionRunning = false

       //save the map and stop session
       LibPlacenote.instance.saveMap(savedCb: { (mapID: String?) -> Void in
         print ("MapId: " + mapID!)
         LibPlacenote.instance.stopSession()  },

                                     uploadProgressCb: {(completed: Bool, faulted: Bool, percentage: Float) -> Void in
                                        print("Map Uploading...")
                                        if(completed){
                                          print("Map upload done!!!")
                                        }
        })
      }
}
```

Note how the saveMap function returns a unique Map ID. We just output it to the console here, but you could also save it programmatically or retrieve it later. The map is saved and uploaded to the cloud for access on any device running with your API Key.

### Reload a map

Now let’s look at how to retrieve the map everytime you turn on the app. For this example, let’s comment out everything we added to the IBAction function in the mapping step and make a call to loadMap() using the mapID we just generated. Once the loadMap() function returns successfully, you can again, start a session. This time, as you call on startSession() after loading a map, Placenote SDK will try to localize the old ARKit session for you.

``` objectivec
@IBAction func buttonClick(_ sender: Any) {

        if (!placenoteSessionRunning)
        {
            placenoteSessionRunning = true
            LibPlacenote.instance.loadMap(mapId: "Paste Map ID here",
                                          downloadProgressCb: {(completed: Bool, faulted: Bool, percentage: Float) -> Void in
                                            if (completed) {
                                                LibPlacenote.instance.startSession()
                                            }
            })
        }
}
```

For now, we just copy paste the Map ID as it was output to the console when we saved the map. However, in most circumstances you will save this mapID locally or on your app’s backend to retrieve any time.

Now, remember when you set up a delegate with a function called *onStatusChange()*? The moment PlacenoteSDK finds the map you created before, the status will change from ‘lost’ to ‘running’. When this happens, you can render the sphere exactly at (0,0,0) like this.

``` objectivec
func onStatusChange(_ prevStatus: LibPlacenote.MappingStatus, _ currStatus: LibPlacenote.MappingStatus) {
    if (prevStatus == LibPlacenote.MappingStatus.lost && currStatus == LibPlacenote.MappingStatus.running) {
        renderSphere()
        print("Map Found!")
    }          
}
```

Lets run this app in place now! No matter where we start, we find the sphere appearing in the exact same position we initially placed it.
