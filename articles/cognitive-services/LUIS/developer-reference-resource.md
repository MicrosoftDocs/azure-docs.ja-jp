---
title: 開発者向けリソース - Language Understanding
titleSuffix: Azure Cognitive Services
description: 開発者には、Language Understanding 用の REST API と SDK の両方があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 97684a4668c1d495fa690674f7bd94e064737bd4
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273945"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding の開発者向けリソース

開発者は、Language Understanding 用の REST API と SDK の両方を使用できます。 

## <a name="azure-resource-management"></a>Azure Resource Management

Azure Cognitive Services 管理レイヤーを使用して、Language Understanding または Cognitive Service リソースの作成、編集、一覧表示、削除を行います。

ツールに基づくリファレンス ドキュメントを検索します。

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding の作成と予測要求

Language Understanding サービスには、作成する必要がある Azure リソースからアクセスします。 作成と予測のエンドポイント リソースという 2 つのリソースがあります。 これらのどちらのリソースでも、LUIS リソースを制御できます。 

### <a name="rest-apis"></a>REST API

作成と予測の両方のエンドポイント API は、REST API から入手できます。

* 作成の[リファレンス ドキュメント](https://go.microsoft.com/fwlink/?linkid=2092087)
* 予測ランタイムの[リファレンス ドキュメント](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>言語ベースの SDK

|言語 |リファレンス ドキュメント|Package|サンプル|クイック スタート|
|--|--|--|--|--|
|C#|[作成](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[予測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet の作成](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet の予測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.NET SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[アプリの作成と管理](sdk-csharp-quickstart-authoring-app.md)<br>[予測エンドポイントに対するクエリの実行](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[作成と予測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST を使用した作成](luis-get-started-go-add-utterance.md)<br>[REST を使用した予測](luis-get-started-go-get-intent.md)|
|Java|[作成と予測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven の作成](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven の予測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[作成](luis-get-started-java-add-utterance.md)<br>[予測](luis-get-started-java-get-intent.md)
|Node.js|[作成](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[予測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM の作成](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM の予測](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST を使用した作成](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[REST を使用した予測](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[作成と予測](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[作成](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[作成](sdk-python-quickstart-authoring-app.md)<br>[REST を使用した予測](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>その他のツールと SDK

Bot Framework は、さまざまな言語の [SDK](https://github.com/Microsoft/botframework) として、[Azure Bot Service](https://dev.botframework.com/) を使用するサービスとして使用できます。 

Bot Framework には、次のような Language Understanding に役立つ[いくつかのツール](https://github.com/microsoft/botbuilder-tools)が用意されています。

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - マークダウン ファイルを使用して LUIS Language Understanding モデルを構築します
* [LUIS Cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - LUIS.ai アプリケーションを作成および管理します
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch) - 親アプリと子アプリを管理します
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - LUIS の意図とエンティティのバッキング C#/Typescript クラスを自動生成します。
* [ボット エミュレーター](https://github.com/Microsoft/BotFramework-Emulator/releases) - ボット開発者が Bot Framework SDK を使用して構築されたボットをテストおよびデバッグできるデスクトップ アプリケーションです


## <a name="next-steps"></a>次の手順

一般的な [HTTP エラー コード](luis-reference-response-codes.md)について学習します。