---
title: Mobile Apps と Mobile Services のクライアントとサーバーの SDK バージョン管理 | Microsoft Docs
description: Mobile Services と Azure Mobile Apps のクライアント SDK およびサーバー SDK バージョンとの互換性の一覧
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 56c5e0582afe55dcd63aa056817898d3d4942419
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594068"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Mobile Apps と Mobile Services のクライアントとサーバーのバージョン管理
Azure Mobile Services の最新版は Azure App Service の **Mobile Apps** 機能です。

Mobile Apps のクライアント/サーバー SDK は Mobile Services のクライアント/サーバー SDK にもともと基づいていますが、互いに互換性が *ありません* 。
つまり、*Mobile Apps* クライアント SDK と共に *Mobile Apps* サーバー SDK を使用する必要があり、*Mobile Services* の場合も同様です。 この契約は、クライアント/サーバー SDK により使用される特殊なヘッダー値 `ZUMO-API-VERSION`により適用されます。

注: 本書で *Mobile Services* に言及するとき、それは必ずしも Mobile Services にホストすることを意味しません。 App Service で実行するモバイル サービスをコード変更なしで移行することが可能になりましたが、サービスでは引き続き *Mobile Services* SDK バージョンが利用されます。

コード変更なしで App Service に移行する方法については、 [Mobile Service を Azure App Service に移行する]方法に関する記事を参照してください。

## <a name="header-specification"></a>ヘッダーの仕様
キー `ZUMO-API-VERSION` を HTTP ヘッダーかクエリ文字列に指定できます。 値は「 **x.y.z**」形式のバージョン文字列です。

例: 

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS:ZUMO-API-VERSION:2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>バージョン チェックの除外
バージョン チェックを除外できます。その場合、アプリ設定 **MS_SkipVersionCheck** に **true** 値を設定します。 これは web.config か Azure ポータルの [アプリケーション設定] セクションで指定します。

> [!NOTE]
> Mobile Services と Mobile Apps の間には、特にオフライン同期、認証、プッシュ通知の領域で、さまざまな動作変更があります。 テストを完了し、動作変更によるアプリの機能停止がないことを確認してからバージョン チェックを除外してください。
>
>

## <a name="summary-of-compatibility-for-all-versions"></a>すべてのバージョンの互換性のまとめ
以下の表は、あらゆる種類のクライアント/サーバー間の互換性をまとめたものです。 バックエンドは、それが使用するサーバー SDK に基づき、Mobile **Services** または Mobile **Apps** として分類されます。

|  | **Mobile Services** Node.js または .NET | **Mobile Apps** Node.js または .NET |
| --- | --- | --- |
| [Mobile Services クライアント] |[OK] |エラー\* |
| [Mobile Apps クライアント] |エラー\* |[OK] |

\*これは **MS_SkipVersionCheck** を指定することで制御できます。

<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is https://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Mobile Services のクライアントとサーバー
下の表にあるクライアント SDK は **Mobile Services**と互換性があります。

注: Mobile Services クライアント SDK は `ZUMO-API-VERSION` のヘッダー値を送信 "*しません*"。 サービスがこのヘッダーやクエリ文字列値を受け取った場合、前述のように明示的に除外しない限り、エラーが返されます。

### <a name="MobileServicesClients"></a> Mobile *Services* クライアント SDK
| クライアント プラットフォーム | Version | バージョンのヘッダー値 |
| --- | --- | --- |
| 管理されたクライアント (Windows、Xamarin) |[1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) |該当なし |
| iOS |[2.2.2](https://aka.ms/gc6fex) |該当なし |
| Android |[2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) |該当なし |
| HTML |[1.2.7](https://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) |該当なし |

### <a name="mobile-services-server-sdks"></a>Mobile *Services* サーバー SDK
| サーバー プラットフォーム | Version | 同意済みのバージョン ヘッダー |
| --- | --- | --- |
| .NET |[WindowsAzure.MobileServices.Backend.* バージョン 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) |**バージョン ヘッダーなし** |
| Node.js |(近日対応予定) |**バージョン ヘッダーなし** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Mobile Services バックエンドの動作
| ZUMO-API-VERSION | MS_SkipVersionCheck の値 | Response |
| --- | --- | --- |
| 指定なし |任意 |200 - OK |
| 任意の値 |True |200 - OK |
| 任意の値 |偽/指定なし |400 - 正しくない要求 |

## <a name="2.0.0"></a>Azure Mobile Apps クライアントとサーバー
### <a name="MobileAppsClients"></a> Mobile *Apps* クライアント SDK
バージョン チェックは **Azure Mobile Apps**の次のバージョンのクライアント SDK から導入されました。

| クライアント プラットフォーム | Version | バージョンのヘッダー値 |
| --- | --- | --- |
| 管理されたクライアント (Windows、Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Mobile *Apps* サーバー SDK
バージョン チェックは次のサーバー SDK バージョンに含まれています。

| サーバー プラットフォーム | SDK | 同意済みのバージョン ヘッダー |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server.](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobile Apps バックエンドの動作
| ZUMO-API-VERSION | MS_SkipVersionCheck の値 | Response |
| --- | --- | --- |
| x.y.z または Null |True |200 - OK |
| Null |偽/指定なし |400 - 正しくない要求 |
| 1.x.y |偽/指定なし |400 - 正しくない要求 |
| 2.0.0-2.x.y |偽/指定なし |200 - OK |
| 3.0.0-3.x.y |偽/指定なし |400 - 正しくない要求 |

## <a name="next-steps"></a>次の手順
* [Mobile Service を Azure App Service に移行する]

[Mobile Services クライアント]: #MobileServicesClients
[Mobile Apps クライアント]: #MobileAppsClients


[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Mobile Service を Azure App Service に移行する]: app-service-mobile-migrating-from-mobile-services.md
