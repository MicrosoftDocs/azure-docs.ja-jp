---
title: Azure Maps でサポートされている言語 | Microsoft Docs
description: Azure Maps のサービスでサポートされている言語について説明します
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446182"
---
# <a name="localization-support-in-azure-maps"></a>Azure Maps でのローカライズのサポート

Azure Maps は、さまざまな言語および国/地域に基づくビューをサポートします。 このアーティクルでは、サポートされる言語と、Azure Maps の実装に役立つビューを提供します。


## <a name="azure-maps-supported-languages"></a>Azure Maps でサポートされている言語

Azure Maps は、サービス全体にわたってさまざまな言語にローカライズされています。 次の表は、各サービスでサポートされている言語コードを示しています。  
  

| id         | Name                   |  マップ | Search | ルーティング | トラフィック インシデント | JS マップ コントロール | タイム ゾーン |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | アフリカーンス語              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | アラビア語                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | バスク語                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | ブルガリア語              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | カタルニア語                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | 中国語 (簡体字)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | 中国語 (繁体字)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | クロアチア語               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | チェコ語                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | デンマーク語                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | オランダ語                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | オランダ語 (ベルギー)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | 英語 (オーストラリア)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | 英語 (ニュージーランド)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | 英語 (英国) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | 英語 (米国)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | エストニア語               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | フィンランド語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | フランス語                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | フランス語 (カナダ)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | ガリシア語               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | ドイツ語                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | ギリシャ語                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | ヘブライ語                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | ヒンディー語                  |       |        |         |                   |                |     ✓     |
| hu-HU      | ハンガリー語              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | インドネシア語             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | イタリア語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | 日本語               |       |        |         |                   |                |     ✓     |
| kk-KZ      | カザフ語                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | 韓国語                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | スペイン語 (ラテンアメリカ) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | ラトビア語                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | リトアニア語             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | マレー語 (ラテン)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | ノルウェー語 (ブークモール)       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutral Ground Truth - 地域の書記体系 (利用可能な場合) でのすべてのリージョンに対する公用語 |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutral Ground Truth - ラテンの異名。 可能な場合はラテン文字が使用されます |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | ポーランド語                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | ポルトガル語 (ブラジル)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | ポルトガル語 (ポルトガル)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | ルーマニア語               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | ロシア語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | セルビア語 (キリル文字)     |       |    セルビア語 (キリル) (sr-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | セルビア語 (ラテン)        |       |        |         |                   |                |     ✓     |
| sk-SK      | スロバキア語              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | スロベニア語              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | スペイン語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | スペイン語 (メキシコ)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | スウェーデン語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | タイ語                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | トルコ語                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | ウクライナ語               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | ベトナム語             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Azure Maps でサポートされているビュー

> [!Note]
> Azure Maps は、2019 年 8 月 1 日に次の国/地域でリリースしています。
>  * アルゼンチン
>  * インド
>  * モロッコ
>  * パキスタン
>
> 2019 年 8 月 1 日以降、**View** パラメーターの設定で、上記の新しい地域/国に対して返されるマップ コンテンツが定義されます。 必要に応じて、ご自分のサービスに使用されている REST API および SDK に View パラメーターを設定することをお勧めします。
>  
>
>  **Rest API:**
>  
>  必要に応じて View パラメーターを設定します。 View パラメーターには、Azure Maps サービスを介して返される地政学的な紛争のあるコンテンツのセットを指定します。 
>
>  影響を受ける Azure Maps REST サービス:
>    
>    * Get Map Tile
>    * Get Map Image 
>    * Get Search Fuzzy
>    * Get Search POI
>    * Get Search POI Category
>    * Get Search Nearby
>    * Get Search Address
>    * Get Search Address Structured
>    * Get Search Address Reverse
>    * Get Search Address Reverse Cross Street
>    * Post Search Inside Geometry
>    * Post Search Address Batch Preview
>    * Post Search Address Reverse Batch Preview
>    * Post Search Along Route
>    * Post Search Fuzzy Batch Preview
>
>    
>  **SDK:**
>
>  必要に応じて View パラメーターを設定し、Web SDK と Android SDK の最新バージョンを用意します。 影響を受ける SDK:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Azure Maps の **View** パラメーター ("ユーザーの地域のパラメーター" とも呼ばれます) は 2 文字の ISO-3166 の国番号であり、マップ上に表示される国境やラベルなど、その国/地域にとって正しいマップが Azure Maps サービスを介して表示されます。 

要求で定義していない場合でも、既定で View パラメーターは **Unified** に設定されています。 ユーザーの場所を特定し、その場所を正しく View パラメーターを設定するのはユーザーの責任です。 または、要求の IP アドレスに基づいてマップ データを返す 'View = Auto' を設定することもできます。  Azure Maps の View パラメーターは、Azure Maps へのアクセスを承認されたマップ、イメージ、およびその他のデータとサード パーティ コンテンツを表示できる国のマッピングに関する法律など、該当する法律に準拠していなければなりません。


次の表に、サポートされるビューを示します。

| 表示         | 説明                            |  マップ | Search | JS マップ コントロール |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | アラブ首長国連邦、AE    |   ✓   |        |     ✓          |
| AR           | アルゼンチン (アルゼンチン ビュー)           |   ✓   |    ✓   |     ✓          |
| BH           | バーレーン (アラビアのビュー)                 |   ✓   |        |     ✓          |
| IN           | インド (インド ビュー)                    |   ✓   |   ✓     |     ✓          |
| IQ           | イラク (アラビアのビュー)                    |   ✓   |        |     ✓          |
| JO           | ヨルダン (アラビアのビュー)                  |   ✓   |        |     ✓          |
| KW           | クウェート (アラビアのビュー)                  |   ✓   |        |     ✓          |
| LB           | レバノン (アラビアのビュー)                 |   ✓   |        |     ✓          |
| MA           | モロッコ (モロッコのビュー)                |   ✓   |   ✓     |     ✓          |
| OM           | オマーン (アラビアのビュー)                    |   ✓   |        |     ✓          |
| PK           | パキスタン (パキスタンのビュー)              |   ✓   |    ✓    |     ✓          |
| PS           | パレスチナ自治政府 (アラビアのビュー)    |   ✓   |        |     ✓          |
| QA           | カタール (アラビアのビュー)                   |   ✓   |        |     ✓          |
| SA           | サウジアラビア (アラビアのビュー)            |   ✓   |        |     ✓          |
| SY           | シリア (アラビアのビュー)                   |   ✓   |        |     ✓          |
| YE           | イエメン (アラビアのビュー)                   |   ✓   |        |     ✓          |
| Auto         | 要求の IP アドレスに基づいてマップ データを返します。|   ✓   |    ✓   |     ✓          |
| 統一      | 統一されたビュー (その他)                  |   ✓   |   ✓     |     ✓          |
