---
title: すべての作成済みエンティティ - LUIS
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) に含まれる作成済みエンティティの一覧を示します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: cb3c74a2176ee7fcac53afb5185e8c62e66f4dfb
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798801"
---
# <a name="entities-per-culture-in-your-luis-model"></a>LUIS モデルにおけるカルチャごとのエンティティ

Language Understanding (LUIS) では、作成済みのエンティティが提供されています。

## <a name="entity-resolution"></a>エンティティの解決
作成済みのエンティティをアプリケーションに組み込むと、LUIS は対応するエンティティの解決をエンドポイントの応答に含めます。 すべての発話例にも、エンティティでラベルが付けられます。

作成済みのエンティティの動作は変更できませんが、[機械学習エンティティまたはサブエンティティに作成済みのエンティティを特徴量として追加](luis-concept-entity-types.md#effective-prebuilt-entities)することによって、解決を向上させることができます。

## <a name="availability"></a>可用性
特に記載のない限り、作成済みエンティティはすべての LUIS アプリケーション ロケール (カルチャ) で使用できます。 次の表では、各カルチャでサポートされている作成済みエンティティを示します。

|カルチャ|サブカルチャ|Notes|
|--|--|--|
|Chinese|[zh-CN](#chinese-entity-support)||
|オランダ語|[nl-NL](#dutch-entity-support)||
|英語|[en-US (米国)](#english-american-entity-support)||
|フランス語|[fr-CA (カナダ)](#french-canadian-entity-support)、[fr-FR (フランス)](#french-france-entity-support), ||
|ドイツ語|[de-DE](#german-entity-support)||
|イタリア語|[it-IT](#italian-entity-support)||
|日本語|[en-US](#japanese-entity-support)||
|韓国語|[ko-KR](#korean-entity-support)||
|Portuguese|[pt-BR (ブラジル)](#portuguese-brazil-entity-support)||
|スペイン語|[es-ES (スペイン)](#spanish-spain-entity-support)、[es-MX (メキシコ)](#spanish-mexico-entity-support)||
|トルコ語|[turkish](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>予測エンドポイントのランタイム

特定の言語であらかじめ構築されたエンティティの可用性は、予測エンドポイントのランタイム バージョンによって決まります。

## <a name="chinese-entity-support"></a>中国語のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | zh-CN |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2、V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="dutch-entity-support"></a>オランダ語のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | nl-NL |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="english-american-entity-support"></a>英語 (米国) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | ja-JP |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2、V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2、V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2、V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="french-france-entity-support"></a>フランス語 (フランス) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | fr-FR |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="french-canadian-entity-support"></a>フランス語 (カナダ) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | fr-CA |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="german-entity-support"></a>ドイツ語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ | de-DE |
| -------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="italian-entity-support"></a>イタリア語のエンティティのサポート

イタリア語の事前構築済みの年齢、通貨、ディメンション、数値、割合 "_解像度_" が V2 および V3 プレビューから変更されました。

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | it-IT |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="japanese-entity-support"></a>日本語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ | ja-JP |
| -------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、-   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、-   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、-   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、-   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、-   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="korean-entity-support"></a>韓国語のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | ko-KR |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="portuguese-brazil-entity-support"></a>ポルトガル語 (ブラジル) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | pt-BR |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

KeyPhrase は、ポルトガル語 (ブラジル) - ```pt-BR``` のすべてのサブカルチャーで使用できるわけではありません。

## <a name="spanish-spain-entity-support"></a>スペイン語 (スペイン) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | es-ES |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    V2、V3   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    V2、V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2、V3   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    V2、V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2、V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2、V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2、V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

## <a name="spanish-mexico-entity-support"></a>スペイン語 (メキシコ) のエンティティのサポート

次のエンティティがサポートされています。

| 事前構築済みのエンティティ | es-MX |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2、V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2、V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2、V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2、V3   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2、V3   |

「[非推奨のエンティティ](luis-reference-prebuilt-deprecated.md)」の注をご覧ください

## <a name="turkish-entity-support"></a>トルコ語のエンティティのサポート

| 事前構築済みのエンティティ | tr-tr |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[温度](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>作成済みエンティティ カルチャへの寄与
作成済みエンティティは、Recognizers-Text オープンソース プロジェクトで開発されています。 プロジェクトに[寄与](https://github.com/Microsoft/Recognizers-Text)してください。 このプロジェクトには、カルチャごとの通貨の例が含まれています。

GeographyV2 と PersonName は Recognizers-Text プロジェクトに含まれていません。 これらの事前構築済みエンティティの問題については、[サポート リクエスト](../../azure-portal/supportability/how-to-create-azure-support-request.md)を開いてください。

## <a name="next-steps"></a>次のステップ

[number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[currency](luis-reference-prebuilt-currency.md) エンティティについて学習します。
