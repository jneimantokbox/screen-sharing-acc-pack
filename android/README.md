![logo](../tokbox-logo.png)

# OpenTok Screensharing Accelerator Pack for Android<br/>Version 2.0.0

## Quick start

This section shows you how to:
* Examine a sample app to see how it uses this accelerator pack.
* Import the accelerator pack into a project you are developing.

**Note**: OpenTok Screensharing requires Android Lollipop or later.


### Examine how the sample app uses the acc pack

To learn how to use the acc pack, download and examine a sample app project that uses the the OpenTok Screensharing Accelerator Pack: 

1. Download or clone the Clone the [OpenTok Screensharing with Annotations Sample repo](https://github.com/opentok/screensharing-annotation-acc-pack).
1. In Android Studio's **Quick Start** panel, click **Open an existing Android Studio Project**.
1. Open the `*./one-to-one-screen-annotations-sample-apps-master/android/OneToOneScreenSharingSample/build.gradle` file.
1. Open the MainActivity.java file and search for "ScreenSharing".

This sample app contains the `*.class` file versions of the following source files in the acc pack:
* ScreenPublisher.java 
* ScreenSharingBar.java 
* ScreenSharingCapturer.java 
* ScreenSharingFragment.java 

To see the decompiled `*.class` files:
1. In Android Studio, click the **Project** view.
2. Expand **External Libraries > opentok-screensharing-annotations-* > classes.jar > com.tokbox.android.accpack.screensharing > config**
3. Open the *.class files you want to see. Android Studio decompiles and displays the file in the main window.

To see the original *.java source files, download and open the acc pack in Android Studio. 

### Add the Screensharing Acc Pack to a project

Use one of the following options to install the OpenTok Screensharing Accelerator Pack library:

  - [Using the repository](#using-the-repository)
  - [Using Maven](#using-maven)
  - [Downloading and Installing the AAR File](#downloading-and-installing-the-aar-file)

#### Using the repository

1. Clone the [OpenTok Screensharing Acc Pack repository](https://github.com/opentok/screen-sharing-acc-pack).
1. In Android Studio's **Quick Start** panel, click *Import project*.
1. Import the `*/one-to-one-screen-annotations-sample-apps-master 3/android/ScreensharingAccPackKit/build.gradle` file.
1. Open the **build.gradle** file for the app and ensure the following lines have been added to the `dependencies` section:
```
compile project(':screensharing-acc-pack-kit')
```

#### Using Maven

1. Modify the `build.gradle` for your solution and add the following code snippet to the section labeled 'repositories’:

`maven { url  "http://tokbox.bintray.com/maven" }`

1. Modify the `build.gradle` for your activity and add the following code snippet to the section labeled 'dependencies’:

`compile 'com.opentok.android:screensharing-acc-pack-kit:2.0.0'`

  _**NOTE**: Since dependencies are transitive with Maven, it is not necessary to explicitly reference the TokBox Common Accelerator Session Pack with this option._


#### Downloading and Installing the AAR File

1.  Download the [Screensharing Accelerator Pack zip file](https://s3.amazonaws.com/artifact.tokbox.com/solution/rel/screensharing-acc-pack-kit/android/opentok-screensharing-acc-pack-kit-2.0.0.zip) containing the AAR file and documentation.
1. Extract the **opentok-screensharing-acc-pack-kit-2.0.0.aar** file.
1. Right-click the app name and select **Open Module Settings** and click **+**.
1.  Select **Import .JAR/.AAR Package** and click  **Next**.
1.  Browse to the **Screensharing Accelerator Pack library AAR** and click **Finish**.

### Configuring the app

Add the following configuration details to your app. These will include the **Session ID**, **Token**, and **API Key** you retrieved from your Developer dashboard. For more information, see [OpenTok Screensharing Accelerator Pack](../)

In **OpenTokConfig.java**, replace the following empty strings with the required detail:


```java
// Replace with a generated Session ID
public static final String SESSION_ID = "";

// Replace with a generated token
public static final String TOKEN = "";

// Replace with your OpenTok API key
public static final String API_KEY = "";
```

You may also set the `SUBSCRIBE_TO_SELF` constant. Its default value, `false`, means that the app subscribes automatically to the other client’s stream. This is required to establish communication between two streams using the same Session ID:

```java
public static final boolean SUBSCRIBE_TO_SELF = false;
```

You can enable or disable the `SUBSCRIBE_TO_SELF` feature by invoking the `OneToOneCommunication.setSubscribeToSelf()` method:

```java
OneToOneCommunication comm = new OneToOneCommunication(
  MainActivity.this,
  OpenTokConfig.SESSION_ID,
  OpenTokConfig.TOKEN,
  OpenTokConfig.API_KEY
);

comm.setSubscribeToSelf(OpenTokConfig.SUBSCRIBE_TO_SELF);

```
_At this point you can try running the app! You can either use a simulator or an actual mobile device._


## Exploring the code

This section describes how the sample app code design uses recommended best practices to deploy the screensharing features.

For detail about the APIs used to develop this sample, see the [OpenTok Android SDK Reference](https://tokbox.com/developer/sdks/android/reference/) and [Android API Reference](http://developer.android.com/reference/packages.html).

  - [Class design](#class-design)
  - [Screensharing Accelerator Pack](#screensharing-accelerator-pack)
  - [User interface](#user-interface)

_**NOTE:** The sample app contains logic used for logging. This is used to submit anonymous usage data for internal TokBox purposes only. We request that you do not modify or remove any logging code in your use of this sample application._

### Class design

The following classes represent the software design for this sample app, focusing primarily on the screensharing features.

| Class        | Description  |
| ------------- | ------------- |
| `MainActivity`    | Implements the sample app UI and screensharing callbacks. |
| `OpenTokConfig`   | Stores the information required to configure the session and connect to the cloud.   |
| `ScreenSharingFragment`   | Provides the initializers and methods for the client screensharing views. |
| `ScreenSharingCapturer`   | Provides TokBox custom support for sharing content displayed in the screensharing area, overriding the mobile device OS default to share content captured by the camera. |
| `ScreenSharingBar`   | Initializes the screensharing toolbar and its UI controls. |
| `ScreenPublisher`   | Provides TokBox support for displaying shared screen content. |


###  Screensharing Accelerator Pack

The `ScreenSharingFragment` class is the backbone of the screensharing features for the app. It serves as a controller for the screensharing UI widget and initializes such functionality as stroke color and scrolling features. It uses the [`android.media.projection.MediaProjection`](http://developer.android.com/reference/android/media/projection/MediaProjection.html), supported on Android Lollipop, and provides the projection callbacks needed for screensharing.

This class, which inherits from the [`android.support.v4.app.Fragment`](http://developer.android.com/intl/es/reference/android/support/v4/app/Fragment.html) class, sets up the screensharing UI views and events, sets up session listeners, and defines a listener interface that is implemented in this example by the `MainActivity` class.

```java
public class ScreenSharingFragment
        extends    Fragment
        implements AccPackSession.SessionListener,
                   PublisherKit.PublisherListener,
                   AccPackSession.SignalListener,
                   ScreenSharingBar.ScreenSharingBarListener {

    . . .

}
```

The `ScreenSharingListener` interface monitors state changes in the `ScreenSharingFragment`, and defines the following methods:

```java
public interface ScreenSharingListener {

    void onScreenSharingStarted();
    void onScreenSharingStopped();
    void onScreenSharingError(String error);
    void onClosed();

}
```


#### Initialization methods

The following `ScreenSharingFragment` methods are used to initialize the app and provide basic information determining the behavior of the screensharing functionality.

| Feature        | Methods  |
| ------------- | ------------- |
| Start screen capture.   | `start()`  |
| Stop screen capture.  | `stop()`  |
| Set the listener object to monitor state changes.   | `setListener()` |


#### Setting the Screensharing Toolbar

To set up your screensharing toolbar, instantiate a `ScreenSharingFragment` object. For example, the following private method instantiates a `ScreenSharingFragment` object:

```java
    private void initScreenSharingFragment(){

        mScreenSharingFragment = ScreenSharingFragment.newInstance(
          mComm.getSession(),
          OpenTokConfig.API_KEY
        );

        mScreenSharingFragment.setListener(this);

        getSupportFragmentManager().beginTransaction()
                .add(
                      R.id.screensharing_fragment_container,
                      mScreenSharingFragment
                ).commit();
    }
```



### User interface

As described in [Class design](#class-design), the `ScreenSharingFragment` class sets up and manages the UI views, events, and rendering for the screensharing controls.

This class works with the following `MainActivity` methods, which manage the views as both clients participate in the session.

| Feature        | Methods  |
| ------------- | ------------- |
| Manage the UI containers. | `onCreate()`  |
| Reload the UI views whenever the device [configuration](http://developer.android.com/reference/android/content/res/Configuration.html), such as screen size or orientation, changes. | `onConfigurationChanged()`  |
| Opens and closes the screensharing view. | `onScreenSharing()` |
| Manage the customizable views for the action bar, screensharing, and annotation callbacks.   | `onScreenSharingStarted()`, `onScreenSharingStopped()`, `onScreenSharingError()`,  `onClosed()`|

## Requirements

To develop a screensharing app:

1. Install [Android Studio](http://developer.android.com/intl/es/sdk/index.html).
2. Review the [OpenTok Android SDK Requirements](https://tokbox.com/developer/sdks/android/#developerandclientrequirements).
