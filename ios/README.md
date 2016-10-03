![logo](../tokbox-logo.png)

# OpenTok Screensharing Accelerator Pack for iOS<br/>Version 2.0.0

## Quick start

This section shows you how to:
* Examine a sample app to see how it uses this accelerator pack.
* Import the accelerator pack into a project you are developing.

### Examine how the sample app uses the acc pack

To learn how to use the acc pack, download and examine a sample app project that uses the the OpenTok Screensharing Accelerator Pack: 

1. Download or clone the Clone the [OpenTok Screensharing with Annotations Sample repo](https://github.com/opentok/screensharing-annotation-acc-pack).
1. In a terminal prompt, navigate to your project directory and type `pod install`.
2. Reopen your project using the new `*.xcworkspace` file.

For more information about CocoaPods, including installation instructions, visit [CocoaPods Getting Started](https://guides.cocoapods.org/using/getting-started.html#getting-started).

_At this point you can try running the app! You can either use a simulator or an actual mobile device._

### Configuring the app

Now you are ready to add the configuration detail to your app. For more information about getting this information, see [OpenTok Screensharing Accelerator Pack](../)

In **AppDelegate.h**, replace the following empty strings with the required detail:


```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

	  // Override point for customization after application launch.    
    [OTScreenSharer setOpenTokApiKey:@""
                           sessionId:@""
                               token:@""];
  	return YES;
}
```

_At this point you can try running the app! You can either use a simulator or an actual mobile device._

## Exploring the code

This section describes how the sample app code design uses recommended best practices to deploy the screensharing features. 

For detail about the APIs used to develop this sample, see the [OpenTok iOS SDK Reference](https://tokbox.com/developer/sdks/ios/reference/).

  - [App design](#app-design)
  - [Screensharing features](#screensharing-features)

_**NOTE:** The sample app contains logic used for logging. This is used to submit anonymous usage data for internal TokBox purposes only. We request that you do not modify or remove any logging code in your use of this sample application._

### App design

The following classes, interfaces, and protocols represent the software design for this sample app, focusing primarily on the screensharing features. 

| Class        | Description  |
| ------------- | ------------- |
| `MainViewController`   | In conjunction with **Main.storyboard**, this class uses the OpenTok API to initiate the client connection to the OpenTok session, and implements the sample UI and screensharing callbacks.   |
| `OTScreenSharer`   | This component enables the publisher to share either the entire screen or a specified portion of the screen. |
| `OTAnnotationScrollView` | Provides the initializers and methods for the client annotating views. |
| `OTAnnotationToolbarView`   | A convenient annotation toolbar that is optionally available for your development. As an alternative, you can create your toolbar using `OTAnnotationScrollView`. See the [OpenTok Annotations Accelerator Pack](https://github.com/opentok/annotation-acc-pack) for more information. |
| `OTFullScreenAnnotationViewController`   | Combines both the scroll toolbar views. See the [OpenTok Annotations Accelerator Pack](https://github.com/opentok/annotation-acc-pack) for more information. |


### Screensharing features

The `OTScreenSharer` and `OTAnnotationScrollView` classes are the backbone of the screensharing features for the app.

```objc
@interface OTScreenSharer : NSObject

@property (readonly, nonatomic) BOOL isScreenSharing;

+ (instancetype)screenSharer;
+ (void) setOpenTokApiKey:(NSString *)apiKey
               sessionId:(NSString *)sessionId
                   token:(NSString *)token;

- (void)connectWithView:(UIView *)view;
- (void)connectWithView:(UIView *)view
                handler:(ScreenShareBlock)handler;
- (void)disconnect;
```

```objc
@interface OTAnnotationScrollView : UIView

@property (nonatomic, getter = isAnnotating) BOOL annotating;
@property (nonatomic, getter = isZoomEnabled) BOOL zoomEnabled;

- (instancetype)init;
- (instancetype)initWithFrame:(CGRect)frame;
- (void)addContentView:(UIView *)view;  // this will enable scrolling if image is larger than actual device screen

@property (readonly, nonatomic) OTAnnotationToolbarView *toolbarView;
- (void)initializeToolbarView;

#pragma mark - annotation
- (void)startDrawing;
@property (nonatomic) UIColor *annotationColor;
- (void)addTextAnnotation:(OTAnnotationTextView *)annotationTextView;
- (UIImage *)captureScreen;
- (void)erase;
- (void)eraseAll;

@end
```



#### Initialization methods

The following `OTScreenSharer` and `OTAnnotationScrollView` methods are used to initialize the screensharing features so the client can annotate their sharing screen.

| Feature        | Methods  |
| ------------- | ------------- |
| Initialize the annotation view  | `initWithFrame:` |
| Initialize the connection for screen sharing | `connectWithView:handler:` |


```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    UIImage *image = [UIImage imageNamed:@"mvc"];
    UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
    imageView.frame = CGRectMake(0, 0, image.size.width, image.size.height);

    self.screenShareView = [[OTAnnotationScrollView alloc] initWithFrame:CGRectMake(0, 0, CGRectGetWidth([UIScreen mainScreen].bounds), CGRectGetHeight([UIScreen mainScreen].bounds))];
    [self.screenShareView addContentView:imageView];
    [self.view addSubview:self.screenShareView];
}

- (void)shareTheWholeScreen {
    [self.screenSharer connectWithView:[UIApplication sharedApplication].keyWindow.rootViewController.view handler:^(ScreenShareSignal signal, NSError *error) {

        if (!error) {
            // begin sharing screen
        }
        else {
            // error with screen sharing
        }
    }];
}
```

## Requirements

To develop your screensharing app:

1. Install Xcode version 5 or later.
2. Review the [OpenTok iOS SDK Requirements](https://tokbox.com/developer/sdks/ios/).

_You do not need the OpenTok iOS SDK to use this sample._
