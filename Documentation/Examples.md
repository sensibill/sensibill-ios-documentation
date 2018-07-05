## Local Notification Example

Here is some sample code if you would like to implement local notifications to set Reminder. You will need to modify `AppDelegate.h`, `AppDelegate.m`, similar to what is found below.

It is recommended that you since you may have other notifications being received by your application, that you check if each notification originated from the Sensibill SDK. To do this, you can look into the `userInfo` field on a given notification. See the code below for a demonstration.

Please note that the UINavigationController variable `DESIRED_NAVIGATION_CONTROLLER` is not instantiated in this sample code and is meant for you to substitute some navigation controller which you would like our SDK to push views upon (much like how you would pass in a controller for presenting a receipt view).

### Important note about integration

Please note that if this notification is received while your app was not recently just active, you'll need to ensure that the Sensibill SDK is initialized properly and the appropriate user has been authenticated before calling any of our Notification Handler methods. In the sample code below, that is the purpose of `[someClass initializeSensibillSDK];`, as this class doesn't really exist and is merely a placeholder which is meant to demonstrate the point at which you should be initializing the Sensibill SDK using the methods described at the beginning of this document.

```objective-c
//
//  AppDelegate.h
//
//

#import <UIKit/UIKit.h>
#import <UserNotifications/UserNotifications.h>

@interface AppDelegate : UIApplication <UIApplicationDelegate, UNUserNotificationCenterDelegate>

@property (strong, nonatomic) UIWindow *window;

@end

//
//  AppDelegate.m
//
//

#import "AppDelegate.h"

@interface AppDelegate ()

@end

@implementation AppDelegate
#import "SensibillSDK.h"
#import "SBReminderNotificationHandler.h"

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
// Override point for customization after application launch.
    if (SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(@"10.0")) {
        [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    } else {
        [launchOptions objectForKey:UIApplicationLaunchOptionsLocalNotificationKey];
    }
    return YES;
}

//...

#pragma mark -
#pragma mark - UILocalNotification Method (iOS 8,9)

- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
    [[UIApplication sharedApplication] cancelLocalNotification:notification] ;

    BOOL fromSensibillSDK = notification.userInfo[@"isSensibillNotification"];

    if (fromSensibillSDK) {
        [someClass initializeSensibillSDK];
        [[SBReminderNotificationHandler sharedHandler] setNavigationControllerToUse:[self visibleViewController:self.window.rootViewController].navigationController];
        [[SBReminderNotificationHandler sharedHandler] application:application didReceiveLocalNotification:notification];
    } else {
        // Non-Sensibill notification, proceed as normal
    }
}

#pragma mark -
#pragma mark - UNUserNotificationCenterDelegate Method (iOS 10+)

//Called when a notification is delivered to a foreground app.
- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert);
}

//Called to let your app know which action was selected by the user for a given notification.
- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler {
    BOOL fromSensibillSDK = response.notification.request.content.userInfo[@"isSensibillNotification"];

    if (fromSensibillSDK) {
        [someClass initializeSensibillSDK];
        [[SBReminderNotificationHandler sharedHandler] setNavigationControllerToUse:[self visibleViewController:self.window.rootViewController].navigationController];
        [[SBReminderNotificationHandler sharedHandler] userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    } else {
        // Non-Sensibill notification, proceed as normal
    }
}

@end
```

## Inbox Sync Integration Example

Here is some sample code for integrating the Inbox Sync feature into your app. You will need to modify `AppDelegate.m` to forward the `openURL` calls into our SDK in order for the OAuth SDKs from our Mail providers to work correctly.

```objective-c
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary *)options {
    return [SensibillSDK application:app openURL:url options:options];
}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
    return [SensibillSDK application:application openURL:url sourceApplication:sourceApplication annotation:annotation];
}
```
