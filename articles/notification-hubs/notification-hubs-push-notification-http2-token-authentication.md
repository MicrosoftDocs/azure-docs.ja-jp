---
title: Azure Notification Hubs での APNS のトークンベース (HTTP/2) 認証 | Microsoft Docs
description: APNS の新しいトークン認証を使用する方法について説明します。
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
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460467"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS のトークンベース (HTTP/2) 認証

## <a name="overview"></a>概要

この記事では、トークンベースの認証で新しい APNS HTTP/2 プロトコルを使用する方法を説明します。

この新しいプロトコルを使用する主な利点は次のとおりです。

* トークンの生成が比較的簡単である (証明書と比較して)
* 有効期限がない – ユーザーが認証トークンと失効を管理する
* 最大 4 KB のペイロードが許容される
* 同期フィードバック
* Apple の最新プロトコルの利用 – 証明書では、非推奨とマークされているバイナリ プロトコルがまだ使用されている

この新しいメカニズムは、2 つのステップで使用できるようになります。

* Apple Developer アカウント ポータルから必要な情報を取得します。
* 新しい情報で通知ハブを構成します。

これで、APNS で新しい認証システムを使用するように Notification Hubs が設定されます。

APNS の証明書の資格情報の使用から移行した場合、トークンのプロパティによって Microsoft のシステム内のお客様の証明書が上書きされますが、お客様のアプリケーションでは通知の受信がシームレスに継続されます。

## <a name="obtaining-authentication-information-from-apple"></a>Apple からの認証情報の取得

トークンベースの認証を有効にするには、Apple Developer アカウントから次のプロパティを取得する必要があります。

### <a name="key-identifier"></a>キー識別子

キー識別子は、Apple Developer アカウント内の **[Certificates, Identifiers & Profiles]\(証明書、識別子およびプロファイル\)** の **[Keys]\(キー\)** ページから入手できます。

![証明書](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![識別子](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>アプリケーション識別子とアプリケーション名

アプリケーションの名前と識別子も、開発者アカウントの **[Certificates, Identifiers & Profiles]\(証明書、識別子およびプロファイル\)** ページにあります。

![証明書と ID](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>.NET SDK または Azure portal を使用して構成する

[最新のクライアント SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs) または Azure portal を使用して、トークンベースの認証を使用するようにハブを構成できます。 ポータルでトークンベースの認証を有効にするには、Azure portal にサインインし、通知ハブの **[設定] > [Apple (APNS)]** パネルの順に移動します。 **[認証モード]** プロパティから **[トークン]** を選択して、関連するすべてのトークン プロパティでハブを更新します。

![トークンの構成](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple Developer アカウントから取得したプロパティを入力します。
* アプリケーション モード ( **[運用]** または **[サンドボックス]** ) を選択します。
* **[保存]** ボタンをクリックして APNS 資格情報を更新します。

トークンベースの資格情報は、次のフィールドで構成されます。

* **キー ID**:Apple Developer ポータルで生成された秘密キーの識別子。たとえば、`2USFGKSKLT` です。
* **チーム ID**:"プレフィックス" または "アプリのプレフィックス" とも呼ばれます。 これは、Apple Developer ポータルの組織の識別子です。たとえば、`S4V3D7CHJR` です。
* **[Bundle ID]\(バンドル ID\)** : "アプリ ID" とも呼ばれます。 これは、アプリケーションのバンドル識別子です。たとえば、`com.example.myapp` です。 1 つのアプリには、1 つのキーのみを使用できることに注意してください。 この値は、通知の送信時に `apns-topic` HTTP ヘッダーにマップされ、特定のアプリケーションを対象とするために使用されます。 `apns-topic` の値は明示的に設定できません。
* **トークン**:"キー" または "秘密キー" とも呼ばれます。 これは、Apple Developer ポータルで生成された .p8 ファイルから取得されます。 キーで APNS が有効になっている必要があります (キーの生成時に Apple Developer ポータルで選択されます)。 この値を NH ポータルや API に指定するときは、その値から PEM ヘッダー/フッターを削除する必要があります。
* **エンドポイント**: これは、Notification Hubs ポータル ブレードのトグルであり、API の文字列フィールドです。 有効な値は `https://api.development.push.apple.com:443/3/device` または `https://api.sandbox.push.apple.com:443/3/device`です。 Notification Hubs は、通知を送信するために、この値を運用またはサンドボックスのいずれかの環境で使用します。 これは、アプリの `aps-environment` エンタイトルメントと一致する必要があります。一致しないと、生成された APNS デバイス トークンが環境と一致せず、通知の送信に失敗します。

正しい使用法を示すコード サンプルを次に示します。

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>次のステップ

* [Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)
* [Azure portal 内で通知ハブを構成する](create-notification-hub-portal.md)
