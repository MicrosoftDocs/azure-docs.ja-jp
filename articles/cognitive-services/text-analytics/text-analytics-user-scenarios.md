---
title: Text Analytics API のユーザー シナリオの例
titleSuffix: Azure Cognitive Services
description: この記事では、Text Analytics API をサービスおよびプロセスに統合するための一般的なシナリオをいくつか確認できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97505360"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Text Analytics API のユーザー シナリオの例

Text Analytics API は、テキストに対する高度な自然言語処理を提供するクラウドベースのサービスです。 この記事では、この API をビジネス ソリューションおよびプロセスに統合するためのユース ケースの例をいくつか紹介します。 

## <a name="analyze-survey-results"></a>調査結果を分析する

感情分析を使用して未加工のテキスト応答を処理することにより、顧客と従業員の調査結果から分析情報を引き出します。 解析、フォローアップ、およびエンゲージメントの推進のために調査結果を集約します。

![顧客および従業員調査で感情分析を実行する方法を説明する画像。](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>記録された受信カスタマー コールを分析する

音声テキスト変換、感情分析、およびキー フレーズ抽出を使用して、カスタマー サービスの通話から分析情報を引き出します。 顧客の理解を深め、カスタマー サービスの傾向を明らかにし、顧客エンゲージメントを促進するために、結果を Power BI ダッシュボードまたはポータルに表示します。 API 要求をレポートのためにバッチとして送信するか、または介入のためにリアルタイムで送信します。 [GitHub 上](https://github.com/rlagh2/callcenteranalytics)のサンプル コードを参照してください。

![カスタマー サービスの問い合わせからの分析情報の取得をセンチメント分析を使用して自動化する方法を説明する画像](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>サポート インシデントを処理および分類する

キー フレーズ抽出とエンティティ認識を使用して、構造化されていないテキスト形式で送信されたサポート要求を処理します。 抽出したフレーズとエンティティを使用して、リソース計画と傾向解析のために要求を分類します。

![キー フレーズ抽出とエンティティ認識を使用してインシデント レポートと傾向を分類する方法を説明する画像](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>商品のソーシャル メディア フィードを監視する

商品の Twitter または Facebook ページで商品に関するユーザーのフィードバックを監視します。 このデータを使用して、新商品の発売に対する顧客の感情を分析したり、機能や機能要求に関するキー フレーズを抽出したり、発生した顧客の苦情に対処したりできます。 [Microsoft Power Automate テンプレート](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)の例を参照してください。

![キー フレーズ抽出を使用してソーシャル メディアでの商品や企業のフィードバックを監視する方法を説明する画像](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>機密情報を含んだドキュメントを分類、編集する

ドキュメントに含まれる個人情報や機密情報を識別するには、名前付きエンティティの認識を使用します。 そのデータを使用してドキュメントを分類または編集し、それらを安全に共有できるようにします。

![NER を使用して個人情報を検出したりドキュメントを分類、編集したりする方法を説明する画像](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>意見マイニングを実行する

アンケートでの商品やサービスの特定の特徴に関連した意見、カスタマー フィードバック、特徴に関する意見がテキストで保持されているかどうかをグループ化します。 これを使用すると、商品の発売や改良点、マーケティング活動を案内にしたり、商品やサービスのパフォーマンスを明確に示したりするのに役立ちます。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="ホテルに関する意見の例。":::

## <a name="next-steps"></a>次のステップ

* [Text Analytics API とは](overview.md)
* [クライアント ライブラリを使用して Text Analytics API に要求を送信する](quickstarts/client-libraries-rest-api.md)
