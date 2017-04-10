
## <a name="a-idadd-pushaadd-push-notifications-to-app"></a><a id="add-push"></a>アプリケーションへのプッシュ通知の追加
* QSAppDelegate.m で、iOS SDK および QSTodoService.h をインポートします。

```
        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>
        #import "QSTodoService.h"
```

* QSAppDelegate.m の `didFinishLaunchingWithOptions` で、`return YES;` の直前に、次の行を挿入します。

```
        UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
```

* QSAppDelegate.m で、次のハンドラー メソッドを追加します。 これで、アプリケーションがプッシュ通知をサポートするように更新されました。

```
        // Registration with APNs is successful
        - (void)application:(UIApplication *)application
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

        // Handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

        // Use userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);

            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];

            UIAlertView *alert = [[UIAlertView alloc]
              initWithTitle:@"Notification"
              message:alertString
              delegate:nil
              cancelButtonTitle:@"OK"
              otherButtonTitles:nil];
            [alert show];
        }
```


<!--HONumber=Jan17_HO3-->


