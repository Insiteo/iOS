# Room Counting

## Requirements

> **Important**: In order to use the room counting feature, your application must enable the  [iBeacon configuration](beacon.md). Also keep in mind that users must have **enabled their bluetooth** on their phone to detect iBeacon. 

Room counting is based on iBeacon location service and Insiteo analytics. You must have intialized the SDK properly at least once with analytics enabled for the system to work (`.plist` configuration or using initialization parameters). Analytics configuration and iBeacon regions are retrieved from the server and stored in your client configuration.


## 1. Best Practices

The best way to report background location data through Insiteo SDK is to initialize the SDK as soon as possible. Indeed, when your application is woken up from iBeacon service, the OS starts your application for ~10 seconds in the background. The Insiteo class provides a local initialization method `-initializeLocallyWithLastConfiguration` which uses your last known configuration to avoid server synchronization that could take a long time. You don't need to start an Insiteo site.

> **Note:** Such as with standard initialization methods, a full local initialization method with parameters is available.


## 2. Usage

- 1. Start the Beacon Provider as soon as possible in `application:didFinishLaunchingWithOptions:`

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    ...
    // Start Beacon Provider with nil or delegate.
    [[ISBeaconProvider sharedInstance] startWithDelegate:nil];
    ...
    return YES;
}
```

- 2. Detect if the application is woken up from location service (in the background):

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    ...
    // Typically from iBeacon region entry/exit (the application is launched in the background)
    // You can store it in AppDelegate or elsewhere to retrieve the value later.
    BOOL isWokenUpFromLocation = [launchOptions objectForKey:UIApplicationLaunchOptionsLocationKey] != nil);
    ...
    return YES;
}
```

- 3. According to `launchOptions` you can either choose to initialize the SDK locally or remotely:

> We **highly recommend** to **NOT start** the SDK when room counting in background is enable to avoid downloading/installing package automatically each time your application is awaken.

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    
    ...
    
    BOOL localInit = NO;
    BOOL wasLaunchFromBG = YES; // Check launch options
    
    // Launch from BG ? => local init
    if (wasLaunchFromBG) {
    	// If an error occured, it seems you never have synchronized the SDK with the server
        ISError *error = [[Insiteo sharedInstance] initializeLocallyWithLastConfiguration];
        localInit = (error == nil);
    }
    
    if (!localInit) {   
    	// If local init failed, you can try the standard way (remotely)
        [[Insiteo sharedInstance] initializeWithInitializeHandler:^(ISError *error, ISUserSite *suggestedSite, Boolean fromLocalCache) {
            if (error) {
                // Init error...
            } else {
				// Init succeed !
            }
        } andChooseSiteHandler:nil];
        
    } else {
        // Local init succeed, you can now start your site
        // NB: We highly recommand to NOT call the start SDK method if your application is 
        // awaken from iBeacon
        if (!wasLaunchFromBG) {
        	// Not from BG => OK you can start
        }
    }
    
    ...
}
```

> **Note:** You can do all the process in `application:didFinishLaunchingWithOptions:` from your `AppDelegate` class. 


## Where To Go From Here?

- Map rendering:
	- [Display your first map](map.md).
	- [Add graphical objects on map](map.md#2-add-graphical-objects-on-map)
- Location:
	- [Get your first location](location.md).
	- [Setup your first geofencing zone](geofence.md).
	- [Configure your iBeacons](beacon.md).
- Itinerary:
	- [Compute your first itinerary](itinerary.md).
- Analytics tracking events:
	- [Track Custom Events](analytics.md).