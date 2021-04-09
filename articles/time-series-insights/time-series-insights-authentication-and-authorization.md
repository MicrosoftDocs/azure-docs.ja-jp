---
title: API の認証と承認 - Azure Time Series Insights | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 88fd575d40cc31f12f052158bda0aed9a5335555
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009268"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

ビジネス ニーズによっては、Azure Time Series Insights 環境の [API](/rest/api/time-series-insights/reference-data-access-overview) と対話するために使用する 1 つ以上のクライアント アプリケーションが、ソリューションに含まれる場合があります。 Azure Time Series Insights により、[OAUTH 2.0 に基づく Azure AD セキュリティ トークン](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)を使用して認証が実行されます。 クライアントの認証を行うには、適切なアクセス許可を持つベアラー トークンを取得し、API の呼び出しでそれを渡す必要があります。 このドキュメントでは、ベアラー トークンを取得して認証を行うために使用できる、資格情報を取得するためのいくつかの方法について説明します。これには、マネージド ID の使用と Azure Active Directory アプリの登録が含まれます。

## <a name="managed-identities"></a>マネージド ID

以下のセクションでは、Azure Active Directory (Azure AD) からのマネージド ID を使用して、Azure Time Series Insights API にアクセスする方法について説明します。 Azure のマネージド ID を使用すれば、開発者が資格情報を管理する必要がなくなります。Azure リソースの ID は Azure AD から提供され、その ID を使用して Azure Active Directory (Azure AD) トークンが取得されます。 以下に、マネージド ID を使用する利点をいくつか紹介します。

- 資格情報を自ら管理する必要がない。 資格情報には、自分もアクセスできません。
- マネージド ID を使用すると、Azure AD Authentication をサポートするあらゆる Azure サービス (Azure Key Vault を含む) に対して認証を行うことができる。
- マネージド ID の使用に関して追加コストは一切かからない。

2 種類のマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください

次の場所のマネージド ID を使用できます。

- Azure VM
- Azure App Service
- Azure Functions
- Azure Container Instances
- その他...

完全な一覧については、「[Azure リソースのマネージド ID をサポートする Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)」を参照してください。

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory のアプリ登録

