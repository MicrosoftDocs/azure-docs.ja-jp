---
title: 仮想マシン プランの価格 | Azure Marketplace
description: 仮想マシン プランの価格を指定する 3 つの方法について説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255891"
---
<a name="pricing-for-virtual-machine-offers"></a>仮想マシン プランの価格
==================================

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、オファーがパートナー センターに移行された後も引き続き機能します。 統合によりわずかな変更が行われました。 「[Cloud パートナー ポータルの API リファレンス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)」に記載されている変更内容を調べて、パートナー センターへの移行後もコードが引き続き動作することを確認してください。

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
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
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

<a name="new-core-sizes-added-on-722019"></a>2019 年 7 月 2 日に追加された新しいコア サイズ
---------------------------

2019 年 7 月 2 日に、(コア数に基づく) 新しい Azure 仮想マシン サイズに対する新しい料金の追加が VM の発行元に通知されました。  新しい料金は、コア サイズが 10、44、48、60、120、208、416 に対するものです。  既存の VM の場合、これらのコア サイズの新しい価格は、現在の価格に基づいて自動的に計算されます。  発行元は、2019 年 8 月 1 日までに追加料金を確認し、必要な変更を行う必要があります。  この日を過ぎて、発行元がまだ再パブリッシュを行っていない場合、これらの新しいコア サイズに対して自動的に計算された価格が有効になります。


<a name="regions"></a>リージョン
-------

次の表は、カスタマイズされたコア価格に対して指定できるさまざまなリージョンと、それぞれに対応する通貨コードを示します。

| **リージョン** | **名前**             | **通貨コード** |
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
| id         | インドネシア            | IDR               |
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
