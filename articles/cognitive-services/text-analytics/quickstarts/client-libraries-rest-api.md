---
title: クイックスタート:Text Analytics クライアント ライブラリを使用したテキスト マイニング
titleSuffix: Azure Cognitive Services
description: このクイックスタートを使用すると、Azure Cognitive Services の Text Analytics API を使用して、センチメント分析などを実行できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/11/2021
ms.author: aahi
keywords: テキスト マイニング, センチメント分析, テキスト分析
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: d0fae3c12d4315d829ad4d505e7157e3bf919d99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599062"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>クイックスタート: Text Analytics クライアント ライブラリおよび REST API を使用する

この記事では、Text Analytics クライアントライブラリおよび REST API の概要について説明します。 テキスト マイニングのサンプル コードを試すには、こちらの手順に従います。

* センチメント分析
* 意見マイニング
* 言語検出
* エンティティの認識
* 個人が特定される情報の認識
* キー フレーズの抽出


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Text Analytics API の最新の安定バージョンは `3.0` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。
> * Text Analytics for Health または非同期操作を使用する場合は、Github で [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)、または [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 用の例を参照してください。

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Text Analytics API の最新の安定バージョンは `3.0` です。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。
Text Analytics for Health または非同期操作を使用する場合は、Github で [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)、または [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 用の例を参照してください。

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Text Analytics API の最新の安定バージョンは `3.0` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。
> * このバージョンの Text Analytics クライアント ライブラリは、[お使いのブラウザー](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)でも実行できます。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Text Analytics API の最新の安定バージョンは `3.0` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 運用環境のシナリオでは、パフォーマンスとスケーラビリティを確保するために、バッチ処理された非同期メソッドを使用することをお勧めします。 以下のリファレンス ドキュメントを参照してください。 Text Analytics for Health または非同期操作を使用する場合は、Github で [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)、または [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 用の例を参照してください。

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Text Analytics API の最新の安定バージョンは `3.0` です。
>    * 必ず、使用するバージョンの手順にのみ従ってください。

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ソリューションを探す](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
* [言語を検出する](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [言語の認識](../how-tos/text-analytics-how-to-language-detection.md)
