---
title: 言語サポート - Bing Spell Check API
titleSuffix: Azure Cognitive Services
description: Bing Spell Check API でサポートされる自然言語の一覧。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a97bd6bfc1cb7a0760894faaf3fd0617e6165f58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351199"
---
# <a name="language-and-region-support-for-bing-spell-check-api"></a>Bing Spell Check API でサポートされる言語と地域のサポート

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Spell Check API では次の言語がサポートされています (`spell` モードのみ)。

`en-US` 以外の言語を使用するには、`mkt` を設定し、`Accept-Language` または `setLang` を設定する必要があることに注意してください (「[Spell Check API v7 reference](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)」(Spell Check API v7 リファレンス) を参照)。

| Language    | 言語コード |
|:----------- |:-------------:|
| アラビア語      | `ar`          |
| 中国語 (中華人民共和国)     | `zh-CN`          |
| 中国語 (香港特別行政区)    | `zh-HK`          |
| 中国語 (台湾)     | `zh-TW`          |
| デンマーク語      | `da`          |
| オランダ語 (ベルギー)       | `nl-BE`          |
| オランダ語 (オランダ)      | `nl-NL`          |
| 英語 (オーストラリア)    | `en-AU`          |
| 英語 (カナダ)     | `en-CA`          |
| 英語 (インド)    | `en-IN`          |
| 英語 (インドネシア)     | `en-ID`          |
| 英語 (マレーシア)     | `en-MY`          |
| 英語 (ニュージーランド)    | `en-NZ`          |
| 英語 (フィリピン)     | `en-PH`          |
| 英語 (南アフリカ)    | `en-ZA`          |
| 英語 (イギリス)    | `en-GB`          |
| 英語 (米国)    | `en-US`          |
| フィンランド語     | `fi`          |
| フランス語 (ベルギー)     | `fr-BE`          |
| フランス語 (カナダ)     | `fr-CA`          |
| フランス語 (フランス)     | `fr-FR`          |
| フランス語 (スイス)      | `fr-CH`          |
| ドイツ語 (オーストリア)      | `de-AT`          |
| ドイツ語 (ドイツ)      | `de-DE`          |
| ドイツ語 (スイス)      | `de-CH`          |
| イタリア語     | `it`          |
| 日本語    | `ja`          |
| 韓国語      | `ko`          |
| ノルウェー語   | `no`          |
| ポーランド語      | `pl`          |
| ポルトガル語 (ブラジル)   | `pt-BR`|
| ポルトガル語 (ポルトガル) | `pt-PT`|
| ロシア語     | `ru`          |
| スペイン語 (アルゼンチン)    | `es-AR`          |
| スペイン語 (チリ)     | `es-CL`          |
| スペイン語 (メキシコ)    | `es-MX`          |
| スペイン語 (スペイン)    | `es-ES`          |
| スペイン語 (米国)    | `es-US`          |
| スウェーデン語     | `sv`          |
| トルコ語     | `tr`          |

## <a name="see-also"></a>関連項目

- [Cognitive Services のドキュメント ページ](../index.yml)
- [Cognitive Services の製品ページ](https://azure.microsoft.com/services/cognitive-services/)