資格情報を管理する必要がないように、可能な限りマネージド ID を使用することをお勧めします。 マネージド ID をサポートする Azure サービスでクライアント アプリケーションがホストされていない場合は、アプリケーションを Azure AD テナントに登録できます。 アプリケーションを Azure AD に登録するときに、アプリケーションの ID 構成を作成します。これによって Azure AD との連携が可能となります。 [Azure portal](https://portal.azure.com/) でアプリを登録するときに、それがシングル テナント (自分のテナント内でのみアクセス可能) かマルチテナント (他のテナント内でアクセス可能) かを選択し、必要に応じてリダイレクト URI (アクセス トークンの送信先) を設定します。

アプリの登録が完了すると、ホーム テナントまたはディレクトリ内に存在するアプリ (アプリケーション オブジェクト) のグローバルに一意なインスタンスが作成されます。 また、アプリにグローバルに一意な ID (アプリまたはクライアント ID) も割り当てられます。 ポータルでは、シークレットまたは証明書とスコープを追加してアプリを機能させることや、サインイン ダイアログでアプリのブランドをカスタマイズすることなどができます。

ポータルでアプリケーションを登録すると、ホーム テナントにアプリケーション オブジェクトとサービス プリンシパル オブジェクトが自動的に作成されます。 Microsoft Graph API を使用してアプリケーションを登録または作成する場合、サービス プリンシパル オブジェクトの作成は別の手順です。 トークンを要求するには、サービス プリンシパル オブジェクトが必要です。

お使いのアプリケーションの「[セキュリティ](../active-directory/develop/identity-platform-integration-checklist.md#security)」チェックリストを必ず確認してください。 ベスト プラクティスとしては、パスワードの資格情報 (クライアント シークレット) ではなく、[証明書の資格情報](../active-directory/develop/active-directory-certificate-credentials.md)を使用する必要があります。

詳細については、「[Azure Active Directory のアプリケーションとサービス プリンシパルのオブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>ステップ 1: マネージド ID またはアプリの登録を作成する

マネージド ID とアプリの登録のどちらを使用するかを決定したら、次のステップとしてそれをプロビジョニングします。

### <a name="managed-identity"></a>マネージド ID

マネージド ID の作成に使用する手順は、コードが配置される場所と、システム割り当てまたはユーザー割り当ての ID を作成するかどうかによって異なります。 違いを理解するには、「[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)」を参照してください。 ID の種類を選択したら、Azure AD のマネージド ID に関する[ドキュメント](../active-directory/managed-identities-azure-resources/index.yml)で、正しいチュートリアルを見つけてそれに従います。 そこでは、次のものに関するマネージド ID の構成方法が説明されています。

- [Azure VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service と Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- その他...

### <a name="application-registration"></a>アプリケーションの登録

「[アプリケーションを登録する](../active-directory/develop/quickstart-register-app.md#register-an-application)」で示されている手順のようにします。

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>ステップ 2: アクセス権を付与する

Azure Time Series Insights 環境によって要求が受信されると、最初に呼び出し元のベアラー トークンが検証されます。 検証に合格すると、呼び出し元は認証され、次に要求されたアクションの実行を呼び出し元が承認されていることを確認するための別のチェックが行われます。 ユーザーまたはサービス プリンシパルを承認するには、最初に、閲覧者ロールまたは共同作成者ロールをそれらに割り当てることによって、環境へのアクセスを許可する必要があります。

- [Azure portal](https://portal.azure.com/) の UI を使用してアクセスを許可するには、記事「[環境へのデータ アクセスの許可](concepts-access-policies.md)」に記載されている手順に従います。 ユーザーを選択するときは、名前または ID でマネージド ID またはアプリの登録を検索できます。

- Azure CLI を使用してアクセスを許可するには、次のコマンドを実行します。 アクセスの管理に使用できるコマンドの完全な一覧については、[こちら](/cli/azure/ext/timeseriesinsights/tsi/access-policy)のドキュメントを参照してください。

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Azure CLI の timeseriesinsights 拡張機能には、バージョン 2.11.0 以降が必要です。 この拡張機能は、az tsi access-policy コマンドを初めて実行したときに自動的にインストールされます。 拡張機能の[詳細を参照してください](/cli/azure/azure-cli-extensions-overview)。

## <a name="step-3-requesting-tokens"></a>ステップ 3: トークンを要求する

マネージド ID またはアプリの登録がプロビジョニングされ、ロールが割り当てられたら、それを使用して OAuth 2.0 ベアラー トークンの要求を始めることができます。 トークンの取得に使用する方法は、コードがホストされている場所と選択した言語によって異なります。 リソース (トークンの "対象ユーザー" とも呼ばれます) を指定するときは、URL または GUID によって Azure Time Series Insights を識別できます。

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> リソース ID として URL を使用する場合は、トークンが `https://api.timeseries.azure.com/` に正確に発行されるようにする必要があります。 末尾にスラッシュが必要です。

> * したがって、[Postman](https://www.getpostman.com/) を使用する場合は、**AuthURL** は次のようになります: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` は有効ですが、`https://api.timeseries.azure.com` は有効ではありません。

### <a name="managed-identities"></a>マネージド ID

Azure App Service または Functions からアクセスする場合は、「[Azure リソースのトークンを取得する](../app-service/overview-managed-identity.md)」のガイダンスに従ってください。

.NET のアプリケーションと関数の場合、マネージド ID を利用する最も簡単な方法は、.NET 用の [Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)を使用することです。 このクライアント ライブラリは、簡単でセキュリティ上の利点があるため、よく使われています。 開発者はコードを 1 回だけ記述すればよく、後はクライアント ライブラリにより、アプリケーションの環境 (開発者のアカウントを使用する開発者ワークステーション上か、またはマネージド サービス ID を使用して Azure にデプロイされるか) に基づいて、認証方法が決定されます。 前の AppAuthentication ライブラリからの移行に関するガイダンスについては、「[AppAuthentication から Azure.Identity への移行ガイダンス](/dotnet/api/overview/azure/app-auth-migration)」を参照してください。

C# と .NET 用 Azure ID クライアント ライブラリを使用して、Azure Time Series Insights 用のトークンを要求します。

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>アプリの登録

* 開発者は、[Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) を使用して、アプリの登録用のトークンを取得することができます。

MSAL は、次のものを初めとする多くのアプリケーション シナリオで使用できます。

* [シングルページ アプリケーション (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Web アプリケーションのユーザーのサインインとユーザーの代理としての Web API の呼び出し](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web API がサインイン ユーザーの代わりに別のダウンストリーム Web API を呼び出す](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [デスクトップ アプリケーションがサインイン ユーザーの代わりに Web API を呼び出す](../active-directory/develop/scenario-desktop-overview.md)
* [モバイル アプリケーションが、対話形式でサインインしたユーザーの代わりに Web API を呼び出す](../active-directory/develop/scenario-mobile-overview.md)。
* [デスクトップ/サービス デーモン アプリケーションがそれ自体の代理として Web API を呼び出す](../active-directory/develop/scenario-daemon-overview.md)

アプリの登録としてトークンを取得し、Gen2 環境からデータを照会する方法を示す C# コードのサンプルについては、[GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs) のサンプル アプリを参照してください

> [!IMPORTANT]
> [Azure Active Directory 認証ライブラリ (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) を使用している場合は、[MSAL への移行](../active-directory/develop/msal-net-migration.md)に関する記事をご覧ください。

## <a name="common-headers-and-parameters"></a>一般的なヘッダーとパラメーター

このセクションでは、Azure Time Series Insights GA 1 API や GA2 API に対するクエリの作成に使用される、一般的な HTTP 要求ヘッダーとパラメーターについて説明します。 API 固有の要件は、[Azure Time Series Insights REST API リファレンス ドキュメント](/rest/api/time-series-insights/)に詳しく記載されています。

> [!TIP]
> REST API の使用方法、HTTP 要求の作成方法、および HTTP 応答の処理方法の詳細については、[Azure REST API リファレンス](/rest/api/azure/) を参照してください。

### <a name="http-headers"></a>HTTP ヘッダー

必要な要求ヘッダーを以下に示します。

| 必要な要求ヘッダー | 説明 |
| --- | --- |
| 承認 | Azure Time Series Insights で認証を行うには、有効な OAuth 2.0 ベアラー トークンを [Authorization ヘッダー](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)内で渡す必要があります。 |

> [!TIP]
> チャートやグラフと共に [JavaScript クライアント SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) を使用して、プログラムによって Azure Time Series Insights API で認証を行う方法については、Azure Time Series Insights の[クライアント SDK のサンプルの視覚化](https://tsiclientsample.azurewebsites.net/)に関する記事を参照してください。

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
> 必須および省略可能なクエリの情報の詳細については、[参照ドキュメント](/rest/api/time-series-insights/)を参照してください。

必須の URL クエリ文字列パラメーターは、API バージョンによって異なります。

| Release | API バージョンの値 |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

省略可能な URL クエリ文字列パラメーターには、HTTP 要求の実行時間のタイムアウト設定が含まれます。

| 省略可能なクエリパラメーター | 説明 | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 要求実行のサーバー側のタイムアウト。 [環境イベントの取得](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) API と[環境集計の取得](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API にのみ適用できます。 タイムアウト値は、`"PT20S"` など、ISO 8601 の期間の形式で指定され、その範囲は `1-30 s` である必要があります。 既定値は `30 s` です。 | Gen1 |
| `storeType=<storeType>` | ウォーム ストアが有効になっている Gen2 環境では、`WarmStore` または `ColdStore` のいずれかでクエリを実行できます。 クエリ内のこのパラメーターは、クエリを実行する必要があるストアを定義します。 定義されていない場合は、コールドストアでクエリが実行されます。 ウォームストアに対してクエリを実行するには、**storeType** を `WarmStore`に設定する必要があります。 定義されていない場合は、コールドストアに対してクエリが実行されます。 | Gen2 |

## <a name="next-steps"></a>次の手順

* Gen1 Azure Time Series Insights API を呼び出すサンプル コードについては、[C# を使用した Gen1 データの照会](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。

* Gen2 Azure Time Series Insights API のコード サンプルを呼び出すサンプル コードについては、[C# を使用した Gen2 データの照会](./time-series-insights-update-query-data-csharp.md)に関する記事を参照してください。

* API リファレンスについては、[クエリ API リファレンス](/rest/api/time-series-insights/reference-query-apis)のドキュメントを参照してください。
