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
ms.openlocfilehash: 5be72a4125b276d85174a7a056cbbc2c23053e89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388880"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Mobile Apps と Mobile Services のクライアントとサーバーのバージョン管理
> [!NOTE]
> Visual Studio App Center は、モバイル アプリ開発の中心となるエンドツーエンドの統合サービスをサポートしています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。
>
> モバイル アプリケーションにクラウド サービスを統合することを検討している場合は、今すぐ [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) にサインアップしてください。

Azure Mobile Services の最新版は Azure App Service の **Mobile Apps** 機能です。

Mobile Apps のクライアント/サーバー SDK は Mobile Services のクライアント/サーバー SDK にもともと基づいていますが、互いに互換性が *ありません* 。
つまり、*Mobile Apps* クライアント SDK と共に *Mobile Apps* サーバー SDK を使用する必要があり、*Mobile Services* の場合も同様です。 この契約は、クライアント/サーバー SDK により使用される特殊なヘッダー値 `ZUMO-API-VERSION`により適用されます。

注: 本書で *Mobile Services* に言及するとき、それは必ずしも Mobile Services にホストすることを意味しません。 App Service で実行するモバイル サービスをコード変更なしで移行することが可能になりましたが、サービスでは引き続き *Mobile Services* SDK バージョンが利用されます。

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

## <a name="2.0.0"></a>Azure Mobile Apps クライアントとサーバー
### <a name="MobileAppsClients"></a> Mobile *Apps* クライアント SDK
バージョン チェックは **Azure Mobile Apps**の次のバージョンのクライアント SDK から導入されました。

| クライアント プラットフォーム | Version | バージョンのヘッダー値 |
| --- | --- | --- |
| 管理されたクライアント (Windows、Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

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

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
