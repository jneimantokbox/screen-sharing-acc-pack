![logo](../tokbox-logo.png)

# OpenTok Screensharing Accelerator Pack for Android<br/>Version 1.1.0

## Quick start

This section shows you how to:
* Examine a sample app to see how it uses this accelerator pack.
* Import the accelerator pack into a project you are developing.


### Examine how the sample app uses the acc pack

To learn how to use the acc pack, download and examine a sample app project that uses the the OpenTok Screensharing Accelerator Pack:

1. Download or clone the [OpenTok Screensharing with Annotations Sample repo](https://github.com/opentok/one-to-one-screen-annotations-sample-apps).
1. In Android Studio's **Quick Start** panel, click **Open an existing Android Studio Project**.
1. Open the `*./one-to-one-screen-annotations-sample-apps/android/OneToOneScreenSharingSample/build.gradle` file.
1. Open the MainActivity.java file and search for "ScreenSharing".

### Add the Screensharing Acc Pack to a project

Use one of the following options to install the OpenTok Screensharing Accelerator Pack library:

  - [Using the repository](#using-the-repository)
  - [Using Maven](#using-maven)
  - [Downloading and Installing the AAR File](#downloading-and-installing-the-aar-file)

#### Using the repository

1. Clone the [OpenTok Screensharing Acc Pack](https://github.com/opentok/screen-sharing-acc-pack).
1. In Android Studio's **Quick Start** panel, click *Import project*.
1. Import the `*/one-to-one-screen-annotations-sample-apps/android/ScreensharingAccPackKit/build.gradle` file.
1. Open the **build.gradle** file for the app and ensure the following lines have been added to the `dependencies` section:
```
compile project(':screensharing-acc-pack-kit')
```

#### Using Maven

1. Modify the `build.gradle` for your solution and add the following code snippet to the section labeled 'repositories’:

`maven { url  "http://tokbox.bintray.com/maven" }`

1. Modify the `build.gradle` for your activity and add the following code snippet to the section labeled 'dependencies’:

`compile 'com.opentok.android:opentok-screensharing-annotations:1.1.0'`

  _**NOTE**: Since dependencies are transitive with Maven, it is not necessary to explicitly reference the TokBox Common Accelerator Session Pack with this option._


#### Downloading and Installing the AAR File

1.  Download the [Screensharing Accelerator Pack zip file](https://s3.amazonaws.com/artifact.tokbox.com/solution/rel/screensharing-acc-pack-kit/android/opentok-screensharing-acc-pack-kit-1.1.0.zip) containing the AAR file and documentation.
1. Extract the **opentok-screensharing-annotations-1.1.0.aar** file.
1. Right-click the app name and select **Open Module Settings** and click **+**.
1.  Select **Import .JAR/.AAR Package** and click  **Next**.
1.  Browse to the **Screensharing Accelerator Pack library AAR** and click **Finish**.


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
| `ScreenSharingFragment`   | Provides the initializers and methods for the client screensharing views. |
| `ScreenSharingCapturer`   | Provides TokBox custom support for sharing content displayed in the screensharing area, overriding the mobile device OS default to share content captured by the camera. |
| `ScreenSharingBar`   | Initializes the screensharing toolbar and its UI controls. |
| `ScreenPublisher`   | Provides TokBox support for displaying shared screen content. |

The screensharing toolbar appears when you call `start()`. You can customize the `ScreenSharingBar` by overriding the `ScreenSharingBar` and `ScreenSharingBarListener` classes, or by directly modifying them. Note that this ScreenSharing library allows you to share the app screen but doesn't allow you to share the device screen.


###  Screensharing Accelerator Pack

The `ScreenSharingFragment` class is the backbone of the screensharing features for the app. It serves as a controller for the screensharing UI widget and initializes such functionality as stroke color and scrolling features. It uses the [`android.media.projection.MediaProjection`](http://developer.android.com/reference/android/media/projection/MediaProjection.html), supported on Android Lollipop, and provides the projection callbacks needed for screensharing.

This class, which inherits from the [`android.support.v4.app.Fragment`](http://developer.android.com/intl/es/reference/android/support/v4/app/Fragment.html) class and is implemented in the app using the library, sets up the screensharing UI views and events, sets up session listeners, and defines a listener interface.

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
    void onAnnotationsViewReady(AnnotationsView view);
    void onClosed();

}
```

Note that the screensharing library contains methods to add screensharing features to your apps, as well as the option to enable annotations in the screensharing features.

#### Initialization methods

The following `ScreenSharingFragment` methods are used to initialize the app and provide basic information determining the behavior of the screensharing functionality.

| Feature        | Methods  |
| ------------- | ------------- |
| Start screen capture.   | `start()`  |
| Stop screen capture.  | `stop()`  |
| Set the listener object to monitor state changes.   | `setListener()` |
| Check whether screensharing has started. Return `true` if screensharing started, `false` otherwise. | `isStarted()` |
| Enable or disable the annotations in the screensharing. | `enableAnnotations(boolean annotationsEnabled, AnnotationsToolbar toolbar)` |
| Enable or disable the audio in the screensharing. | `enableAudioScreensharing(boolean enabled)` |


### User interface

As described in [Class design](#class-design), the `ScreenSharingFragment` class sets up and manages the UI views, events, and rendering for the screensharing controls. The screensharing bar can be customized, and the different Screensharing callbacks allow to the app to manage the status changes using the screensharing kit. For example, status changes can indicate when the app has started or ended. You can adapt the UI in the sample-app to implement these changes.

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

**Note**: OpenTok Screensharing requires Android Lollipop or later.
