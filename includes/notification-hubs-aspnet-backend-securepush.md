---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b150cad22528234286fa7939bf7055e8312ed361
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229265"
---
## <a name="webapi-project"></a>Web API プロジェクト
1. Visual Studio で、**ユーザーへの通知**に関するチュートリアルで作成した **AppBackend** プロジェクトを開きます。
2. Notifications.cs の **Notifications** クラス全体を次のコードで置き換えます。 必ず、プレースホルダーを通知ハブの (フル アクセスを持つ) 接続文字列とハブの名前に置き換えます。 これらの値は [Azure ポータル](https://portal.azure.com)から取得できます。 ここで、このモジュールは、送信される、セキュリティで保護された別の通知を表します。 完全な実装では、通知はデータベースに格納されますが、ここでは、操作を簡単にするために、メモリに格納します。
   
        public class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
            }

            public Notification CreateNotification(string payload)
            {
                var notification = new Notification() {
                Id = notifications.Count,
                Payload = payload,
                Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Notification ReadNotification(int id)
            {
                return notifications.ElementAt(id);
            }
        }

1. NotificationsController.cs の **NotificationsController** クラス定義内のコードを次のコードで置き換えます。 このコンポーネントは、デバイスが安全に通知を取得する方法を実装します。また、(このチュートリアルでは) 自分のデバイスへの安全なプッシュをトリガーする方法も提供します。 通知ハブに通知を送信するときに、通知の ID のみを含む (実際のメッセージは含まない) 直接通知を送信することに注意してください。
   
       public NotificationsController()
       {
           Notifications.Instance.CreateNotification("This is a secure notification!");
       }
   
       // GET api/notifications/id
       public Notification Get(int id)
       {
           return Notifications.Instance.ReadNotification(id);
       }
   
       public async Task<HttpResponseMessage> Post()
       {
           var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
           var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;
   
           // windows
           var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                           new Dictionary<string, string> {
                               {"X-WNS-Type", "wns/raw"}
                           });
           await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);
   
           // apns
           await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);
   
           // gcm
           await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }


`Post` メソッドは、トースト通知を送信しません。 通知 ID のみを含み、慎重な扱いを要するコンテンツは含まない直接通知を送信します。 また、通知ハブで資格情報を構成していないプラットフォームの送信操作は必ずコメント アウトしてください。そうしないと、エラーになります。

1. 次に、このアプリを Azure の Web サイトにもう一度デプロイして、すべてのデバイスからアクセスできるようにします。 **AppBackend** プロジェクトを右クリックして **[発行]** を選択します。
2. 発行先として Azure の Web サイトを選択します。 Azure アカウントでサインインし、既存または新規の Web サイトを選択します。 **[接続]** タブの **[宛先 URL]** プロパティをメモしておきます。後で、この URL を *バックエンド エンドポイント* として参照します。 **[発行]** をクリックします。

