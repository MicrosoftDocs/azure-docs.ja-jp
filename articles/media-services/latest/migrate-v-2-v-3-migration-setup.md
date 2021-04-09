---
title: Media Services v2 から v3 への移行のセットアップ
description: この記事は、Azure Media Services v2 から v3 に移行するための環境のセットアップに役立ちます。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 移行, ストリーム, ブロードキャスト, ライブ, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 792f4f81d84b61f0f78c53e62168921221b4f330
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503166"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>手順 3 - V3 REST API またはクライアント SDK に移行するための設定

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-3.svg)

ここでは、Media Services V3 API を使用するように環境を設定する手順について説明します。

## <a name="sdk-model"></a>SDK モデル

V2 API では、2 つの異なるクライアント SDK がありました。1 つは管理 API 用で、アカウントをプログラムで作成することができ、もう 1 つはリソース管理用でした。

以前は、開発者は Azure サービス プリンシパルのクライアント ID とクライアント シークレットを、AMS アカウントの特定の V2 REST API エンドポイントと共に使用していました。

V3 API は、Azure Resource Management (ARM) に基づいています。 これは、Azure Active Directory (Azure AD) のサービス プリンシパル ID とキーを使用して API に接続します。 開発者は、API に接続するために、サービス プリンシパルまたはマネージド ID を作成する必要があります。 V3 API では、API は標準の ARM エンドポイントを使用し、他のすべての Azure サービスと同様の一貫性のあるモデルを使用します。

V2 アカウントを管理するために、以前に 2015-10-01 バージョンの ARM 管理 API を使用していたお客様は、V3 API アクセス用にサポートされている 2020-05-01 バージョンの ARM 管理 API を使用する必要があります。

## <a name="create-a-new-media-services-account-for-testing"></a>テスト用の新しいメディア サービス アカウントを作成する

Azure portal を使用して[環境を設定する](how-to-set-azure-subscription.md?tabs=portal)ためのクイックスタートの手順に従ってください。 このテスト アカウントで使用する新しい Azure AD アプリケーション ID とシークレットを生成するには、API アクセスとサービス プリンシパル認証を選択します。

[メディア サービス アカウントを作成する](create-account-howto.md?tabs=portal)。
[Media Services API にアクセスするための資格情報を取得する](access-api-howto.md?tabs=portal)。

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>任意のクライアント SDK をダウンロードして環境を設定する

-  [.NET](/dotnet/api/overview/azure/mediaservices/management)、.Net Core、 [Node.js](/javascript/api/overview/azure/mediaservices/management)、 [Python](/python/api/overview/azure/mediaservices/management)、 [Java](/java/api/overview/azure/mediaservices/management)、 [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)、および [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) で利用できる SDK。
- 単純なスクリプト サポートのための [Azure CLI](/cli/azure/ams)  統合。

> [!NOTE]
> V3 の Azure Media Services では、コミュニティの PHP SDK を利用できなくなりました。 V2 で PHP を使用している場合は、コード内で直接 REST API に移行する必要があります。

## <a name="open-api-specifications"></a>Open API 仕様

- V3 は、Azure Resource Manager 上に構築された管理と操作の両方の機能を公開する統一された API サーフェスに基づいています。 Azure Resource Manager テンプレートを使って、変換、ストリーミング エンドポイント、ライブ イベントなどを作成してデプロイできます。

- [OpenAPI 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (旧称 Swagger) ドキュメントでは、すべてのサービス コンポーネントのスキーマについて説明しています。

- すべてのクライアント SDK は、GitHub で公開されている Open API 仕様から派生し、生成されます。 この記事の公開時点では、最新の Open API 仕様は GitHub で公開管理されています。 2020-05-01 バージョンは、[最新の安定版リリース](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)です。

## <a name="rest"></a>[REST](#tab/rest)

Media Services v3 REST API 呼び出し用に [Postman](./media-rest-apis-with-postman.md) を使用します。
[REST API リファレンス ページ](/rest/api/media/)を参照してください。

Postman コレクションの 2020-05-01 バージョン文字列を使用する必要があります。

## <a name="net"></a>[.NET](#tab/net)

[.NET を使用した Media Services v3 API への接続](configure-connect-dotnet-howto.md)に関する記事を参照して、環境を設定します。

PackageManager を使用して最新の SDK をインストールするだけの場合は、次のコマンドを使用します。

```Install-Package Microsoft.Azure.Management.Media```

または、.NET CLI を使用して最新の SDK をインストールするには、次のコマンドを使用します。

```dotnet add package Microsoft.Azure.Management.Media```

また、さまざまなシナリオについての .NET のサンプル一式は、[Azure-Samples/media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) で入手できます。 このリポジトリ内のプロジェクトでは、v3 バージョンを使用してさまざまな Azure Media Services シナリオを実装する方法が示されています。

### <a name="get-started-adjusting-your-code"></a>コードの調整を開始する

V3 API へのアップグレード プロセスを開始するには、コード ベースで `CloudMediaContext` の使用例を見つけます。

次のコードは、v2 API が以前 v2 .NET SDK を使用してどのようにアクセスされていたかを示しています。 開発者はまず `CloudMediaContext` を作成し、`AzureAdTokenCredentials` オブジェクトを使用してインスタンスを作成します。

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

[Java を使用した Media Services v3 API への接続](configure-connect-java-howto.md)に関する記事を参照して、環境を設定します。

## <a name="python"></a>[Python](#tab/python)

[Azure Media Services v3 API への接続 - Python](configure-connect-python-howto.md) に関する記事を参照して、環境を設定します。

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Azure Media Services v3 API への接続 - Node.js](configure-connect-nodejs-howto.md) に関する記事を参照して、環境を設定します。

## <a name="ruby"></a>[Ruby](#tab/ruby)

- GitHub で [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK を入手します。

## <a name="go"></a>[Go](#tab/go)

[Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK をダウンロードします。

---

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]