---
title: Text Analytics API のユーザー シナリオの例
titleSuffix: Azure Cognitive Services
description: この記事では、Text Analytics API をサービスおよびプロセスに統合するための一般的なシナリオをいくつか確認できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8f8d110a13e768d93f0be3c4baaf5715436f2ed
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502415"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Text Analytics API のユーザー シナリオの例

Text Analytics API は、テキストに対する高度な自然言語処理を提供するクラウドベースのサービスです。 この記事では、この API をビジネス ソリューションおよびプロセスに統合するためのユース ケースの例をいくつか紹介します。 

## <a name="analyze-survey-results"></a>調査結果を分析する

感情分析を使用して未加工のテキスト応答を処理することにより、顧客と従業員の調査結果から分析情報を引き出します。 解析、フォローアップ、およびエンゲージメントの推進のために調査結果を集約します。

![顧客および従業員調査で感情分析を実行する方法を説明する画像。](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>記録された受信カスタマー コールを分析する

テキスト読み上げ、感情分析、およびキー フレーズ抽出を使用して、カスタマー サービスから分析情報を引き出します。 顧客の理解を深め、カスタマー サービスの傾向を明らかにし、顧客エンゲージメントを促進するために、結果を Power BI ダッシュボードまたはポータルに表示します。 API 要求をレポートのためにバッチとして送信するか、または介入のためにリアルタイムで送信します。 

![カスタマー サービスの問い合わせからの分析情報の取得をセンチメント分析を使用して自動化する方法を説明する画像](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>サポート インシデントを処理および分類する

キー フレーズ抽出とエンティティ認識を使用して、構造化されていないテキスト形式で送信されたサポート要求を処理します。 抽出したフレーズとエンティティを使用して、リソース計画と傾向解析のために要求を分類します。

![キー フレーズ抽出とエンティティ認識を使用してインシデント レポートと傾向を分類する方法を説明する画像](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>商品のソーシャル メディア フィードを監視する

商品の Twitter または Facebook ページで商品に関するユーザーのフィードバックを監視します。 このデータを使用して、新商品の発売に対する顧客の感情を分析したり、機能や機能要求に関するキー フレーズを抽出したり、発生した顧客の苦情に対処したりできます。

![キー フレーズ抽出を使用してソーシャル メディアでの商品や企業のフィードバックを監視する方法を説明する画像](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>次の手順

* [Text Analytics API とは](overview.md)
* [C# を使用して Text Analytics API に要求を送信する](quickstarts/csharp.md)