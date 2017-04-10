
**Objective-C**:

1. **QSAppDelegate.m** で、iOS SDK と **QSTodoService.h**をインポートします。
   
        #import <MicrosoftAzureMobile/MicrosoftAzureMobile.h>
        #import "QSTodoService.h"
2. **QSAppDelegate.m** の `didFinishLaunchingWithOptions` で、`return YES;` の直前に次の行を挿入します。
   
        UIUserNotificationSettings* notificationSettings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound categories:nil];
        [[UIApplication sharedApplication] registerUserNotificationSettings:notificationSettings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
3. **QSAppDelegate.m**で、次のハンドラー メソッドを追加します。 これで、アプリケーションがプッシュ通知をサポートするように更新されました。 
   
        // Registration with APNs is successful
        - (void)application:(UIApplication *)application
        didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
   
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;
   
            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
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
   
        // Use userInfo in the payload to display an alert.
        - (void)application:(UIApplication *)application
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
   
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
   
            // Create alert with notification content.
            UIAlertController *alertController = [UIAlertController
                                          alertControllerWithTitle:@"Notification"
                                          message:alertString
                                          preferredStyle:UIAlertControllerStyleAlert];
   
            UIAlertAction *cancelAction = [UIAlertAction
                                           actionWithTitle:NSLocalizedString(@"Cancel", @"Cancel")
                                           style:UIAlertActionStyleCancel
                                           handler:^(UIAlertAction *action)
                                           {
                                               NSLog(@"Cancel");
                                           }];
   
            UIAlertAction *okAction = [UIAlertAction
                                       actionWithTitle:NSLocalizedString(@"OK", @"OK")
                                       style:UIAlertActionStyleDefault
                                       handler:^(UIAlertAction *action)
                                       {
                                           NSLog(@"OK");
                                       }];
   
            [alertController addAction:cancelAction];
            [alertController addAction:okAction];
   
            // Get current view controller.
            UIViewController *currentViewController = [[[[UIApplication sharedApplication] delegate] window] rootViewController];
            while (currentViewController.presentedViewController)
            {
                currentViewController = currentViewController.presentedViewController;
            }
   
            // Display alert.
            [currentViewController presentViewController:alertController animated:YES completion:nil];
   
        }

**Swift**:

1. 次の内容を含む **ClientManager.swift** ファイルを追加します。 *%AppUrl%* を Azure モバイル アプリ バックエンドの URL に置き換えます。
   
        class ClientManager {
            static let sharedClient = MSClient(applicationURLString: "%AppUrl%")
        }
2. **ToDoTableViewController.swift** で、`MSClient` を初期化する `let client` 行を次の行に置き換えます。
   
        let client = ClientManager.sharedClient
3. **AppDelegate.swift** で、`func application` の本文を次のように置き換えます。
   
        func application(application: UIApplication,
          didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
           application.registerUserNotificationSettings(
               UIUserNotificationSettings(forTypes: [.Alert, .Badge, .Sound],
                   categories: nil))
           application.registerForRemoteNotifications()
           return true
        }
4. **AppDelegate.swift**で、次のハンドラー メソッドを追加します。 これで、アプリケーションがプッシュ通知をサポートするように更新されました。
   
        func application(application: UIApplication,
           didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
            ClientManager.sharedClient.push?.registerDeviceToken(deviceToken) { error in
                print("Error registering for notifications: ", error?.description)
            }
        }
   
        func application(application: UIApplication,
           didFailToRegisterForRemoteNotificationsWithError error: NSError) {
            print("Failed to register for remote notifications: ", error.description)
        }
   
        func application(application: UIApplication,
           didReceiveRemoteNotification userInfo: [NSObject: AnyObject]) {
   
            print(userInfo)
   
            let apsNotification = userInfo["aps"] as? NSDictionary
            let apsString       = apsNotification?["alert"] as? String
   
            let alert = UIAlertController(title: "Alert", message: apsString, preferredStyle: .Alert)
            let okAction = UIAlertAction(title: "OK", style: .Default) { _ in
                print("OK")
            }
            let cancelAction = UIAlertAction(title: "Cancel", style: .Default) { _ in
                print("Cancel")
            }
   
            alert.addAction(okAction)
            alert.addAction(cancelAction)
   
            var currentViewController = self.window?.rootViewController
            while currentViewController?.presentedViewController != nil {
                currentViewController = currentViewController?.presentedViewController
            }
   
            currentViewController?.presentViewController(alert, animated: true) {}
   
        }



<!--HONumber=Nov16_HO3-->


