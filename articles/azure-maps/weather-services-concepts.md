---
title: Microsoft Azure Maps における Weather Service (プレビュー) の概念
description: Microsoft Azure Maps Weather Service (プレビュー) に適用される概念について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 657cb6a86122d267b86e82b6f02eb58e968cddd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904739"
---
# <a name="weather-services-preview-in-azure-maps"></a>Azure Maps の Weather Service (プレビュー)

> [!IMPORTANT]
> Azure Maps Weather Service は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Maps [Weather Service](/rest/api/maps/weather) に適用される概念を紹介します。 Weather API の使用を開始する前に、この記事を読み通すことをお勧めします。

## <a name="unit-types"></a>単位の種類

Weather Service (プレビュー) API の中には、データをメトリックとヤード単位のどちらで返すかをユーザーが指定できるものがあります。 これらの API で返される応答には、単位の変換に使用できる unitType (数値) が含まれます。 これらの値を解釈するには、次の表を参照してください。

|unitType|説明         |
|--------|--------------------|
|0       |フィート                |
|1       |インチ              |
|2       |マイル               |
|3       |ミリメートル          |
|4       |センチメートル          |
|5       |メートル               |
|6       |キロメートル           |
|7       |キロメートル毎時   |
|8       |ノット               |
|9       |マイル毎時        |
|10      |メートル毎秒     |
|11      |ヘクトパスカル        |
|12      |水銀柱インチ     |
|13      |キロパスカル         |
|14      |ミリバール           |
|15      |水銀柱ミリメートル|
|16      |ポンド/平方インチ |
|17      |celsius             |
|18      |fahrenheit          |
|19      |kelvin              |
|20      |パーセント             |
|21      |float               |
|22      |整数 (integer)             |


## <a name="weather-icons"></a>天気アイコン

Weather Service (プレビュー) API の中には、応答で `iconCode` が返されるものがあります。 `iconCode` は、アイコンの定義に使用される数値です。 アプリケーションからこれらの画像に直接リンクしないでください。URL が変更される可能性があります。

| アイコン番号 |アイコン| 日 | 夜間 | Text |
|-------------|:----:|-----|-------|------|
| 1           | :::image type="icon" source="./media/weather-services-concepts/sunny-i.png"::: | はい |  いいえ    | 晴れ|
| 2           | :::image type="icon" source="./media/weather-services-concepts/mostly-sunny.png"::: | はい |  いいえ    | 晴れ一時曇り|
| 3           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny.png"::: | はい |  いいえ    | 晴れ時々曇り|
| 4           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds.png"::: | はい |  いいえ    | 晴れたり曇ったり|
| 5           | :::image type="icon" source="./media/weather-services-concepts/hazy-sunshine.png"::: | はい |  いいえ    | 薄晴れ |
| 6           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy.png"::: | はい |  いいえ    | 曇り一時晴れ|
| 7           | :::image type="icon" source="./media/weather-services-concepts/cloudy-i.png"::: | はい |  はい   | 曇り |
| 8           | :::image type="icon" source="./media/weather-services-concepts/dreary-overcast.png"::: | はい |  はい   | 本雲り|
| 11           | :::image type="icon" source="./media/weather-services-concepts/fog-i.png"::: | はい |  はい   | 霧|
| 12           | :::image type="icon" source="./media/weather-services-concepts/showers-i.png"::: | はい |  はい   | 小雨|
| 13           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers.png"::: | はい |  いいえ    | 曇り一時小雨|
| 14           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-showers.png"::: | はい |  いいえ    | 晴れ時々小雨|
| 15           | :::image type="icon" source="./media/weather-services-concepts/tstorms-i.png"::: | はい |  はい   | 雷雨|
| 16           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms.png"::: | はい |  いいえ    | 曇り一時雷雨|
| 17           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-tstorms.png"::: | はい |  いいえ    | 晴れ時々雷雨|
| 18           | :::image type="icon" source="./media/weather-services-concepts/rain-i.png"::: | はい |  はい   | 雨|
| 19           | :::image type="icon" source="./media/weather-services-concepts/flurries-i.png"::: | はい |  はい   | にわか雨|
| 20           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries.png"::: | はい |  いいえ    | 曇り一時にわか雨|
| 21           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-flurries.png"::: | はい |  いいえ    | 晴れ時々にわか雨|
| 22           | :::image type="icon" source="./media/weather-services-concepts/snow-i.png"::: | はい |  はい   | 雪|
| 23           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | はい |  いいえ    | 曇り一時雪|     
| 24           | :::image type="icon" source="./media/weather-services-concepts/ice-i.png"::: | はい |  はい   | 凍雨 |
| 25           | :::image type="icon" source="./media/weather-services-concepts/sleet-i.png"::: | はい |  はい   | みぞれ|
| 26           | :::image type="icon" source="./media/weather-services-concepts/freezing-rain.png"::: | はい |  はい   | 雨氷|
| 29           | :::image type="icon" source="./media/weather-services-concepts/rain-snow.png"::: | はい |  はい   | 雨と雪|
| 30           | :::image type="icon" source="./media/weather-services-concepts/hot-i.png"::: | はい |  はい   | ホット|
| 31           | :::image type="icon" source="./media/weather-services-concepts/cold-i.png"::: | はい |  はい   | アイス|
| 32           | :::image type="icon" source="./media/weather-services-concepts/windy-i.png"::: | はい |  はい   | 強風|
| 33           | :::image type="icon" source="./media/weather-services-concepts/clear-night.png"::: | いいえ  |  はい   | Clear|
| 34           | :::image type="icon" source="./media/weather-services-concepts/mostly-clear-night.png"::: | いいえ  |  はい   | 晴夜一時曇り|
| 35           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-night.png"::: | いいえ  |  はい   | 晴夜時々曇り|
| 36           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds-Night.png"::: | いいえ  |  はい   | 晴れたり曇ったり|
| 37           | :::image type="icon" source="./media/weather-services-concepts/hazymoon-light.png"::: | いいえ  |  はい   | 薄月夜|
| 38           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-night.png"::: | いいえ  |  はい   | 曇り一時晴れ|
| 39           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-showers-night.png"::: | いいえ  |  はい   | 曇り時々小雨|
| 40           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers-night.png"::: | いいえ  |  はい   | 曇り一時小雨|
| 41           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-tstorms-night.png"::: | いいえ  |  はい   | 曇りときどき雷雨|
| 42           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms-night.png"::: | いいえ  |  はい   | 曇り一時雷雨|
| 43           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries-night.png"::: | いいえ  |  はい   | 曇り一時にわか雨|
| 44           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | いいえ  |  はい   | 曇り一時雪|


