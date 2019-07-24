---
title: 仮想マシン プランの価格 | Microsoft Docs
description: 仮想マシン プランの価格を指定する 3 つの方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a029477dfd8046863ebfe34cd839562a0b1f3d87
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607797"
---
<a name="pricing-for-virtual-machine-offers"></a>仮想マシン プランの価格
==================================

仮想マシン プランの価格を指定する方法は、カスタマイズされたコア価格、コアごとの価格、およびスプレッドシート価格の 3 つを提供します。


<a name="customized-core-pricing"></a>カスタマイズされたコア価格
-----------------------

価格は、リージョンとコアの組み合わせごとに固有です。 販売リスト内のすべてのリージョンを、定義の **virtualMachinePricing**/**regionPrices** セクションで指定する必要があります。  要求で、[リージョン](#regions)ごとに正しい通貨コードを使用します。  次の例はこれらの要件を示しています。

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>コアごとの価格
----------------

この場合、発行元は、SKU に米国ドルの 1 つの価格を指定し、他の価格はすべて自動的に生成されます。 コアごとの価格は、要求の**単一**のパラメーターで指定されます。

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>スプレッドシート価格
-------------------

発行元は、価格のスプレッドシートを一時的な保存場所にアップロードし、他のファイル成果物のように、要求に URI を含めます。 スプレッドシートは、続いてアップロードされ、指定された価格スケジュールを評価するために変換され、最終的に価格情報でオファーを更新します。 オファーに対する後続の GET 要求は、スプレッドシート URI とリージョンの評価された価格を返します。

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>リージョン
-------

次の表は、カスタマイズされたコア価格に対して指定できるさまざまなリージョンと、それぞれに対応する通貨コードを示します。

| **[リージョン]** | **Name**             | **通貨コード** |
|------------|----------------------|-------------------|
| DZ         | アルジェリア              | DZD               |
| AR         | アルゼンチン            | ARS               |
| AU         | オーストラリア            | AUD               |
| AT         | オーストリア              | EUR               |
| BH         | バーレーン              | BHD               |
| BY         | ベラルーシ              | RUB               |
| BE         | ベルギー              | EUR               |
| BR         | ブラジル               | USD               |
| BG         | ブルガリア             | BGN               |
| CA         | カナダ               | CAD               |
| CL         | チリ                | CLP               |
| CO         | コロンビア             | COP               |
| CR         | コスタリカ           | CRC               |
| HR         | クロアチア              | HRK               |
| CY         | キプロス               | EUR               |
| CZ         | チェコ共和国       | CZK               |
| DK         | デンマーク              | DKK               |
| DO         | ドミニカ共和国   | USD               |
| EC         | エクアドル              | USD               |
| EG         | エジプト                | EGP               |
| SV         | エルサルバドル          | USD               |
| EE         | エストニア              | EUR               |
| FI         | フィンランド              | EUR               |
| FR         | フランス               | EUR               |
| DE         | ドイツ              | EUR               |
| GR         | ギリシャ               | EUR               |
| GT         | グアテマラ            | GTQ               |
| HK         | 香港特別行政区        | HKD               |
| HU         | ハンガリー              | HUF               |
| IS         | アイスランド              | ISK               |
| IN         | インド                | INR               |
| ID         | インドネシア            | IDR               |
| IE         | アイルランド              | EUR               |
| IL         | イスラエル               | ILS               |
| IT         | イタリア                | EUR               |
| JP         | 日本                | JPY               |
| JO         | ヨルダン               | JOD               |
| KZ         | カザフスタン           | KZT               |
| KE         | ケニア                | KES               |
| KR         | 韓国                | KRW               |
| KW         | クウェート               | KWD               |
| LV         | ラトビア               | EUR               |
| LI         | リヒテンシュタイン        | CHF               |
| LT         | リトアニア            | EUR               |
| LU         | ルクセンブルク           | EUR               |
| MK         | 北マケドニア      | MKD               |
| MY         | マレーシア             | MYR               |
| MT         | マルタ                | EUR               |
| MX         | メキシコ               | MXN               |
| ME         | モンテネグロ           | EUR               |
| MA         | モロッコ              | MAD               |
| NL         | オランダ          | EUR               |
| NZ         | ニュージーランド          | NZD               |
| NG         | ナイジェリア              | NGN               |
| NO         | ノルウェー               | NOK               |
| OM         | オマーン                 | OMR               |
| PK         | パキスタン             | PKR               |
| PA         | パナマ               | USD               |
| PY         | パラグアイ             | PYG               |
| PE         | ペルー                 | PEN               |
| PH         | フィリピン          | PHP               |
| PL         | ポーランド               | PLN               |
| PT         | ポルトガル             | EUR               |
| PR         | プエルトリコ          | USD               |
| QA         | カタール                | QAR               |
| RO         | ルーマニア              | RON               |
| RU         | ロシア               | RUB               |
| SA         | サウジアラビア         | SAR               |
| RS         | セルビア               | RSD               |
| SG         | シンガポール            | SGD               |
| SK         | スロバキア             | EUR               |
| SI         | スロベニア             | EUR               |
| ZA         | 南アフリカ         | ZAR               |
| ES         | スペイン                | EUR               |
| LK         | スリランカ            | USD               |
| SE         | スウェーデン               | SEK               |
| CH         | スイス          | CHF               |
| TW         | 台湾               | TWD               |
| TH         | タイ             | THB               |
| TT         | トリニダード・トバゴ  | TTD               |
| TN         | チュニジア              | TND               |
| TR         | トルコ               | TRY               |
| UA         | ウクライナ              | UAH               |
| AE         | アラブ首長国連邦 | EUR               |
| GB         | イギリス       | GBP               |
| US         | 米国        | USD               |
| UY         | ウルグアイ              | UYU               |
| VE         | ベネズエラ            | USD               |
|  |  |  |
