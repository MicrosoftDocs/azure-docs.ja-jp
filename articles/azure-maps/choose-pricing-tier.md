---
title: Microsoft Azure Maps の適切な価格レベルの選択
description: Azure Maps の価格レベルについて説明します。 どの機能がどのレベルで提供されているかを確かめ、価格レベルの選択に関する重要な考慮事項を確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905300"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps での適切な価格レベルの選択

Azure Maps では 2 つの価格レベルが提供されています。S0 と S1 です。 この記事は、ニーズにあった適切な価格レベルを選択するのに役立ちます。 適切な価格レベルを選択するには、次の 2 つの質問を自分に尋ねてください。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>サポートする同時実行ユーザーの数はどれくらいですか

S0 価格レベルと S1 価格レベルでは、処理されるデータ スループットの量が異なります。 S0 価格レベルでは、**1 秒あたり最大 50 個のクエリ** が処理されます。 一方、S1 レベルでは、**1 秒あたり 50 個を超えるクエリ** が処理されます。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>どのような地理空間機能を使用する予定ですか

コア地理空間 API で、自分のサービス要件が満たされる場合は、S0 価格レベルを選択してください。 アプリケーションでより高度な機能が必要な場合は、S1 価格レベルを選択することを検討してください。 高度な機能は次のとおりです。航空写真とハイブリッド画像、ルート範囲の取得、バッチ ジオコーディング。 アプリケーションに最適な価格レベルを選択するには、下の「**価格レベルの機能**」の表を確認してください。

### <a name="pricing-tier-capabilities"></a>価格レベルの機能

| 機能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| マップの表示                              | ✓                   | ✓       |
| 衛星映像                       |                     | ✓        |
| 検索                                  | ✓                    | ✓        |
| バッチ検索                            |                     | ✓        |
| ルート                                   | ✓                    |✓        |
| バッチ ルーティング                            |                    | ✓        |
| マトリックス ルーティング                          |                     | ✓        |
| ルート範囲 (等時線)                |                     | ✓        |
| トラフィック                                |✓                    |✓        |
| タイム ゾーン                               |✓                    |✓        |
| 位置情報 (プレビュー)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| ジオフェンシング                                |                    |✓        |
| Azure Maps Data (Preview)                |                     | ✓        |
| Mobility (プレビュー)                       |                     | ✓        |
| Weather (プレビュー)                        |✓                    |✓        |
|  Creator (プレビュー)                         |                   |✓        |
|  Elevation (プレビュー)                        |                   |✓        |

さらに次の点も考慮してください。

* どのような種類の企業か。
* アプリケーションはどのくらい重要か。

### <a name="pricing-tier-targeted-customers"></a>価格レベルの対象のお客様

S0 および S1 の価格レベルをさらによく理解するには、「**価格レベルの対象のお客様**」の表をご覧ください。 詳細については、「[Azure Maps の価格](https://azure.microsoft.com/pricing/details/azure-maps/)」をご覧ください。 

| Pricing tier  |     対象となるお客様                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    S0 価格レベルは、概念実証開発や早い段階のテストからアプリケーションの運用やデプロイまで、すべての運用ステージのアプリケーションで利用できます。 ただし、このレベルは、小規模開発、同時ユーザー数の少ないお客様、またはその両方向けに設計されています。 
| S1            |    S1 価格レベルは、大規模なエンタープライズ アプリケーション、ミッションクリティカルなアプリケーション、または多数の同時実行ユーザーが含まれているお客様に適しています。 また、高度な地理空間サービスを必要とするお客様にも適しています。

## <a name="next-steps"></a>次のステップ

価格レベルを表示および変更する方法について詳しく学びます。

> [!div class="nextstepaction"]
> [価格レベルを管理する](how-to-manage-pricing-tier.md)