## <a name="radar-and-satellite-imagery-color-scale"></a>レーダーと衛星画像のカラー スケール

ユーザーは、[Get Map Tile v2 API](/rest/api/maps/renderv2/getmaptilepreview) を使用して、最新のレーダー画像と赤外衛星画像を要求でき ます。 レーダー タイルと衛星タイルで使用される色を解釈するには、以下のガイドを参照してください。

### <a name="radar-images"></a>レーダー画像

次の表は、レーダー画像を解釈し、レーダー タイル データのマップの凡例を作成するためのガイダンスを示しています。

| 16 進カラー コード | 色のサンプル | 天候 |
|----------------|--------------|-------------------|
| #93c701        | ![雨 - 弱いの色。](./media/weather-services-concepts/color-93c701.png) | 雨 - 弱い |
| #ffd701        | ![雨 - 中程度の色。](./media/weather-services-concepts/color-ffd701.png) | 雨 - 中程度 |
| #f05514        | ![雨 - 強いの色。](./media/weather-services-concepts/color-f05514.png) | 雨 - 強い |
| #dc250e        | ![雨 - 激しいの色。](./media/weather-services-concepts/color-dc250e.png) | 雨 - 激しい |
| #9ec8f2        | ![雪 - 弱いの色。](./media/weather-services-concepts/color-9ec8f2.png) | 雪 - 弱い |
| #2a8fdb        | ![雪 - 中程度の色。](./media/weather-services-concepts/color-2a8fdb.png) | 雪 - 中程度 |
| #144bed        | ![雪 - 強いの色。](./media/weather-services-concepts/color-144bed.png) | 雪 - 強い |
| #020096        | ![雪 - 激しいの色。](./media/weather-services-concepts/color-020096.png) | 雪 - 激しい |
| #e6a5c8        | ![凍雨 - 弱いの色。](./media/weather-services-concepts/color-e6a5c8.png) | 凍雨 - 弱い |
| #d24fa0        | ![凍雨 - 中程度の色。](./media/weather-services-concepts/color-d24fa0.png) | 凍雨 - 中程度 |
| #b71691        | ![凍雨 - 強いの色。](./media/weather-services-concepts/color-b71691.png) | 凍雨 - 強い |
| #7a1570        | ![凍雨 - 激しいの色。](./media/weather-services-concepts/color-7a1570.png) | 凍雨 - 激しい |
| #c196e6        | ![混合 - 弱いの色。](./media/weather-services-concepts/color-c196e6.png) | 混合 - 弱い |
| #ae6ee6        | ![混合 - 中程度の色。](./media/weather-services-concepts/color-ae6ee6.png) | 混合 - 中程度 |
| #8a32d7        | ![混合 - 強いの色。](./media/weather-services-concepts/color-8a32d7.png) | 混合 - 強い |
| #6500ba        | ![混合 - 激しいの色。](./media/weather-services-concepts/color-6500ba.png) | 混合 - 激しい |

レーダー タイルで使用される色パレットの詳細を 16 進カラー コードと dBZ 値で以下に示します。 dBZ は、気象レーダーの降水強度を表します。 

