---
title: 開発者向けリソース - Language Understanding
description: SDK、REST API、CLI は、ご使用のプログラミング言語で Language Understanding (LUIS) アプリを開発する際に役立ちます。 Azure リソースと LUIS 予測を管理します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98946175"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Language Understanding (LUIS) 用の SDK、REST、CLI 開発者向けリソース

SDK、REST API、CLI は、ご使用のプログラミング言語で Language Understanding (LUIS) アプリを開発する際に役立ちます。 Azure リソースと LUIS 予測を管理します。

## <a name="azure-resource-management"></a>Azure Resource Management

Azure Cognitive Services 管理レイヤーを使用して、Language Understanding または Cognitive Service リソースの作成、編集、一覧表示、削除を行います。

ツールに基づくリファレンス ドキュメントを検索します。

* [Azure CLI](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding の作成と予測要求

Language Understanding サービスには、作成する必要がある Azure リソースからアクセスします。 次の 2 つのリソースがあります。

* トレーニング用の **作成** リソースを使用して、作成、編集、トレーニング、公開を行います。
* ランタイム用の **予測** を使用すると、ユーザーのテキストを送信し、予測を受け取ることができます。

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

最も一般的なタスクについて学習し、使用するには、[Cognitive Services のサンプル コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code)を使用します。

### <a name="rest-specifications"></a>REST の仕様

[LUIS REST 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)は、すべての [Azure REST 仕様](https://github.com/Azure/azure-rest-api-specs)と共に GitHub で一般提供されています。

### <a name="rest-apis"></a>REST API

作成と予測の両方のエンドポイント API は、REST API から入手できます。

|Type|Version|
|--|--|
|Authoring|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[プレビュー V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|予測|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST エンドポイント

現在、LUIS には次の 2 種類のエンドポイントがあります。

* トレーニング エンドポイントでの **作成**
* ランタイム エンドポイントでのクエリ **予測**。

|目的|URL|
|--|--|
|トレーニング エンドポイントでの V2 作成|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|トレーニング エンドポイントでの V3 作成|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 予測 - ランタイム エンドポイントでのすべての予測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 予測 - ランタイム エンドポイントでのバージョン予測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 予測 - ランタイム エンドポイントでのスロット予測|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

次の表は、前の表で中かっこ `{}` で囲まれているパラメーターの説明です。

|パラメーター|目的|
|--|--|
|`your-resource-name`|Azure リソース名|
|`q` または `query`|チャット ボットなどのクライアント アプリケーションから送信される発話テキスト|
|`version`|10 文字のバージョン名|
|`slot`| `production` または `staging`|

### <a name="rest-query-string-parameters"></a>REST クエリ文字列パラメーター

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>アプリ スキーマ

[アプリ スキーマ](app-schema-definition.md)は、`.json` または `.lu` 形式でインポートおよびエクスポートされます。

### <a name="language-based-sdks"></a>言語ベースの SDK

|Language |リファレンス ドキュメント|Package|クイックスタート|
|--|--|--|--|
|C#|[作成](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[予測](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet の作成](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet の予測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[作成](./client-libraries-rest-api.md?pivots=rest-api)<br>[クエリ予測](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[作成と予測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[作成と予測](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven の作成](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven の予測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[作成](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[予測](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM の作成](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM の予測](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[作成](./client-libraries-rest-api.md?pivots=rest-api)<br>[予測](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[作成と予測](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[作成](./client-libraries-rest-api.md?pivots=rest-api)<br>[予測](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Containers

Language Understanding (LUIS) には、アプリのオンプレミス バージョンと包含バージョンを提供するための[コンテナー](luis-container-howto.md)が用意されています。

### <a name="export-and-import-formats"></a>エクスポートとインポートの形式

Language Understanding には、アプリケーションとそのモデルを JSON 形式、`.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 形式、および Language Understanding コンテナー用の圧縮パッケージで管理する機能が用意されています。

これらの形式のインポートとエクスポートは、API と LUIS ポータルから利用できます。 ポータルでは、アプリの一覧とバージョンの一覧の一部としてインポートとエクスポートが提供されています。

## <a name="workshops"></a>ワークショップ

* GitHub:(ワークショップ) [Conversational-AI :NLU using LUIS (会話 AI: LUIS を使用した NLU)](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>継続的インテグレーション ツール

* GitHub:(プレビュー) [Developing a LUIS app using DevOps practices (DevOps プラクティスを使用した LUIS アプリの開発)](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub:[NLU.DevOps](https://github.com/microsoft/NLU.DevOps) - NLU サービスの継続的インテグレーションとデプロイをサポートするツールです。

## <a name="bot-framework-tools"></a>Bot Framework ツール

Bot Framework は、さまざまな言語の [SDK](https://github.com/Microsoft/botframework) として、[Azure Bot Service](https://dev.botframework.com/) を使用するサービスとして使用できます。

Bot Framework には、次のような Language Understanding に役立つ[いくつかのツール](https://github.com/microsoft/botbuilder-tools)が用意されています。
* [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) - ボット開発者が Bot Framework SDK を使用して構築されたボットをテストおよびデバッグできるデスクトップ アプリケーションです
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - 開発者およびさまざまな分野から成るチームが Microsoft Bot Framework を使用してボットや会話エクスペリエンスを作成できるようにするのための統合開発ツールです
* [Bot Framework サンプル](https://github.com/microsoft/botbuilder-samples) - #C、JavaScript、TypeScript、Python

## <a name="next-steps"></a>次のステップ

* 一般的な [HTTP エラー コード](luis-reference-response-codes.md)について学習します。
* すべての API と SDK の[リファレンス ドキュメント](../../index.yml)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) と [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitive コンテナー](../cognitive-services-container-support.md)
