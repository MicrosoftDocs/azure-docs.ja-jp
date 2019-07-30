---
title: すべての作成済みエンティティ
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) に含まれる作成済みエンティティの一覧を示します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 082ebaef9a2497899cf91c24435dea6e626f0041
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485145"
---
# <a name="entities-per-culture-in-your-luis-model"></a>LUIS モデルにおけるカルチャごとのエンティティ

Language Understanding (LUIS) では、作成済みのエンティティが提供されています。 作成済みエンティティをアプリケーションに組み込むと、LUIS は対応するエンティティの予測をエンドポイントの応答に含めます。 すべての発話例にも、エンティティでラベルが付けられます。 作成済みエンティティの動作を変更することは**できません**。 特に記載のない限り、作成済みエンティティはすべての LUIS アプリケーション ロケール (カルチャ) で使用できます。 次の表では、各カルチャでサポートされている作成済みエンティティを示します。

|カルチャ|サブカルチャ|メモ|
|--|--|--|
|中国語|[zh-CN](#chinese-entity-support)||
|オランダ語|[nl-NL](#dutch-entity-support)||
|英語|[en-US (米国)](#english-american-entity-support)||
|フランス語|[fr-CA (カナダ)](#french-canadian-entity-support)、[fr-FR (フランス)](#french-france-entity-support), ||
|ドイツ語|[de-DE](#german-entity-support)||
|イタリア語|[it-IT](#italian-entity-support)||
|日本語|[ja-JP](#japanese-entity-support)||
|韓国語|[ko-KR](#korean-entity-support)||
|ポルトガル語|[pt-BR (ブラジル)](#portuguese-brazil-entity-support)||
|スペイン語|[es-ES (スペイン)](#spanish-spain-entity-support)、[es-MX (メキシコ)](#spanish-mexico-entity-support)||
|トルコ語|[turkish](#turkish-entity-support)|トルコ語では、あらかじめ構築されたエンティティはサポートされていません|

## <a name="chinese-entity-support"></a>中国語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>オランダ語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>英語 (米国) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    ✔   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>フランス語 (フランス) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>フランス語 (カナダ) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>ドイツ語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>イタリア語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>日本語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>韓国語のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    -   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>ポルトガル語 (ブラジル) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>スペイン語 (スペイン) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    ✔   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>スペイン語 (メキシコ) のエンティティのサポート

次のエンティティがサポートされています。

|事前構築済みのエンティティ|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[通貨 (金額)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (例: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>ボリューム<br>area<br>weight<br>information (例: bit/byte)<br>length (例: meter)<br>speed (例: mile per hour)  |    -   | 
[電子メール](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Number](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

「[非推奨のエンティティ](luis-reference-prebuilt-deprecated.md)」の注をご覧ください

KeyPhrase は、ポルトガル語 (ブラジル) - ```pt-BR``` のすべてのサブカルチャーで使用できるわけではありません。

## <a name="turkish-entity-support"></a>トルコ語のエンティティのサポート

**トルコ語では、あらかじめ構築されたエンティティのサポートはありません。** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>作成済みエンティティ カルチャへの寄与
作成済みエンティティは、Recognizers-Text オープンソース プロジェクトで開発されています。 プロジェクトに[寄与](https://github.com/Microsoft/Recognizers-Text)してください。 このプロジェクトには、カルチャごとの通貨の例が含まれています。 

GeographyV2 と PersonName は Recognizers-Text プロジェクトに含まれていません。 これらの事前構築済みエンティティの問題については、[サポート リクエスト](../../azure-supportability/how-to-create-azure-support-request.md)を開いてください。 

## <a name="next-steps"></a>次の手順

[number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[currency](luis-reference-prebuilt-currency.md) エンティティについて学習します。 
