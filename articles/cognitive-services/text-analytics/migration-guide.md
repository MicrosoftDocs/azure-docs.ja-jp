---
title: Text Analytics API の v2 の移行ガイド
titleSuffix: Azure Cognitive Services
description: Text Analytics API のバージョン 3 を使用するためにアプリケーションを移行する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85412585"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Text Analytics API のバージョン 3.x への移行

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Text Analytics API のバージョン 2.1 を使用している場合、この記事は、アプリケーションをアップグレードしてバージョン 3.x を使用する際に役立ちます。 バージョン3.0 は一般提供されており、拡張された[固有表現認識 (NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) や[モデルのバージョン管理](concepts/model-versioning.md)などの新機能が導入されています。 v3.1 のプレビュー バージョン (v3.1-preview.x) も利用可能です。これには、[意見マイニング](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)などの機能が追加されています。 v2 で使用されていたモデルは、今後の更新を受け取りません。 

#### <a name="sentiment-analysis"></a>[感情分析](#tab/sentiment-analysis)

## <a name="feature-changes"></a>機能の変更 

バージョン 2.1 の感情分析では、API に送信された各ドキュメントに対するセンチメント スコアが 0 から 1 の間で返されます。この場合、スコアは 1 に近いほど、より肯定的な感情を示します。 バージョン 3 では、文とドキュメント全体の両方に対するセンチメント ラベル ("肯定的" や "否定的" など) と、それらに関連付けられた信頼度スコアが返されます。 

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API を使用している場合は、その要求エンドポイントを感情分析用の v3 エンドポイントに更新します。 たとえば、`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` となります。 また、[JSON 応答](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)で返されるセンチメント ラベルを使用するようアプリケーションを更新することも必要になります。 

### <a name="client-libraries"></a>クライアント ライブラリ

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER とエンティティ リンク設定](#tab/named-entity-recognition)

## <a name="feature-changes"></a>機能の変更

> [!NOTE] 
> 現在、[v3 のエンティティ カテゴリ](named-entity-types.md)は、英語およびスペイン語のテキストの場合にのみ返されます。 この API は、他の言語の要求に対して、バージョン 2.1 でサポートされていれば、バージョン 2.1 の結果を返します。

バージョン 2.1 の Text Analytics API では、固有表現認識 (NER) とエンティティ リンク設定に 1 つのエンドポイントが使用されます。 バージョン 3 では、名前付きエンティティの検出が拡張され、NER とエンティティ リンク設定に個別のエンドポイントが使用されます。 v3.1-preview.1 以降、NER はさらに個人 `pii` と医療 `phi` の情報を検出できます。 

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API を使用している場合は、その要求エンドポイントを NER またはエンティティ リンク設定用の v3 エンドポイントに更新します。

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

また、[JSON 応答](how-tos/text-analytics-how-to-entity-linking.md#view-results)で返される[エンティティ カテゴリ](named-entity-types.md)を使用するようアプリケーションを更新することも必要になります。

### <a name="client-libraries"></a>クライアント ライブラリ

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[言語検出](#tab/language-detection)

## <a name="feature-changes"></a>機能の変更 

言語検出機能は、v3 ではエンドポイントのバージョン以外に変更されていませんが、JSON 応答には `score` ではなく `ConfidenceScore` が含まれます。 また、V3 では、出力で 1 つの言語のみが返されます。 

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API を使用している場合は、その要求エンドポイントを言語検出用の v3 エンドポイントに更新します。 たとえば、`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` となります。 また、[JSON 応答](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)で `score` ではなく `ConfidenceScore` を使用するようアプリケーションを更新することも必要になります。 

### <a name="client-libraries"></a>クライアント ライブラリ

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[キー フレーズ抽出](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>機能の変更 

キー フレーズ抽出機能は、v3 ではエンドポイントのバージョン以外に変更されていません。

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API を使用している場合は、その要求エンドポイントをキー フレーズ抽出用の v3 エンドポイントに更新します。 例: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>クライアント ライブラリ

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>関連項目

* [Text Analytics API v2 のリファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Text Analytics API とは](overview.md)
* [言語サポート](language-support.md)
* [モデルのバージョン管理](concepts/model-versioning.md)