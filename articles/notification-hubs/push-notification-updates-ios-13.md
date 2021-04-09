---
title: Azure Notification Hubs iOS 13 の更新内容 | Microsoft Docs
description: Azure Notification Hubs における iOS 13 の重大な変更について説明します
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998052"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs の更新内容 (iOS 13 関連)

最近、Apple のパブリック プッシュ サービスにいくつかの変更が加えられました。ほとんどの変更は、iOS 13 と Xcode のリリースに合わせて調整されています。 この記事では、Azure Notification Hubs に対するこれらの変更の影響について説明します。

## <a name="apns-push-payload-changes"></a>APNS プッシュ ペイロードの変更内容

### <a name="apns-push-type"></a>APNS プッシュの種類

Apple では、開発者が APNS API の新しい `apns-push-type` ヘッダーを介して通知をアラートまたはバックグラウンド通知として指定することを要求しています。 [Apple のドキュメント](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)によると、"このヘッダーの値は、通知のペイロードの内容を正確に反映している必要があります。 不一致がある場合、または必要なシステムにヘッダーがない場合は、APN によってエラーが返されるか、通知の配信が遅延されるか、通知が完全に削除されます。"

開発者は、Azure Notification Hubs を介して通知を送信するアプリケーションで、このヘッダーを設定する必要があります。 技術的な制限により、お客様は、この属性を含む要求で APNS 資格情報に対してトークンベースの認証を使用する必要があります。 APNS 資格情報に証明書ベースの認証を使用している場合は、トークンベースの認証を使用するように切り替える必要があります。

次のコード サンプルは、Azure Notification Hubs を介して送信される通知要求でこのヘッダー属性を設定する方法を示しています。

#### <a name="template-notifications---net-sdk"></a>テンプレート通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>ネイティブ通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>直接の REST 呼び出し

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

この切り替え中に役立つように、Azure Notification Hubs では、`apns-push-type` が設定されていない通知を検出すると、通知要求からプッシュの種類を推測し、値を自動的に設定するようになっています。 トークンベースの認証を使用して必要なヘッダーを設定するように Azure Notification Hubs を構成する必要があることに注意してください。詳細については、「[APNS のトークンベース (HTTP/2) 認証](./notification-hubs-push-notification-http2-token-authentication.md)」を参照してください。

## <a name="apns-priority"></a>APNS の優先度

別の軽微な (しかし、通知を送信するバックエンド アプリケーションに対する変更が必要な) 変更として、バックグラウンド通知で `apns-priority` ヘッダーを 5 に設定することが要件になりました。 多くのアプリケーションでは、`apns-priority` ヘッダーが 10 (即時配信を示す) に設定されているか、未設定であるために既定値 (同じく 10) が使用されています。

バックグラウンド通知では、この値を 10 に設定することはできなくなりました。各要求に値を設定する必要があります。 この値が指定されていない場合、Apple によるバックグラウンド通知の配信は行われません。 次に例を示します。

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK の変更内容

長年にわたり、iOS 開発者は、プッシュ トークン デリゲートに送信される `deviceToken` データの `description` 属性を使用して、バックエンド アプリケーションがデバイスに通知を送信するために使用するプッシュ トークンを抽出しています。 Xcode 11 で、その `description` 属性が異なる形式に変更されました。 この属性に対して開発者が使用していた既存のコードは、動作しなくなりました。 この変更に対応するために Azure Notification Hubs SDK を更新しました。そのため、ご自身のアプリケーションで使用している SDK を [Azure Notification Hubs iOS SDK ](https://github.com/Azure/azure-notificationhubs-ios) のバージョン 2.0.4 以降に更新してください。
