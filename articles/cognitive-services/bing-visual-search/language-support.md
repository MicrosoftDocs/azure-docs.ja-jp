---
title: 言語サポート - Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Bing Visual Search API でサポートされる自然言語、国、および地域の一覧。 Bing Visual Search API は、30 を超え、その多くで複数の言語が使用されている国/地域をサポートしています。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 6d93b6d024fc966f3b260602f4798a2d77669a35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96501160"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Bing Visual Search API の言語と地域のサポート

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Visual Search API では 30 を超える国/地域がサポートされ、その多くで複数の言語が使用されています。 各要求には、ユーザーに最適な国/地域と言語を含める必要があります。 ユーザーの市場を把握することにより、Bing が適切な結果を返しやすくなります。 国/地域と言語を指定しない場合、Bing は可能な限り、ユーザーの国/地域と言語を特定しようとします。 これらの結果には Bing へのリンクを含めることができるため、ユーザーが Bing のリンクをクリックすれば国/地域と言語がわかり、推奨されるローカライズされた Bing ユーザー エクスペリエンスが提供される可能性があります。

国/地域と言語を指定するには、`mkt` (市場) クエリ パラメーターを下の「**市場**」の表のコードに設定します。 この市場は、国/地域と言語の両方を指定します。 表示テキストを別の言語で表示することを希望するユーザー向けには、`setLang` クエリ パラメーターを適切な言語コードに設定します。

あるいは、`cc` クエリ パラメーターを使用して国/地域を指定することもできます。 国/地域を指定する場合は、`Accept-Language` HTTP ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「市場」の表に国ごとに示されています。



> [!NOTE]
> 市場には次の制限事項が適用されます。
>
> - 画像認識に注釈を付ける場合、使用できるのは英語のみです。
> - レシピ、ショッピング、画像を含むページなどの分析情報が使用できるのは en-US 市場のみです。


## <a name="countriesregions"></a>国/リージョン

|国/リージョン|コード|
|-------|----|
|アルゼンチン|AR|
|オーストラリア|AU|
|オーストリア|AT|
|ベルギー|BE|
|ブラジル|BR|
|Canada|CA|
|チリ|CL|
|デンマーク|DK|
|フィンランド|FI|
|フランス|FR|
|ドイツ|DE|
|香港特別行政区|HK|
|インド|IN|
|インドネシア|id|
|イタリア|IT|
|日本|JP|
|韓国|KR|
|マレーシア|MY|
|メキシコ|MX|
|オランダ|NL|
|ニュージーランド|NZ|
|ノルウェー|NO|
|中国|CN|
|ポーランド|PL|
|ポルトガル|PT|
|フィリピン|PH|
|ロシア|RU|
|サウジアラビア|SA|
|南アフリカ|ZA|
|スペイン|ES|
|スウェーデン|SE|
|スイス|CH|
|台湾|TW|
|トルコ|TR|
|イギリス|GB|
|United States|US|


## <a name="markets"></a>市場

|国/リージョン|Language|市場コード|
|-------|--------|-----------|
|アルゼンチン|スペイン語|es-AR|
|オーストラリア|英語|en-AU|
|オーストリア|ドイツ語|de-AT|
|ベルギー|オランダ語|nl-BE|
|ベルギー|フランス語|fr-BE|
|ブラジル|Portuguese|pt-BR|
|Canada|英語|en-CA|
|Canada|フランス語|fr-CA|
|チリ|スペイン語|es-CL|
|デンマーク|デンマーク語|da-DK|
|フィンランド|フィンランド語|fi-FI|
|フランス|フランス語|fr-FR|
|ドイツ|ドイツ語|de-DE|
|香港特別行政区|Traditional Chinese|zh-HK|
|インド|英語|en-IN|
|インドネシア|英語|en-ID|
|イタリア|イタリア語|it-IT|
|日本|日本語|ja-JP|
|韓国|韓国語|ko-KR|
|マレーシア|英語|en-MY|
|メキシコ|スペイン語|es-MX|
|オランダ|オランダ語|nl-NL|
|ニュージーランド|英語|en-NZ|
|中国|Chinese|zh-CN|
|ポーランド|ポーランド語|pl-PL|
|ポルトガル|Portuguese|pt-PT|
|フィリピン|英語|en-PH|
|ロシア|ロシア語|ru-RU|
|サウジアラビア|アラビア語|ar-SA|
|南アフリカ|英語|en-ZA|
|スペイン|スペイン語|es-ES|
|スウェーデン|スウェーデン語|sv-SE|
|スイス|フランス語|fr-CH|
|スイス|ドイツ語|de-CH|
|台湾|Traditional Chinese|zh-TW|
|トルコ|トルコ語|tr-TR|
|イギリス|英語|en-GB|
|United States|英語|ja-JP|
|United States|スペイン語|es-US|