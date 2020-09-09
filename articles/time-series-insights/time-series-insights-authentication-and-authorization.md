---
title: API の認証と承認 - Azure Time Series Insights | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: bbec605f25c2e74178bdb5c28d0a7995e4e265f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690393"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

このドキュメントでは、新しい Azure Active Directory ブレードを使用して Azure Active Directory にアプリを登録する方法について説明します。 Azure Active Directory に登録されたアプリを使用すると、ユーザーは、Azure Time Series Insights 環境に関連付けられた Azure Time Series Insight API を使用する認証と承認を受けることができます。

## <a name="service-principal"></a>サービス プリンシパル

以下のセクションでは、アプリケーションを構成して、アプリの代わりに Azure Time Series Insights API にアクセスする方法について説明します。 アプリケーションは、Azure Active Directory を介して独自のアプリケーション資格情報を使用し、Azure Time Series Insights 環境で参照データをクエリまたは発行することができます。

## <a name="summary-and-best-practices"></a>概要とベスト プラクティス

Azure Active Directory のアプリ登録フローには、主に 3 つの手順があります。

1. Azure Active Directory に[アプリケーションを登録する](#azure-active-directory-app-registration)。
1. [Azure Time Series Insights 環境へのデータ アクセス](#granting-data-access)をアプリケーションに承認します。
1. [クライアント アプリ](#client-app-initialization)の  **からトークンを取得するには、** [申請 ID] **と**[クライアント シークレット]`https://api.timeseries.azure.com/` を使用します。 トークンは、Azure Time Series Insights API の呼び出しに使用できます。

**手順 3** に従い、アプリケーションとユーザーの資格情報を分離すると、次のことが可能になります。

* 独自のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可はアプリで必要な操作のみに制限されます。 たとえば、アプリが特定の Azure Time Series Insights 環境からのデータのみを読み取るようにすることが可能です。
* **クライアント シークレット**またはセキュリティ証明書を使用して、作成しているユーザーの認証資格情報から、アプリのセキュリティを分離します。 その結果、アプリケーションの資格情報は特定のユーザーの資格情報に依存しません。 ユーザーのロールが変わっても、アプリケーションは必ずしも新しい資格情報や追加の構成を必要としません。 ユーザーが自分のパスワードを変更した場合、アプリケーションへのすべてのアクセスに新しい資格情報やキーは必要ありません。
* (存在する必要がある) 特定のユーザーの資格情報ではなく、**クライアント シークレット**またはセキュリティ証明書を使用して無人スクリプトを実行します。
* Azure Time Series Insights API へのアクセスをセキュリティで保護するには、パスワードではなくセキュリティ証明書を使用します。

> [!IMPORTANT]
> Azure Time Series Insights セキュリティ ポリシーを構成するときは、**懸念事項の分離** (このシナリオについては前述を参照) の原則に従います。

> [!NOTE]

> * この記事では、シングルテナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。
> * 通常は、組織内で実行される基幹業務アプリケーションには、シングルテナント アプリケーションが使用されます。

## <a name="detailed-setup"></a>詳細なセットアップ

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory のアプリ登録

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>データ アクセスの許可

1. Azure Time Series Insights 環境のために、 **[データ アクセス ポリシー]** を選択して、 **[追加]** を選択します。

   [![Azure Time Series Insights 環境に新しいデータ アクセス ポリシーを追加する](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **[ユーザーの選択]** ダイアログ ボックスで、Azure Active Directory のアプリ登録セクションの **[アプリケーション名]** または **[申請 ID]** のいずれかを貼り付けます。

   [![[ユーザーの選択] ダイアログ ボックスでアプリケーションを検索する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. ロールを選択します。 データのクエリを実行するには **[閲覧者]** を選択し、データのクエリを実行して参照データを変更するには **[共同作成者]** を選択します。 **[OK]** を選択します。

   [![[ユーザー ロールの選択] ダイアログ ボックスで、[閲覧者] または [共同作成者] を選択する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **[OK]** を選択してポリシーを保存します。

   > [!TIP]
   > 高度なデータ アクセスのオプションについては、[データ アクセスの許可](./time-series-insights-data-access.md)に関する記事を参照してください。

### <a name="client-app-initialization"></a>クライアント アプリの初期化

* 開発者は、Microsoft Authentication Library (MSAL) を使用して Azure Time Series Insights で認証することができます。

* MSAL を使用して認証するには、次のようにします。

   1. アプリケーションに代わってトークンを取得するには、Azure Active Directory のアプリ登録セクションの **[申請 ID]** と **[クライアント シークレット]** (アプリケーション キー) を使用します。

   1. C# では、次のコードでアプリケーションに代わってトークンを取得できます。 Gen1 環境からデータを照会する方法の完全なサンプルについては、[C# を使用したデータ照会](time-series-insights-query-data-csharp.md)に関するページを参照してください。

        C# サンプル コードにアクセスするには、[Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs) リポジトリを参照します。 

   1. トークンは、アプリケーションが Azure Time Series Insights API を呼び出すときに、`Authorization` ヘッダーで渡すことができます。

> [!IMPORTANT]
> [Azure Active Directory 認証ライブラリ (ADAL)](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) を使用している場合は、[MSAL への移行](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)に関する記事をご覧ください。

## <a name="common-headers-and-parameters"></a>一般的なヘッダーとパラメーター

このセクションでは、Azure Time Series Insights GA 1 API や GA2 API に対するクエリの作成に使用される、一般的な HTTP 要求ヘッダーとパラメーターについて説明します。 API 固有の要件は、[Azure Time Series Insights REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/)に詳しく記載されています。

> [!TIP]
> REST API の使用方法、HTTP 要求の作成方法、および HTTP 応答の処理方法の詳細については、[Azure REST API リファレンス](https://docs.microsoft.com/rest/api/azure/) を参照してください。

### <a name="authentication"></a>認証

[Azure Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/) に対して認証されたクエリを実行するには、任意の REST クライアント (Postman、JavaScript、C#) を使用して、有効な OAuth 2.0 ベアラー トークンを [Authorization ヘッダー](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)内で渡す必要があります。

> [!TIP]
> チャートやグラフと共に [JavaScript クライアント SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) を使用して、プログラムによって Azure Time Series Insights API で認証を行う方法については、Azure Time Series Insights の[クライアント SDK のサンプルの視覚化](https://tsiclientsample.azurewebsites.net/)に関する記事を参照してください。

### <a name="http-headers"></a>HTTP ヘッダー

必要な要求ヘッダーを以下に示します。

| 必要な要求ヘッダー | 説明 |
| --- | --- |
| 承認 | Azure Time Series Insights で認証するには、有効な OAuth 2.0 ベアラー トークンを **Authorization** ヘッダー内で渡す必要があります。 |

> [!IMPORTANT]
> そのトークンは、まさしくその `https://api.timeseries.azure.com/` リソース (トークンの "audience" とも呼ばれます) に向けて発行される必要があります。

> * したがって、[Postman](https://www.getpostman.com/) **AuthURL** は`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com/.default` のようになります
> * `https://api.timeseries.azure.com/` は有効ですが、`https://api.timeseries.azure.com` は有効ではありません。

省略可能な要求ヘッダーを以下に示します。

| 省略可能な要求ヘッダー | 説明 |
| --- | --- |
| Content-type | `application/json` のみがサポートされています。 |
| x-ms-client-request-id | クライアント要求 ID。 サービスでは、この値です。 これにより、サービスでは、サービス間の操作を追跡できるようになります。 |
| x-ms-client-session-id | クライアントセッション ID。 サービスでは、この値です。 これにより、サービスでは、サービス間の関連する操作のグループを追跡できるようになります。 |
| x-ms-client-application-name | この要求を生成したアプリケーションの名前。 サービスでは、この値です。 |

省略可能ですが、推奨される応答ヘッダーを以下に示します。

| 応答ヘッダー | 説明 |
| --- | --- |
| Content-type | サポートされるのは `application/json` のみです。 |
| x-ms-request-id | サーバーで生成された要求 ID。 要求の調査についての Microsoft への問い合わせに使用できます。 |
| x-ms-property-not-found-behavior | GA API オプションの応答ヘッダー。 指定できる値は、`ThrowError` (既定値) または `UseNull`です。 |

### <a name="http-parameters"></a>HTTP パラメーター

> [!TIP]
> 必須および省略可能なクエリの情報の詳細については、[参照ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/)を参照してください。

必須の URL クエリ文字列パラメーターは、API バージョンによって異なります。

| Release | 使用可能な API バージョンの値 |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` および `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview` のバージョンは、間もなく非推奨になります。 ユーザーは新しいバージョンに切り替えることをお勧めします。

省略可能な URL クエリ文字列パラメーターには、HTTP 要求の実行時間のタイムアウト設定が含まれます。

| 省略可能なクエリパラメーター | 説明 | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 要求実行のサーバー側のタイムアウト。 [環境イベントの取得](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) API と[環境集計の取得](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API にのみ適用できます。 タイムアウト値は、`"PT20S"` など、ISO 8601 の期間の形式で指定され、その範囲は `1-30 s` である必要があります。 既定値は `30 s` です。 | Gen1 |
| `storeType=<storeType>` | ウォーム ストアが有効になっている Gen2 環境では、`WarmStore` または `ColdStore` のいずれかでクエリを実行できます。 クエリ内のこのパラメーターは、クエリを実行する必要があるストアを定義します。 定義されていない場合は、コールドストアでクエリが実行されます。 ウォームストアに対してクエリを実行するには、**storeType** を `WarmStore`に設定する必要があります。 定義されていない場合は、コールドストアに対してクエリが実行されます。 | Gen2 |

## <a name="next-steps"></a>次の手順

* Gen1 Azure Time Series Insights API を呼び出すサンプル コードについては、[C# を使用した Gen1 データの照会](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。

* Gen2 Azure Time Series Insights API のコード サンプルを呼び出すサンプル コードについては、[C# を使用した Gen2 データの照会](./time-series-insights-update-query-data-csharp.md)に関する記事を参照してください。

* API リファレンスについては、[クエリ API リファレンス](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-api)のドキュメントを参照してください。

* [サービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)方法について学習します。