| **雨**             | **凍雨**              | **雪**              | **混合**             |
|----------------------|----------------------|-----------------------|-----------------------|
| **dBZ** **(色)**  | **dBZ** **(色)**  | **dBZ** **(色)**   | **dBZ** **(色)**   |
| 1.25       (#93C701) | 1.25       (#E6A5C8) | 1.25       (#9EC8F2)  | 1.25        (#C196E6) |
| 2.5        (#92C201) | 2.5        (#E6A2C6) | 2.5        (#98C5F0)  | 2.5         (#BF92E6) |
| 3.75       (#92BE01) | 3.75       (#E69FC5) | 3.75       (#93C3EF)  | 3.75        (#BD8EE6) |
| 5          (#92BA02) | 5          (#E69DC4) | 5          (#8DC1EE)  | 5           (#BB8BE6) |
| 6.25       (#92B502) | 6.25       (#E69AC2) | 6.25       (#88BFEC)  | 6.25        (#BA87E6) |
| 6.75       (#92B403) | 7.5       (#E697C1)  | 7.5        (#82BDEB)   | 7.5         (#B883E6) |
| 8          (#80AD02) | 8.75       (#E695C0) | 8.75       (#7DBAEA)  | 8.75        (#B680E6) |
| 9.25       (#6FA602) | 10         (#E692BE) | 10         (#77B8E8)  | 10          (#B47CE6) |
| 10.5       (#5EA002) | 11.25      (#E68FBD) | 11.25      (#72B6E7)  | 11.25       (#B378E6) |
| 11.75      (#4D9902) | 12.5       (#E68DBC) | 12.5       (#6CB4E6)  | 12.5        (#B175E6) |
| 12.25      (#479702) | 13.75      (#E68ABA) | 13.75      (#67B2E5)  | 13.75       (#AF71E6) |
| 13.5       (#3D9202) | 15         (#E687B9) | 15         (#61AEE4)  | 15          (#AE6EE6) |
| 14.75      (#338D02) | 16.25      (#E685B8) | 16.25      (#5BABE3)  | 16.25       (#AB6AE4) |
| 16         (#298802)  | 17.5       (#E682B6) | 17.5       (#56A8E2)  | 17.5        (#A967E3) |
| 17.25      (#1F8302) | 18.75      (#E67FB5) | 18.75      (#50A5E1)  | 18.75       (#A764E2) |
| 17.75      (#1B8103) | 20         (#E67DB4) | 20         (#4BA2E0)  | 20          (#A560E1) |
| 19         (#187102) | 21.25      (#E275B0) | 21.25       (#459EDF) | 21.25       (#A35DE0) |
| 20.25      (#166102) | 22.5       (#DF6DAD) | 22.5       (#409BDE)  | 22.5        (#A15ADF) |
| 20.75      (#165B02) | 23.75      (#DC66AA) | 23.75      (#3A98DD)  | 23.75       (#9F56DE) |
| 22         (#135001) | 25         (#D85EA6) | 25         (#3595DC)  | 25          (#9D53DD) |
| 23.25      (#114501) | 26.25      (#D556A3) | 26.25      (#2F92DB)  | 26.25       (#9B50DC) |
| 24.5       (#0F3A01) | 27.5       (#D24FA0) | 27.5       (#2A8FDB)  | 27.5        (#9648DA) |
| 25.75      (#124C01) | 28.75      (#CE479E) | 28.75      (#2581DE)  | 28.75       (#9241D9) |
| 27         (#114401) | 30         (#CB409C) | 30         (#2173E2)  | 30          (#8E39D8) |
| 28.25      (#0F3D01) | 31.25      (#C7399A) | 31.25      (#1C66E5)  | 31.25       (#8A32D7) |
| 28.75      (#0F3A01) | 32.5       (#C43298) | 32.5       (#1858E9)  | 32.5        (#862ED2) |
| 30         (#375401) | 33.75      (#C12B96) | 33.75      (#144BED)  | 33.75       (#832BCE) |
| 31.25      (#5F6E01) | 35         (#BD2494) | 35         (#1348EA)  | 35          (#7F28C9) |
| 32.5       (#878801) | 36.25      (#BA1D92) | 36.25      (#1246E7)  | 36.25       (#7C25C5) |
| 33.75      (#AFA201) | 37.5       (#B71691) | 37.5       (#1144E4)  | 37.5        (#7822C1) |
| 35         (#D7BC01) | 38.75      (#B51690) | 38.75      (#1142E1)  | 38.75       (#751FBC) |
| 36.25      (#FFD701) | 40         (#B3168F) | 40         (#1040DE)  | 40          (#711CB8) |
| 37.5       (#FEB805) | 41.25      (#B1168E) | 41.25      (#0F3EDB)  | 41.25       (#6E19B4) |
| 38.75      (#FCAB06) | 42.5       (#AF168D) | 42.5       (#0F3CD8)  | 42.5        (#6D18B4) |
| 40         (#FA9E07) | 43.75      (#AD168C) | 43.75      (#0E3AD5)  | 43.75       (#6D17B4) |
| 41.25      (#F89209) | 45         (#AB168B) | 45         (#0D38D2)  | 45          (#6D16B4) |
| 42.5       (#F05514) | 46.25      (#A9168A) | 46.25      (#0C36CF)  | 46.25       (#6C15B4) |
| 43.75      (#E74111) | 47.5       (#A81689) | 47.5       (#0C34CC)  | 47.5        (#6C14B5) |
| 45         (#DF2D0F) | 48.75      (#A61688) | 48.75      (#0B32C9)  | 48.75       (#6C13B5) |
| 45.5       (#DC250E) | 50         (#A41687) | 50         (#0A30C6)  | 50          (#6B12B5) |
| 46.75      (#D21C0C) | 51.25      (#A21686) | 51.25      (#0A2EC4)  | 51.25       (#6B11B5) |
| 48         (#C9140A) | 52.5       (#A01685) | 52.5       (#092BC1)  | 52.5        (#6B10B6) |
| 49.25      (#BF0C09) | 53.75      (#9E1684) | 53.75      (#0929BF)  | 53.75       (#6A0FB6) |
| 50         (#BA0808) | 55         (#9C1683) | 55         (#0826BC)  | 55          (#6A0EB6) |
| 56.25      (#6f031b) | 56.25      (#9B1682) | 56.25      (#0824BA)  | 56.25       (#6A0DB6) |
| 57.5       (#9f0143) | 57.5       (#981580) | 57.5       (#0721B7)  | 57.5        (#690CB6) |
| 58.75      (#c10060) | 58.75      (#96157F) | 58.75      (#071FB5)  | 58.75       (#690CB7) |
| 60         (#e70086) | 60         (#94157E) | 60         (#071DB3)  | 60          (#690BB7) |
| 61.25      (#e205a0) | 61.25      (#92157D) | 61.25      (#061AB0)  | 61.25       (#680AB7) |
| 62.5       (#cc09ac) | 62.5       (#90157C) | 62.5       (#0618AE)  | 62.5        (#6809B7) |
| 63.75      (#b50eb7) | 63.75      (#8D157A) | 63.75      (#0515AB)  | 63.75       (#6808B8) |
| 65         (#9315c8) | 65         (#8B1579) | 65         (#0513A9)  | 65          (#6707B8) |
| 66.25      (#8f21cc) | 66.25      (#891578) | 66.25      (#0410A6)  | 66.25       (#6706B8) |
| 67.5       (#983acb) | 67.5       (#871577) | 67.5       (#040EA4)  | 67.5        (#6705B8) |
| 68.75      (#9d49cb) | 68.75      (#851576) | 68.75      (#040CA2)  | 68.75       (#6604B8) |
| 70         (#a661ca) | 70         (#821574) | 70         (#03099F)  | 70          (#6603B9) |
| 71.25      (#ad72c9) | 71.25      (#801573) | 71.25      (#03079D)  | 71.25       (#6602B9) |
| 72.5       (#b78bc6) | 72.5       (#7E1572) | 72.5       (#02049A)  | 72.5        (#6501B9) |
| 73.75      (#bf9bc4) | 73.75      (#7C1571) | 73.75      (#020298)  | 73.75       (#6500B9) |
| 75         (#c9b5c2) | 75         (#7A1570) | 75         (#020096)  | 75          (#6500BA) |



### <a name="satellite-images"></a>衛星画像

次の表は、雲を温度で示している赤外衛星画像を解釈し、これらのタイルのマップの凡例を作成するためのガイダンスを示しています。 

| 16 進カラー コード | 色のサンプル | 雲の温度 |
|----------------|--------------|-------------------|
| #b5b5b5        | ![#b5b5b5 の色タイル。](./media/weather-services-concepts/color-b5b5b5.png) | 温度 - 低 | 
| #d24fa0        | ![#d24fa0 の色タイル。](./media/weather-services-concepts/color-d24fa0.png) |  |
| #8a32d7        | ![#8a32d7 の色タイル。](./media/weather-services-concepts/color-8a32d7.png) |  |
| #144bed        | ![#144bed の色タイル。](./media/weather-services-concepts/color-144bed.png) |  |
| #479702        | ![#479702 の色タイル。](./media/weather-services-concepts/color-479702.png) |  |
| #72b403        | ![#72b403 の色タイル。](./media/weather-services-concepts/color-72b403.png) |  |
| #93c701        | ![#93c701 の色タイル。](./media/weather-services-concepts/color-93c701.png) |  |
| #ffd701        | ![#ffd701 の色タイル。](./media/weather-services-concepts/color-ffd701.png) |  |
| #f05514        | ![#f05514 の色タイル。](./media/weather-services-concepts/color-f05514.png) |  |
| #dc250e        | ![#dc250e の色タイル。](./media/weather-services-concepts/color-dc250e.png) |  |
| #ba0808        | ![#ba0808 の色タイル。](./media/weather-services-concepts/color-ba0808.png) |  |
| #1f1f1f        | ![#1f1f1f の色タイル。](./media/weather-services-concepts/color-1f1f1f.png) | 温度 - 高 |


赤外衛星タイルの色パレットの詳細を以下に示します。

|**温度 (K)**|**16 進カラー コード**|
|--------|--------------|
|198     |#fe050505     |
|198.43  |#fe120505     |
|198.87  |#fc1f0505     |
|199.3   |#fc2c0606     |
|199.74  |#fa390606     |
|200.17  |#fa460606     |
|200.61  |#f8530606     |
|201.04  |#f8600707     |
|201.48  |#f66c0707     |
|201.91  |#f6790707     |
|202.35  |#f4860707     |
|202.78  |#f4930707     |
|203.22  |#f2a00808     |
|203.65  |#f2ad0808     |
|204.09  |#f0ba0808     |
|204.52  |#f0bd0a09     |
|204.96  |#eec00d09     |
|205.39  |#eec30f0a     |
|205.83  |#ecc5120a     |
|206.26  |#ecc8140b     |
|206.7   |#eacb170b     |
|207.13  |#eace190c     |
|207.57  |#e8d11b0c     |
|208     |#e8d41e0d     |
|208.43  |#e6d6200d     |
|208.87  |#e6d9230e     |
|209.3   |#e4dc250e     |
|209.74  |#e4df2c0f     |
|210.17  |#e2e23310     |
|210.61  |#e2e53a11     |
|211.04  |#e0e73a11     |
|211.48  |#e0ea4712     |
|211.91  |#deed4e13     |
|212.35  |#def05514     |
|212.78  |#dcf15e13     |
|213.22  |#dcf26811     |
|213.65  |#daf37110     |
|214.09  |#daf47a0f     |
|214.52  |#d8f5830d     |
|214.96  |#d8f68d0c     |
|215.39  |#d6f8960b     |
|215.83  |#d6f99f09     |
|216.26  |#d4faa908     |
|216.7   |#d4fbb206     |
|217.13  |#d2fcbb05     |
|217.57  |#d2fdc404     |
|218     |#d0fece02     |
|218.43  |#d0ffd701     |
|218.87  |#cef8d601     |
|219.3   |#cef2d501     |
|219.74  |#ccebd401     |
|220.17  |#cce4d301     |
|220.61  |#caddd201     |
|221.04  |#cad7d101     |
|221.48  |#c8d0d001     |
|221.91  |#c8c9cf01     |
|222.35  |#c6c2ce01     |
|222.78  |#c6bccd01     |
|223.22  |#c4b5cc01     |
|223.65  |#c4aecb01     |
|224.09  |#c2a7ca01     |
|224.52  |#c2a1c901     |
|224.96  |#c09ac801     |
|225.39  |#c093c701     |
|225.83  |#be90c501     |
|226.26  |#be8ec401     |
|226.7   |#bc8bc202     |
|227.13  |#bc88c102     |
|227.57  |#ba85bf02     |
|228     |#ba83be02     |
|228.43  |#b880bc02     |
|228.87  |#b87dba02     |
|229.3   |#b678b703     |
|229.74  |#b675b603     |
|230.17  |#b472b403     |
|230.61  |#b46eb203     |
|231.04  |#b26bb203     |
|231.48  |#b267ad03     |
|231.97  |#b064aa03     |
|232.35  |#b060a803     |
|232.78  |#ae5da603     |
|233.22  |#ae59a302     |
|233.65  |#ac55a102     |
|234.09  |#ac529e02     |
|234.52  |#aa4e9c02     |
|234.96  |#aa4b9902     |
|235.39  |#a8479702     |
|235.83  |#a845940b     |
|236.26  |#a6439115     |
|236.7   |#a6418e1e     |
|237.13  |#a43f8b28     |
|237.57  |#a43d8831     |
|238     |#a23b853a     |
|238.43  |#a2398244     |
|238.87  |#a0377f4d     |
|239.3   |#a0357c57     |
|239.74  |#9e337960     |
|240.17  |#9e317669     |
|240.61  |#9c2f7373     |
|241.04  |#9c2c6f7c     |
|241.48  |#9a2a6c86     |
|241.91  |#9a28698f     |
|242.35  |#98266698     |
|242.78  |#982463a2     |
|243.22  |#962260ab     |
|243.65  |#96205db5     |
|244.09  |#941e5abe     |
|244.52  |#941c57c7     |
|244.85  |#921a54d1     |
|245.39  |#921851da     |
|245.83  |#90164ee4     |
|246.26  |#90144bed     |
|246.7   |#8e2148eb     |
|247.13  |#8e2e45e8     |
|247.57  |#8c3b43e6     |
|248     |#8c4840e3     |
|248.43  |#8a563de1     |
|248.87  |#8a633ade     |
|249.3   |#887038dc     |
|249.74  |#887d35d9     |
|250.17  |#868a32d7     |
|250.61  |#869034d2     |
|251.04  |#849637ce     |
|251.48  |#849c39c9     |
|251.91  |#82a23cc5     |
|252.35  |#82a83ec0     |
|252.78  |#80ae41bc     |
|253.22  |#80b443b7     |
|253.65  |#7eba45b2     |
|253.09  |#7ec048ae     |
|254.52  |#7cc64aa9     |
|254.96  |#7ccc4da5     |
|255.39  |#7ad24fa0     |
|255.83  |#7ad85fac     |
|256.26  |#78dd6eb8     |
|256.7   |#78e37ec4     |
|257.13  |#76e98ed0     |
|257.57  |#76ee9ddb     |
|258     |#74f4ade7     |
|258.43  |#74f9bcf3     |
|258.87  |#72ffccff     |
|259.3   |#71ffffff     |
|259.74  |#71fcfcfc     |
|260.17  |#6ff6f6f6     |
|260.61  |#6ff6f6f6     |
|261.04  |#6df3f3f3     |
|261.48  |#6df3f3f3     |
|261.91  |#6bededed     |
|262.35  |#6bededed     |
|262.78  |#69e7e7e7     |
|263.22  |#69e7e7e7     |
|263.65  |#67e1e1e1     |
|264.09  |#67e1e1e1     |
|264.52  |#65dedede     |
|264.96  |#65dedede     |
|265.39  |#63d8d8d8     |
|265.83  |#63d8d8d8     |
|265.84  |#61d1d1d1     |
|266.26  |#61d1d1d1     |
|267.13  |#5fcecece     |
|267.57  |#5fcecece     |
|268     |#5dc8c8c8     |
|268.43  |#5dc8c8c8     |
|268.87  |#5bc2c2c2     |
|269.3   |#5bc2c2c2     |
|269.74  |#59bcbcbc     |
|270.17  |#59bcbcbc     |
|270.61  |#57b9b9b9     |
|271.04  |#57b9b9b9     |
|271.48  |#55b3b3b3     |
|271.91  |#55b3b3b3     |
|272.35  |#53adadad     |
|272.78  |#53adadad     |
|273.22  |#51aaaaaa     |
|273.65  |#51aaaaaa     |
|274.09  |#4fa4a4a4     |
|274.52  |#4fa4a4a4     |
|274.96  |#4d9e9e9e     |
|275.39  |#4d9e9e9e     |
|275.83  |#4b989898     |
|276.26  |#4b989898     |
|276.7   |#49959595     |
|277.13  |#49959595     |
|277.57  |#478f8f8f     |
|278     |#478f8f8f     |
|278.43  |#45898989     |
|278.87  |#45898989     |
|279.2   |#43868686     |
|279.74  |#43868686     |
|280.17  |#417f7f7f     |
|280.61  |#417f7f7f     |
|281.04  |#3f797979     |
|281.48  |#3f797979     |
|281.91  |#3d737373     |
|282.35  |#3d737373     |
|282.78  |#3b707070     |
|283.22  |#3b707070     |
|283.65  |#396a6a6a     |
|284.09  |#396a6a6a     |
|284.52  |#37646464     |
|284.96  |#37646464     |
|285.39  |#35616161     |
|285.83  |#35616161     |
|286.26  |#335b5b5b     |
|286.7   |#335b5b5b     |
|287.13  |#31555555     |
|287.57  |#31555555     |
|288     |#2f4f4f4f     |
|288.43  |#2f4f4f4f     |
|288.87  |#2d4c4c4c     |
|289.3   |#2d4c4c4c     |
|289.74  |#2b464646     |
|290.17  |#2b464646     |
|290.61  |#29404040     |
|291.04  |#29404040     |
|291.48  |#273d3d3d     |
|291.91  |#273d3d3d     |
|292.35  |#25373737     |
|292.78  |#25373737     |
|293.22  |#23313131     |
|293.65  |#23313131     |
|294.09  |#212a2a2a     |
|294.52  |#212a2a2a     |
|294.96  |#1f272727     |
|295.39  |#1f272727     |
|295.83  |#1d212121     |
|296.26  |#1d212121     |
|296.7   |#1b1b1b1b     |
|297.13  |#1b1b1b1b     |
|297.57  |#19181818     |
|298     |#19181818     |
|298.43  |#17121212     |
|298.87  |#17121212     |
|299.3   |#150c0c0c     |
|299.74  |#150c0c0c     |
|300.17  |#13060606     |
|300.61  |#13060606     |
|301.04  |#11000000     |
|301.48  |#11000000     |
|301.91  |#0f797979     |
|302.35  |#0f797979     |
|302.78  |#0d737373     |
|303.22  |#0d737373     |
|303.65  |#0b6d6d6d     |
|304.09  |#0b6d6d6d     |
|304.52  |#09676767     |
|304.92  |#09676767     |
|305.39  |#07616161     |
|305.83  |#07616161     |
|306.26  |#055b5b5b     |
|306.7   |#055b5b5b     |
|307.13  |#02555555     |
|307.57  |#02555555     |
|308     |#00525252     |
|308     |#00525252     |

## <a name="index-ids-and-index-groups-ids"></a>インデックス ID とインデックス グループ ID

[Get Daily Indices API](/rest/api/maps/weather) を使用すると、ユーザーは返される結果を特定のインデックスの種類またはインデックス グループに限定できます。

使用可能なインデックス ID とその名前、およびそれらの範囲のセットへのリンクを次の表に示します。 この表の後に、さまざまなインデックス グループを一覧した表を示します。

  Index Name |  id  | 値の範囲
  -------------------------- |---|-----
  関節痛             |21 | [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  ぜんそく                     |23|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  ビーチとプール               |10| [悪い - 非常に良い 1](#poor-excellent-1)
  サイクリング                  |4| [悪い - 非常に良い 1](#poor-excellent-1)
  風邪                |25|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  コンポスト化                 |38| [悪い - 非常に良い 1](#poor-excellent-1)
  建設               |14| [悪い - 非常に良い 1](#poor-excellent-1)
  COPD                       |44|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  犬の散歩の快適さ        |43| [悪い - 非常に良い 1](#poor-excellent-1)
  Driving (車)                    |40|  [悪い - 非常に良い 2](#poor-excellent-2)
  ほこりとフケ              |18| [低 - 極めて高い 1](#low-extreme-1)
  田畑の状態            |32| [悪い - 非常に良い 1](#poor-excellent-1)
  魚釣り                    |13| [悪い - 非常に良い 1](#poor-excellent-1)
  フライトの遅延              |-3|  [めったにない - とてもよくある 2](#very-unlikely-very-likely-2)
  インフルエンザ                        |26|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  飛行機での旅行インデックス        |31| [非常に良い - 悪い](#excellent-poor)
  燃費               |37| [悪い - 非常に良い 1](#poor-excellent-1)
  ゴルフの天気               |5| [悪い - 非常に良い 1](#poor-excellent-1)
  芝生              |33| [悪い - 非常に良い 1](#poor-excellent-1)
  縮毛                 |42| [あまりない - 緊急](#unlikely-emergency)
  心臓によいフィットネス      |16| [悪い - 非常に良い 1](#poor-excellent-1)
  ハイキング                     |3| [悪い - 非常に良い 1](#poor-excellent-1)
  自宅のエネルギー効率     |36| [悪い - 非常に良い 1](#poor-excellent-1)
  検出                    | 20| [悪い - 非常に良い 1](#poor-excellent-1)
  屋内活動            | -2| [悪い - 非常に良い 1](#poor-excellent-1)
  ジョギング                    |2| [悪い - 非常に良い 1](#poor-excellent-1)
  たこ揚げ                |9| [悪い - 非常に良い 1](#poor-excellent-1)
  芝刈り                |28| [悪い - 非常に良い 1](#poor-excellent-1)
  片頭痛          |27|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  朝のスクールバス         |35| [悪い - 非常に良い 1](#poor-excellent-1)
  蚊の活動          |17|[低 - 極めて高い 1](#low-extreme-1)
  屋外活動           |29| [悪い - 非常に良い 1](#poor-excellent-1)
  屋外バーベキュー           |24| [悪い - 非常に良い 1](#poor-excellent-1)
  屋外コンサート            |8| [悪い - 非常に良い 1](#poor-excellent-1)
  実行中                    |1|  [悪い - 非常に良い 1](#poor-excellent-1)
  テニス                     |6| [悪い - 非常に良い 1](#poor-excellent-1)
  のどの渇き                     |41| [低 - 極めて高い 2](#low-extreme-2)
  セーリング                    |11| [悪い - 非常に良い 1](#poor-excellent-1)
  ショッピング                   |39| [悪い - 非常に良い 1](#poor-excellent-1)
  副鼻腔炎性頭痛             |30|  [有益 - 重大なリスク](#beneficial-at-extreme-risk)
  スケートボード              | 7| [悪い - 非常に良い 1](#poor-excellent-1)
  スキーの天気                | 15| [悪い - 非常に良い 1](#poor-excellent-1)
  雪の日数                  | 19| [めったにない - とてもよくある](#very-unlikely-very-likely)
  土壌水分              | 34| [悪い - 非常に良い 1](#poor-excellent-1)
  天体観測                 | 12| [悪い - 非常に良い 1](#poor-excellent-1)

使用可能なインデックス グループ (indexGroupId) の一覧を次に示します。

  id   | グループ名 | このグループ内のインデックス |
  -------- | ------------------|------
  1       |All | All
  2       |うずきと痛み | 関節痛 (21) </br> 片頭痛 (27) </br> 副鼻腔炎性頭痛 (30)
  3       |呼吸器系 | ぜんそく (23) </br> 風邪 (25) </br> インフルエンザ予測 (26)
  4       |ガーデニング | 田畑の状態 (32) </br> 芝刈り (28) </br> 土壌水分 (34)</br>
  5       |環境 | コンポスト化 (38) </br> 自宅のエネルギー効率 (36) </br> 燃費 (37)
  6       |屋外生活 | 屋外バーベキュー (24) </br> 蚊の活動 (17)
  7       |ビーチと海 | ビーチとプール (10) </br> 魚釣り (13) </br> セーリング (11)
  8       |スポーツマン | 魚釣り (13) </br> ハンティング (20) </br> 屋外活動 (29)</br>
  9       |農業 |  田畑の状態 (32) </br>  土壌水分 (34)
  10      |健康 | 関節痛 (21) </br> ぜんそく (23) </br> 風邪 (25) </br> ほこりとフケ (18) </br> インフルエンザ (26) </br> 心臓によいフィットネス (16) </br> 片頭痛 (27)
  11      |アウトドア | 屋外バーベキュー (24) </br> ビーチとプール (10) </br> サイクリング (4) </br> 屋外コンサート (8) </br>  田畑の状態 (32) </br> 魚釣り (13) </br> ゴルフの天気 (5) </br> ハイキング (3) </br> ハンティング (20) </br> ジョギング (2) </br> たこ揚げ (9) </br> 蚊の活動 (17)</br> 芝刈り (28) </br> 屋外活動 (29) </br> ランニング (1) </br> セーリング (11) </br> スケートボード (7) </br> スキーの天気 (15) </br>  土壌水分 (34)</br> 天体観測 (12) </br> テニス (6)
  12      |スポーツ | サイクリング (4) </br> ゴルフの天気 (5) </br> ハイキング (3) </br>  ジョギング (2) </br> ランニング (1) </br> スケートボード (7) </br> スキーの天気 (15) </br>テニス (6)
  13      |ホーム | 自宅のエネルギー効率 (36) </br> 燃費 (37) </br> 屋内活動 (-2)

## <a name="daily-index-range-sets"></a>日次インデックスの範囲のセット

[Get Daily Indices API](/rest/api/maps/weather) は、各インデックス ID に対する範囲の値とそれに関連付けられているカテゴリ名を返します。 範囲のセットはすべてのインデックスで同じではありません。 次の表は、「[インデックス ID とインデックス グループ ID](#index-ids-and-index-groups-ids)」に一覧で示されているサポートされているインデックスによって使用されるさまざまな範囲のセットを示しています。 どのインデックスがどの範囲のセットを使用しているかを調べるには、このドキュメントの「[インデックス ID とインデックス グループ ID](#index-ids-and-index-groups-ids)」を参照してください。

### <a name="poor-excellent-1"></a>悪い - 非常に良い 1

  | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  悪い              |  0 |                2.99
  普通              |  3  |               4.99
  良い              |  5  |              6.99
  とても良い         |  7  |               8.99
  非常に良い         |  9  |               10

### <a name="poor-excellent-2"></a>悪い - 非常に良い 2

 | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  悪い           |0              |  3
  普通           |3.01           |  6
  良い           |6.01           |  7.5
  とても良い      |7.51           |  8.99
  非常に良い      |9              |  10

### <a name="excellent-poor"></a>非常に良い - 悪い

 | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  非常に良い      |     0.00        |    1.00
  とても良い        |   1.01          |  3.00
  良い             |   3.01          |  5.00
  普通             |   5.01          |  7.00
  悪い             |   7.01          |  10.00

### <a name="low-extreme-1"></a>低 - 極めて高い 1

   | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  低                |  0         |        1.99
  中           |  2         |        3.99
  高               |  4         |        5.99
  非常に高          |  6         |        7.99
  エクストリーム            |  8         |        10

### <a name="low-extreme-2"></a>低 - 極めて高い 2

   | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  低                |  0            |      2.99
  中           |  3            |      4.99
  高               |  5            |      6.99
  非常に高          |  7            |      8.99
  エクストリーム            |  9            |      10

### <a name="very-unlikely-very-likely"></a>めったにない - とてもよくある

 | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  めったにない      | 0     |           1.99
  あまりない           | 2     |           3.99
  たまにある           | 4     |           5.99
  よくある             | 6     |           7.99
  高い        | 8     |           10

### <a name="very-unlikely-very-likely-2"></a>めったにない - とてもよくある 2

 | カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  めったにない      |  0.00     |         1.00
  あまりない           |  1.01     |         3.00
  たまにある           |  3.01     |         5.00
  よくある             |  5.01     |         7.00
  高い        |  7.01     |         10.00

### <a name="unlikely-emergency"></a>あまりない - 緊急

| カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  あまりない         |  0     |          2.99
  Watch            |  3     |          4.99
  アドバイザリ         |  5     |          6.99
  警告          |  7     |          8.99
  緊急        |  9     |          10

### <a name="beneficial-at-extreme-risk"></a>有益 - 重大なリスク

| カテゴリ名 | 範囲の開始 | 範囲の終了 |
  ----------------|--------------|------------
  有益        |    0        |        1.99
  中立           |    2        |        3.99
  リスクあり           |    4        |        5.99
  高リスク      |    6        |        7.99
  重大なリスク   |    8        |        10