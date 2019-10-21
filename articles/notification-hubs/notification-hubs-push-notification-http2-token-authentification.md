---
title: Azure Notification Hubs での APNS のトークンベース (HTTP/2) 認証 | Microsoft Docs
description: このトピックでは、APNS 用の新しいトークン認証を活用する方法について説明します
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213121"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS のトークンベース (HTTP/2) 認証

## <a name="overview"></a>概要

この記事では、トークン ベースの認証で新しい APNS HTTP/2 プロトコルを使用する方法を説明します。

この新しいプロトコルを使用する主な利点は次のとおりです。

* トークンの生成が比較的簡単である (証明書と比較して)
* 有効期限がない – ユーザーが認証トークンと失効を管理する
* 最大 4 KB のペイロードが許容される
* 同期フィードバック
* Apple の最新プロトコルの利用 – 証明書ではまだバイナリ プロトコルが使用されているが、これは非推奨とマークされている

この新しいメカニズムは、2 つのステップで、数分間で使用できるようになります。

1. Apple の開発者アカウント ポータルから必要な情報を取得する
2. 新しい情報で通知ハブを構成する

これで、APNS で新しい認証システムを使用するように Notification Hubs が設定されます。

APNS での証明書資格情報の使用から移行する場合は、次の点に注意してください。

* トークンのプロパティによって、システム内で証明書が上書きされる
* ただし、アプリケーションは引き続きシームレスに通知を受信する

## <a name="obtaining-authentication-information-from-apple"></a>Apple からの認証情報の取得

トークン ベースの認証を有効にするには、Apple 開発者アカウントから次のプロパティを取得する必要があります。

### <a name="key-identifier"></a>キー識別子

キー識別子は、Apple 開発者アカウントの [Keys] ページから入手できます。

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>アプリケーション識別子とアプリケーション名

アプリケーション名は、開発者アカウントの [App ID] ペーからで入手できます。

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

アプリケーション識別子は、開発者アカウントのメンバーシップの詳細ページから入手できます。

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>認証トークン

認証トークンは、アプリケーション用のトークンを生成した後にダウンロードできます。 このトークンの生成方法の詳細については、[Apple の開発者向けドキュメント](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1)を参照してください。

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>トークン ベースの認証を使用するための通知ハブの構成

### <a name="configure-via-the-azure-portal"></a>Azure Portal で構成する

ポータルでトークン ベースの認証を有効にするには、Azure Portal にログインし、[Notification Hub] > [Notification Services] > [APNS] パネルの順に移動してください。

新しいプロパティである、"*認証モード*" が見つかります。 [トークン] を選択すると、関連するすべてのトークンのプロパティでハブを更新できます。

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple 開発者アカウントから取得したプロパティを入力します。
* アプリケーション モード (実稼働またはサンドボックス) を選択します。
* **[保存]** ボタンをクリックして APNS 資格情報を更新します。

### <a name="configure-via-management-api-rest"></a>管理 API (REST) を使用して構成する

[管理 API](https://msdn.microsoft.com/library/azure/dn495827.aspx) を使用して、トークン ベースの認証を使用するように通知ハブを更新できます。
構成しているアプリケーションがサンドボックス アプリと実稼働アプリのどちらであるか (Apple 開発者アカウントで指定) に応じて、対応するいずれかのエンドポイントを使用します。

* サンドボックス エンドポイント: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* 運用エンドポイント: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> トークンベースの認証には、**2017-04 以降** の API バージョンが必要です。

トークンベースの認証を使用してハブを更新する PUT 要求の例を次に示します。

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>.NET SDK を使用して構成する

[最新のクライアント SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8) を使用して、トークン ベースの認証を使用するようにハブを構成することができます。

正しい使用法を示すコード サンプルを次に示します。

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>クライアント証明書ベースの認証を使用するように戻す

前述のいずれかのメソッドで、トークン用プロパティの代わりに証明書を渡すことによって、いつでも証明書ベースの認証を使用するように戻すことができます。 そのアクションにより、以前に保存された資格情報が上書きされます。
