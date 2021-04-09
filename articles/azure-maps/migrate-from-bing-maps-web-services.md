---
title: チュートリアル:Bing 地図から Web サービスを移行する | Microsoft Azure Maps
description: Bing 地図から Microsoft Azure Maps に Web サービスを移行する方法に関するチュートリアルです。
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679071"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>チュートリアル:Bing 地図から Web サービスを移行する

Azure Maps と Bing 地図では、どちらの場合も REST Web サービスを介して空間 API にアクセスすることができます。 これらのプラットフォームの API インターフェイスは同様の機能を実行しますが、使用される名前付け規則と応答オブジェクトが異なります。 このチュートリアルでは、次の内容を学習します。

> * フォワード ジオコーディングと逆ジオコーディング
> * 目的地を検索する
> * ルートと道順を計算する
> * マップ イメージを取得する
> * 距離行列を計算する
> * タイム ゾーンの詳細を取得する

次の表に、記載されている Bing 地図サービス API と同様の機能を提供する Azure Maps サービス API を示します。

| Bing 地図サービス API                 | Azure Maps サービス API      |
|---------------------------------------|-----------------------------|
| 自動提案                           | [検索](/rest/api/maps/search)     |
| ルート案内 (トラックを含む)          | [ルートの道順](/rest/api/maps/route/getroutedirections)                          |
| 距離行列                       | [ルート マトリックス](/rest/api/maps/route/postroutematrixpreview)                          |
| 映像 - 静的マップ                  | [レンダリング](/rest/api/maps/render/getmapimage)                                   |
| 等時線                            | [ルート範囲](/rest/api/maps/route/getrouterange)                                    |
| Local Insights                        | [検索](/rest/api/maps/search) + [ルート範囲](/rest/api/maps/route/getrouterange)    |
| ローカル検索                          | [検索](/rest/api/maps/search)     |
| 位置情報認識 (POI)           | [検索](/rest/api/maps/search)     |
| 位置情報 (フォワード ジオコーディングと逆ジオコーディング) | [Search](/rest/api/maps/search)                                               |
| Snap to Road                          | [POST Route Directions](/rest/api/maps/route/postroutedirections)                         |
| Spatial Data Services (SDS)           | [検索](/rest/api/maps/search) + [ルート](/rest/api/maps/route) + その他の Azure サービス |
| タイム ゾーン                             | [タイム ゾーン](/rest/api/maps/timezone)  |
| トラフィック インシデント                     | [トラフィック インシデントの詳細](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Elevation                             | [Elevation (プレビュー)](/rest/api/maps/elevation)

次のサービス API は、Azure Maps では現在使用できません。

-   巡回ルートの最適化 - 対応予定。 Azure Maps ルート API は、車両が １ つの場合における巡回セールスマンの最適化には対応していません。
-   映像メタデータ - 主に Bing 地図のタイル URL を取得する目的で使用します。 Azure Maps には、マップ タイルに直接アクセスためのスタンドアロンのサービスがあります。

Azure Maps には、必要になる可能性のある追加の REST Web サービスがいくつか用意されています。

-   [Azure Maps Creator (プレビュー)](./creator-indoor-maps.md) - 建物や空間のプライベートなデジタル ツインを独自に作成できます。
-   [Spatial Operations](/rest/api/maps/spatial) - ジオフェンシングなどの複雑な空間計算や空間演算をサービスにオフロードします。
-   [マップ タイル](/rest/api/maps/render/getmaptile) - Azure Maps のラスターおよびベクター タイル形式の道路タイルとイメージ タイルにアクセスできます。
-   [バッチ ルート指定](/rest/api/maps/route/postroutedirectionsbatchpreview) - 1 つのバッチで一定期間にわたり最大 1,000 件のルート要求を実行できます。 処理の高速化のため、ルートはサーバー上で並列に計算されます。
-   [トラフィック](/rest/api/maps/traffic) フロー - ラスターおよびベクター タイル形式のリアルタイムのトラフィック フロー データにアクセスできます。
-   [位置情報 API (プレビュー)](/rest/api/maps/geolocation/getiptolocationpreview) - IP アドレスの位置情報を取得します。
-   [Weather サービス](/rest/api/maps/weather) - リアルタイムと予測の気象データにアクセスできます。

次のベスト プラクティス ガイドも確認してください。

-   [検索のベスト プラクティス](./how-to-use-best-practices-for-search.md)
-   [ルート指定のベスト プラクティス](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

## <a name="geocoding-addresses"></a>住所のジオコーディング

ジオコーディングは、住所 (たとえば、`"1 Microsoft way, Redmond, WA"`) を座標 (たとえば、経度: -122.1298、緯度: 47.64005) に変換するプロセスです。 多くの場合、この後、座標を使用して、マップにプッシュピンを配置したり、座標の場所をマップの中央に表示したりします。

Azure Maps では、住所のジオコーディング方法が複数提供されています。

-   [自由形式の住所のジオコーディング](/rest/api/maps/search/getsearchaddress): 1 行の住所文字列 (`"1 Microsoft way, Redmond, WA"` など) を指定します。要求は直ちに処理されます。 このサービスは、個々の住所を迅速にジオコーディングする必要がある場合に推奨されます。
-   [構造化された住所のジオコーディング](/rest/api/maps/search/getsearchaddressstructured): 単一の住所の一部 (番地、市区町村、国、郵便番号など) を指定します。要求はただちに処理されます。 このサービスは、個々の住所を迅速にジオコーディングする必要があり、データが既に個別の住所の一部として解析されている場合に推奨されます。
-   [住所のバッチ ジオコーディング](/rest/api/maps/search/postsearchaddressbatchpreview): 最大 10,000 個の住所を含む要求を作成します。これらの住所は一定期間内に処理されます。 すべての住所はサーバーで並行してジオコーディングされ、完了すると、完全な結果セットをダウンロードできます。 このサービスは、大きなデータ セットをジオコーディングする場合に推奨されます。
-   [あいまい検索](/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
-   [あいまいバッチ検索](/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

次の表では、構造化されたおよび自由形式の住所のジオコーディングについて、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

**住所指定の場所 (構造化された住所)**

| Bing 地図 API パラメーター              | 相当する Azure Maps API パラメーター                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`、`streetName` または `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` および `countryCode`                     |
| `locality`                         | `municipality` または `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | 該当なし - 常に Azure Maps によって返されます (利用可能な場合)。   |
| `include` (`incl`)               | 該当なし - 常に Azure Maps によって ISO2 国コードが返されます。 |
| `key`                              | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)                  | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。 |
| `userRegion` (`ur`)              | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

Azure Maps では、以下もサポートされます。

* `countrySecondarySubdivision` - 郡、行政区
* `countryTertiarySubdivision` - 特定の区域 (自治区、小郡、コミューン)
* `ofs` - `maxResults` パラメーターと組み合わせることで結果をページングします。

**クエリ指定の場所 (自由形式文字列の住所)**

| Bing 地図 API パラメーター              | 相当する Azure Maps API パラメーター      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | 該当なし - 常に Azure Maps によって返されます (利用可能な場合)。  |
| `include` (`incl`)               | 該当なし - 常に Azure Maps によって ISO2 国コードが返されます。  |
| `key`                              | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)                  | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。  |
| `userRegion` (`ur`)              | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

Azure Maps では、以下もサポートされます。

* `typeahead` - クエリを部分的な入力として解釈し、検索を予測モード (自動提案または自動補完) にするかどうかを指定します。
* `countrySet` - 検索対象を制限する ISO2 国コードのコンマ区切りリスト。
* `lat`/`lon`、`topLeft`/`btmRight`、`radius` - ユーザーの位置情報とエリアを指定して、指定場所との関連性が高い結果を得ます。
* `ofs` - `maxResults` パラメーターと組み合わせることで結果をページングします。

検索サービスの使用方法の例については、[こちら](./how-to-search-for-address.md)を参照してください。 [検索のベスト プラクティス](./how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>座標の逆ジオコーディング (ポイント指定による場所検索)

逆ジオコーディングは、地理座標 (たとえば、経度: -122.1298、緯度: 47.64005) をおよその住所 (たとえば、`"1 Microsoft way, Redmond, WA"`) に変換するプロセスです。

Azure Maps では、逆ジオコーディング方法が複数の提供されています。

-   [住所の逆ジオコーダー](/rest/api/maps/search/getsearchaddressreverse): 1 つの地理座標を指定すると、そのおよその住所が取得されます。要求はただちに処理されます。
-   [交差点の逆ジオコーダー](/rest/api/maps/search/getsearchaddressreversecrossstreet): 1 つの地理座標を指定すると周辺の交差点情報 (たとえば、最初の交差点や主要交差点など) が取得されます。要求はただちに処理されます。
-   [住所のバッチ逆ジオコーダー](/rest/api/maps/search/postsearchaddressreversebatchpreview): 最大 10,000 個の座標を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター              | 相当する Azure Maps API パラメーター       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` - 下記のエンティティ型対応表を参照してください。    |
| `includeNeighborhood` (`inclnb`)     | 該当なし - 常に Azure Maps によって返されます (利用可能な場合)。         |
| `include` (`incl`)                   | 該当なし - 常に Azure Maps によって ISO2 国コードが返されます。    |
| `key`                                | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)                      | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。   |
| `userRegion` (`ur`)                  | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

[検索のベスト プラクティス](./how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

Azure Maps の逆ジオコーディング API には、Bing 地図では利用できない追加機能がいくつかあります。これらは、アプリを移行する際に統合するのに役立つ場合があります。

* 速度制限のデータの取得。
* 道路使用情報の取得 (地方の道路、幹線道路、自動車専用道路、ランプなど)。
* 座標が位置する通りの側。

**エンティティ型対応表**

次の表では、Bing 地図のエンティティ型の値と、これらに相当する Azure Maps のプロパティ名を相互参照で示しています。

| Bing 地図のエンティティ型 | 相当する Azure Maps のエンティティ型               | 説明                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *アドレス*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | "*近隣*"                             |
| `PopulatedPlace`      | `Municipality` または `MunicipalitySubdivision`     | "*市*"、"*町*"、または "*地区*"     |
| `Postcode1`           | `PostalCodeArea`                                | "*郵便番号*" または "*ZIP コード*"                |
| `AdminDivision1`      | `CountrySubdivision`                            | "*州*" または "*都道府県*"                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | "*郡*" または "*行政区*"                    |
| `CountryRegion`       | `Country`                                       | *国名*                             |
|                       | `CountryTertiarySubdivision`                    | "*自治区*"、"*小郡*"、"*コミューン*"          |

## <a name="get-location-suggestions-autosuggest"></a>場所候補の取得 (自動提案)

いくつかの Azure Maps 検索 API では、自動提案シナリオに使用可能な予測モードがサポートされています。 Bing 地図の Autosuggest API に最も近いのは、Azure Maps の[あいまい検索](/rest/api/maps/search/getsearchfuzzy) API です。 次の API も予測モードに対応しています。クエリに `&typeahead=true` を追加してください。

-   [自由形式の住所のジオコーディング](/rest/api/maps/search/getsearchaddress): 1 行の住所文字列 (`"1 Microsoft way, Redmond, WA"` など) を指定します。要求は直ちに処理されます。 このサービスは、個々の住所を迅速にジオコーディングする必要がある場合に推奨されます。
-   [あいまい検索](/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
-   [POI 検索](/rest/api/maps/search/getsearchpoi): 目的地を名前で検索します。 たとえば、`"starbucks"` などです。
-   [POI カテゴリ検索](/rest/api/maps/search/getsearchpoicategory): 目的地をカテゴリで検索します。 たとえば、"restaurant" などです。

## <a name="calculate-routes-and-directions"></a>ルートと道順を計算する

Azure Maps を使用してルートと道順を計算することができます。 Azure Maps には、Bing 地図のルート指定サービスと同じ機能が数多く用意されています。たとえば、次のようなものがあります。

* 到着および出発の時刻
* リアルタイムおよび予測ベースの交通路
* さまざまな移動手段 (乗用車、徒歩、トラック)
* ウェイポイントの順序の最適化 (巡回セールスマン)

> [!NOTE]
> Azure Maps では、すべてのウェイポイントを座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

Azure Maps のルート指定サービスでは、ルート計算用に次の API が提供されます。

-   [ルート計算](/rest/api/maps/route/getroutedirections): ルートが計算されます。要求はただちに処理されます。 この API では、GET 要求と POST 要求の両方がサポートされます。 大量のウェイポイントを指定する場合、または多くのルート オプションを使用する場合は、URL 要求が長くなりすぎて問題が発生することがないように POST 要求が推奨されます。
-   [ルートのバッチ処理](/rest/api/maps/route/postroutedirectionsbatchpreview): 最大 1,000 個のルート要求を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。
-   [Mobility Service (プレビュー)](/rest/api/maps/mobility): 公共輸送を使用するルートと道順が計算されます。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター                                    | 相当する Azure Maps API パラメーター               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` または `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/A                                               |
| `distanceUnit` (`du`)                                      | N/A - Azure Maps ではメートル法のみが使用されます。     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`、`alternativeType`、`minDeviationDistance`、および `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` および `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` または `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/A                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) または `viaWaypoint.n` (`vwp.n`)         | `query` - `lat0,lon0:lat1,lon1….` 形式の座標   |
| `key`                                                      | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)                                            | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。 |
| `userRegion` (`ur`)                                        | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

Azure Maps のルート指定 API では、同一 API 内でのトラックのルート指定もサポートされています。 次の表では、Bing 地図の追加のトラック ルート指定パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター                  | 相当する Azure Maps API パラメーター        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | 該当なし - メートル単位の寸法のみがサポートされます。 |
| `weightUnit` (`wu`)                      | 該当なし - キログラム単位の重量のみがサポートされます。 |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/A**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> 既定では、Azure Maps ルート API により、概要 (距離と時間) とルートの座標のみが返されます。 道案内の指示を取得するには、`instructionsType` パラメーターを使用します。 `routeRepresentation` パラメーターを使用すると、概要とルートをフィルター処理することができます。

[ルート指定のベスト プラクティス](./how-to-use-best-practices-for-routing.md)に関するドキュメントも参照してください。

Azure Maps のルート指定 API には、Bing 地図では利用できない追加機能が多数用意されています。これらは、アプリを移行する際に統合するのに役立つ場合があります。

* ルートの種類のサポート: 最短、最速、スリリング、最も燃費が良い。
* 追加の移動モードのサポート: 自転車、バス、バイク、タクシー、トラック、ライトバン。
* 150 個のウェイポイントのサポート。
* 1 つの要求で複数の移動時間を計算: 過去の交通量、現在の交通量、交通量なし。
* 追加の道路の種類を回避: 自動車の相乗り用車線、未舗装道路、既に使用したことがある道路。
* エンジン仕様に基づくルート指定。 燃料または充電の残量とエンジン仕様に基づいて内燃自動車または電気自動車のルートを計算。
* 車両の最大速度を指定。

## <a name="snap-coordinates-to-road"></a>座標を道路にスナップする

Azure Maps には、道路に座標をスナップする方法がいくつかあります。

* 道路ネットワークに沿った論理ルートに座標をスナップするには、ルートの道順 API を使用します。
* ベクター タイルに含まれる最も近い道路に個々の座標をスナップするには、Azure Maps Web SDK を使用します。
* 座標を個別にスナップするには、Azure Maps のベクター タイルを直接使用します。

**ルートの道順 API を使用して座標をスナップする**

Azure Maps では、[ルートの道順](/rest/api/maps/route/postroutedirections) API を使用して道路に座標をスナップすることができます。 このサービスは、一連の座標の間の論理ルートの再構築に使用でき、Bing 地図の Snap to Road API に相当します。

ルートの道順 API を使用して道路に座標をスナップする方法は 2 種類あります。

* 座標が 150 個以下の場合は、GET Route Directions API でウェイポイントとして渡します。 この方法では、スナップされた 2 種類のデータを取得することができます。ルート指示には個々のスナップされたウェイポイントが含まれるのに対し、ルート経路には座標間の経路全体を埋める一連の補完座標が含まれます。
* 座標が 150 個を超える場合は、POST Route Directions API を使用します。 クエリ パラメーターに始点座標と終点座標を渡す必要がありますが、POST 要求の本文ですべての座標を `supportingPoints` パラメーターに渡し、GeoJSON ジオメトリ形式のポイントのコレクションをフォーマットできます。 この方法で利用できるスナップされたデータは、座標間の経路全体を埋める一連の補完座標で構成されたルート経路のみです。 [こちらの例](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path)では、Azure Maps Web SDK のサービス モジュールを使用した場合の実行方法を紹介しています。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター    | 相当する Azure Maps API パラメーター                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` - Post 要求の本文にこれらのポイントを渡します  |
| `interpolate`              | N/A                                                                 |
| `includeSpeedLimit`        | 該当なし                                                                 |
| `includeTruckSpeedLimit`   | 該当なし                                                                 |
| `speedUnit`                | N/A                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)            | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。   |
| `userRegion` (`ur`)        | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。   |

Azure Maps ルート指定 API では、論理経路の計算を行う同じ API 内のトラックのルート指定パラメーターもサポートされています。 次の表では、Bing 地図の追加のトラック ルート指定パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター                 | 相当する Azure Maps API パラメーター        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | 該当なし - メートル単位の寸法のみがサポートされます。 |
| `weightUnit` (`wu`)                     | 該当なし - キログラム単位の重量のみがサポートされます。 |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/A**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

この方法ではルートの道順 API を使用するため、該当するサービスのオプションすべてを使用して、道路への座標のスナップに使用するロジックをカスタマイズできます。 たとえば、出発時刻を指定すると、過去のトラフィック データが考慮されます。

現在、Azure Maps のルートの道順 API では速度制限のデータが返されませんが、Azure Maps の逆ジオコーディング API を使用することで取得できます。

**Web SDK を使用して座標をスナップする**

Azure Maps Web SDK では、マップのレンダリングにベクター タイルを使用します。 これらのベクター タイルには未処理の道路形状の情報が含まれており、個々の座標について単純なスナップを行う場合に、座標に最も近い道路を算出するのに使用できます。 これは、座標を道路に視覚的に重ねて表示したいとき、既に Azure Maps Web SDK を使用してデータを可視化している場合に便利です。

ただし、この方法では、スナップの対象はマップ ビューに読み込まれている道路セグメントだけになります。 国レベルで縮小した場合、道路データがなければスナップは実行できません。ただし、このズーム レベルでは、1 つのピクセルで複数の都市ブロックが含まれる領域を表すことができるので、スナップは必要ありません。 これを解決するには、マップの移動が完了するたびにスナップのロジックを適用します。 これを示す[コード サンプルはこちら](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)です。

**Azure Maps のベクター タイルを直接使用して座標をスナップする**

Azure Maps のベクター タイルには未処理の道路形状データが含まれており、このデータをもとにして座標に最も近い道路上のポイントを算出して、個々の座標について基本的なスナップを行うことができます。 ズーム レベル 15 で地区のすべての道路セグメントが表示されるので、ここからタイルを取得します。 その後、[4 分木タイル ピラミッドの演算](./zoom-levels-and-tile-grid.md)を使用し、必要なタイルを判別してジオメトリに変換することができます。 さらに、[turf js](http://turfjs.org/) や [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) などの空間演算ライブラリを使用することで、最も近い線分を算出できます。

## <a name="retrieve-a-map-image-static-map"></a>マップ イメージを取得する (静的マップ)

Azure Maps には、静的マップ イメージにデータを重ねてレンダリングするための API が用意されています。 Azure Maps の[マップ イメージのレンダリング](/rest/api/maps/render/getmapimagerytile) API は、Bing 地図の静的マップ API に相当します。

> [!NOTE]
> Azure Maps では、中央、すべてのプッシュピン、経路の位置を `longitude,latitude` 形式の座標にする必要がありますが、Bing 地図では `latitude,longitude` 形式が使用されます。</p>
<p>住所は、最初にジオコーディングする必要があります。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター  | 相当する Azure Maps API パラメーター            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` - URL パスの一部として指定されます。 現時点では、PNG のみがサポートされています。  |
| `heading`                | 該当なし - ストリート ビューはサポートされていません。                |
| `imagerySet`             | `layer` および `style` - [サポートされているマップ スタイル](./supported-map-styles.md)に関するドキュメントを参照してください。   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/A                                            |
| `mapSize` (`ms`)         | `width` および `height` - 最大 8192 x 8192 のサイズを指定できます。 |
| `declutterPins` (`dcl`)  | N/A                                            |
| `dpi`                    | 該当なし                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/A                                            |
| `pitch`                  | 該当なし - ストリート ビューはサポートされていません。                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | 該当なし - 中央または境界ボックスを使用する必要があります。     |
| `highlightEntity` (`he`) | N/A                                            |
| `style`                  | N/A                                            |
| ルート パラメーター         | N/A                                            |
| `key`                    | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)          | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。   |
| `userRegion` (`ur`)      | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

> [!NOTE]
> Azure Maps では、Bing 地図で使用されるマップ タイルの 2 倍のサイズのタイルを使用するタイル システムが使用されます。 このため、Azure Maps のズーム レベル値では、Bing 地図と比べて 1 つ上のズーム レベルが表示されます。 移行する要求では、ズーム レベルを 1 つ下げて、これを補正してください。

詳細については、[マップ イメージのレンダリング API に関する攻略ガイド](./how-to-render-custom-data.md)を参照してください。

Azure Maps のレンダリング サービスでは、静的マップ イメージを生成できるだけでなく、ラスター (PNG) およびベクター形式のマップ タイルに直接アクセスすることもできます。

-   [マップ タイル](/rest/api/maps/render/getmaptile) - ベース マップ (道路、境界、背景) のラスター (PNG) およびベクター タイルを取得します。
-   [マップ イメージ タイル](/rest/api/maps/render/getmapimagerytile) - 航空写真と衛星画像のタイルを取得します。

### <a name="pushpin-url-parameter-format-comparison"></a>プッシュピン URL パラメーターの形式の比較

**前: Bing 地図**

Bing 地図では、URL で `pushpin` パラメーターを使用することにより、プッシュピンを静的マップ イメージに追加できます。 `pushpin` パラメーターは、次に示すように、`latitude,longitude` 形式の場所、アイコンのスタイル、テキスト ラベル (最大 3 文字) を受け取ります。

> `&pushpin=latitude,longitude;iconStyle;label`

プッシュピンを追加するには、異なる値を指定した別の `pushpin` パラメーターを URL に追加します。 プッシュピンのアイコンのスタイルは、Bing 地図 API で利用可能ないずれかの定義済みのスタイルに限られます。

たとえば、Bing 地図で、マップの座標 (経度: -110、緯度: 45) に "AB" ラベル付きの赤色のプッシュピンを追加するには、次の URL パラメーターを使用します。

> `&pushpin=45,-110;7;AB`

![Bing 地図の静的マップのピン](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**後: Azure Maps**

Azure Maps では、URL で `pins` パラメーターを指定することにより、プッシュピンを静的マップ イメージに追加できます。 Azure Maps のプッシュピンは、アイコンのスタイルと、そのアイコンのスタイルを使用する場所の一覧を指定することで定義します。 この情報は `pins` パラメーターに渡されます。パラメーターを複数回指定することで、異なるスタイルのプッシュピンをサポートできます。

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

追加のスタイルを使用するには、異なるスタイルや場所のセットを指定した別の `pins` パラメーターを URL に追加します。

ピンの位置について、Azure Maps では座標を `longitude latitude` 形式にする必要がありますが、Bing 地図では `latitude,longitude` 形式が使用されます。 さらに、Azure Maps では、経度と緯度を **コンマではなくスペース** で区切ることにも注意してください。

`iconType` 値は、作成するピンの種類を指定します。次の値を指定することができます。

* `default` - 既定のピン アイコン。
* `none` - アイコンは表示されず、ラベルのみがレンダリングされます。
* `custom` - カスタム アイコンを使用することを指定します。 アイコン イメージを指す URL は、ピンの位置情報の後、`pins` パラメーターの末尾に追加できます。
* `{udid}` - Azure Maps データ ストレージ プラットフォームに格納されるアイコンの固有データ ID (UDID)。

Azure Maps でピンのスタイルを追加するには、形式 `optionNameValue` を使用します。複数のスタイルを指定する場合は、`iconType|optionName1Value1|optionName2Value2` のようにパイプ (`|`) 文字で区切ります。 オプションの名前と値は区切らないことに注意してください。 Azure Maps でプッシュピンのスタイルを設定するには、次のスタイル オプション名を使用します。

* `al` - プッシュピンの不透明度 (アルファ) を指定します。 0 から 1 までの範囲の数値を指定できます。
* `an` - ピンのアンカーを指定します。 x と y のピクセル値を `x y` 形式で指定します。
* `co` - ピンの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定する必要があります。
* `la` - ラベルのアンカーを指定します。 x と y のピクセル値を `x y` 形式で指定します。
* `lc` - ラベルの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定する必要があります。
* `ls` - ラベルのサイズ (ピクセル単位)。 0 より大きい数値を指定できます。
* `ro` - アイコンを回転させる角度の値。 -360 から 360 までの範囲の数値を指定できます。
* `sc` - ピン アイコンのスケール値。 0 より大きい数値を指定できます。

場所の一覧内にあるすべてのプッシュピンに単一のラベル値を適用するのではなく、ピンの位置ごとにラベル値を指定します。 ラベル値には、複数の文字で構成される文字列を指定することができます。また、スタイルや場所の値として誤って解釈されないようにするため、一重引用符で囲むこともできます。

たとえば、Azure Maps で、赤色 (`FF0000`) の既定のアイコンと、座標 (経度: -122.349300、緯度: 47.620180) にあるアイコンの下 (15 50) に位置付けられたラベル "Space Needle" を追加するには、次の URL パラメーターを使用します。

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Azure Maps の静的マップ上にあるピン](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

次の例では、ラベル値が 1'、'2'、'3' の 3 つのピンを追加します。

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Azure Maps の静的マップ上にある複数のピン](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>曲線描画 URL パラメーターの形式の比較

**前: Bing 地図**

Bing 地図では、URL で `drawCurve` パラメーターを使用することにより、線とポリゴンを静的マップ イメージに追加できます。 `drawCurve` パラメーターは、次に示すように、シェイプの種類、スタイルの種類、マップにレンダリングされる場所の一覧を受け取ります。

> `&drawCurve=shapeType,styleType,location1,location2...`

追加のスタイルを使用するには、異なるスタイルや場所のセットを指定した別の `drawCurve` パラメーターを URL に追加します。

Bing 地図で場所を指定するには、`latitude1,longitude1_latitude2,longitude2_…` 形式を使用します。 場所もエンコード可能です。

Bing 地図のシェイプの種類には、線、ポリゴン、円、曲線があります。 スタイルの種類には、線の色、線の太さ、枠線の色、塗りつぶしの色、枠線の太さ、円の半径があります。

たとえば、Bing 地図で、不透明度が 50%、太さが 4 ピクセルの青色の線をマップの座標 (経度: -110、緯度: 45) と座標 (経度: -100、緯度: 50) の間に追加するには、次の URL を使用します。

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Bing 地図の静的マップの線](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**後: Azure Maps**

Azure Maps では、URL で *path* パラメーターを指定することにより、線とポリゴンを静的マップ イメージに追加できます。 Bing 地図と同様に、このパラメーターでスタイルと場所の一覧を指定できます。また、*path* パラメーターを複数回指定することで、異なるスタイルの円、線、ポリゴンを複数レンダリングできます。

> `&path=pathStyles||pathLocation1|pathLocation2|...`

経路の位置について、Azure Maps では座標を `longitude latitude` 形式にする必要がありますが、Bing 地図では `latitude,longitude` 形式が使用されます。 さらに、Azure Maps では、経度と緯度を **コンマではなくスペース** で区切ることにも注意してください。 現在、Azure Maps では、エンコードされた経路はサポートされません。 大きなデータ セットを GeoJSON 塗りつぶしとして Azure Maps データ ストレージ API に読み込むことができます。[こちら](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)を参照してください。

Azure Maps で経路のスタイルを追加するには、形式 `optionNameValue` を使用します。複数のスタイルを指定する場合は、`optionName1Value1|optionName2Value2` のようにパイプ (`|`) 文字で区切ります。 オプションの名前と値は区切らないことに注意してください。 Azure Maps で経路のスタイルを設定するには、次のスタイル オプション名を使用します。

* `fa` - ポリゴンのレンダリングに使用する塗りつぶしの色の不透明度 (アルファ)。 0 から 1 までの範囲の数値を指定できます。
* `fc` - ポリゴンの領域のレンダリングに使用する塗りつぶしの色。
* `la` - 線およびポリゴンの枠線をレンダリングする際に使用される線の色の不透明度 (アルファ)。 0 から 1 までの範囲の数値を指定できます。
* `lc` - 線およびポリゴンの枠線をレンダリングするために使用される線の色。
* `lw` - 線の幅 (ピクセル単位)。
* `ra` - 円の半径をメートル単位で指定します。

たとえば、Azure Maps で、不透明度が 50%、太さが 4 ピクセルの青色の線をマップの座標 (経度: -110、緯度: 45) と座標 (経度: -100、緯度: 50) の間に追加するには、次の URL を使用します。

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Azure Maps の静的マップの線](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>距離行列を計算する

Azure Maps には、場所のセット間の移動時間と移動距離を距離行列として計算するための API が用意されています。 Azure Maps の距離行列 API は、Bing 地図の距離行列 API に相当します。

-   [ルート マトリックス](/rest/api/maps/route/postroutematrixpreview): 出発地と目的地のセットについて移動時間と移動距離が非同期で計算されます。 要求あたり最大 700 個のセルがサポートされます (出発地の数 × 目的地の数)。 この制限を念頭に置いて考えられるマトリックス ディメンションの例としては、`700x1`、`50x10`、`10x10`、`28x25`、`10x70` があります。

> [!NOTE]
> 距離行列 API に対する要求は、要求の本文で出発地と目的地の情報を指定した POST 要求を使用してのみ作成できます。</p>
<p>さらに、Azure Maps では、すべての出発地および目的地を座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター | 相当する Azure Maps API パラメーター                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` - POST 要求の本文で GeoJSON として指定します。    |
| `destinations`          | `destination` - POST 要求の本文で GeoJSON として指定します。 |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/A                                                         |
| `distanceUnit`          | 該当なし - 距離はすべてメートル単位です。                              |
| `timeUnit`              | 該当なし - 時間はすべて秒単位です。                                 |
| `key`                   | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)         | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。  |
| `userRegion` (`ur`)     | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。     |

> [!TIP]
> Azure Maps のルート指定 API で使用できる高度なルート指定オプション (トラック ルート指定、エンジン仕様、回避など) はすべて、Azure Maps 距離行列 API でサポートされます。

## <a name="calculate-an-isochrone"></a>等時線の計算

Azure Maps では、等時線を計算するための API が提供されます。これは、指定された時間内または燃料や充電の残量内で出発地点から任意の方向に移動できる領域をカバーするポリゴンです。 Azure Maps のルートのレンダリング API は、Bing 地図の等時線 API に相当します。

-   [ルート](/rest/api/maps/route/getrouterange)範囲**:指定された時間内、距離内、または使用可能な燃料や充電の残量内で出発地点から任意の方向に移動できる領域をカバーするポリゴンを計算します。

> [!NOTE]
> Azure Maps では、クエリの出発地を座標で指定する必要があります。 住所は、最初にジオコーディングする必要があります。</p>
<p>また、Bing 地図では、時間や距離に基づいて等時線を計算できますが、Azure Maps では、時間、距離、使用可能な燃料や充電の残量に基づいて等時線の計算を行うことができます。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター      | 相当する Azure Maps API パラメーター            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | 該当なし - 時間はすべて秒単位です。                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | 該当なし - 距離はすべてメートル単位です。                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)              | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。  |
| `userRegion` (`ur`)          | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

> [!TIP]
> Azure Maps のルート指定 API で使用できる高度なルート指定オプション (トラック ルート指定、エンジン仕様、回避など) はすべて、Azure Maps の等時線 API でサポートされます。

## <a name="search-for-points-of-interest"></a>目的地を検索する

Bing 地図で目的地データを検索するには、次の API を使用します。

-   **ローカル検索:** 名前またはエンティティの種類 (カテゴリ) を指定して、近くにある目的地を検索します (放射状検索)。 この API に最も近い Azure Maps の API は、[POI 検索](/rest/api/maps/search/getsearchpoi)と [POI カテゴリ検索](/rest/api/maps/search/getsearchpoicategory)です。
-   **位置情報認識**:ある場所から特定の距離内にある目的地を検索します。 この API に最も近い Azure Maps の API は、[近隣検索](/rest/api/maps/search/getsearchnearby)です。
-   **ローカル情報:** 特定の座標から指定の最大走行時間内または最長走行距離内にある目的地を検索します。 これは、Azure Maps では、まず等時線を計算してから[ジオメトリ内検索](/rest/api/maps/search/postsearchinsidegeometry) API に渡すことで実現できます。

Azure Maps には、目的地を検索するための複数の検索 API が用意されています。

-   [POI 検索](/rest/api/maps/search/getsearchpoi): 目的地を名前で検索します。 たとえば、`"starbucks"` などです。
-   [POI カテゴリ検索](/rest/api/maps/search/getsearchpoicategory): 目的地をカテゴリで検索します。 たとえば、"restaurant" などです。
-   [近隣検索](/rest/api/maps/search/getsearchnearby): ある場所から特定の距離内にある目的地を検索します。
-   [あいまい検索](/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
-   [ジオメトリ内検索](/rest/api/maps/search/postsearchinsidegeometry):指定されたジオメトリ (ポリゴン) 内にある目的地を検索します。
-   [ルートに沿った検索](/rest/api/maps/search/postsearchalongroute): 指定されたルートに沿って目的地を検索します。
-   [あいまいバッチ検索](/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

[検索のベスト プラクティス](./how-to-use-best-practices-for-search.md)に関するドキュメントを、必ずご覧ください。

## <a name="get-traffic-incidents"></a>トラフィック インシデントの取得

Azure Maps では、トラフィック データを取得するための API が複数提供されます。 使用できるトラフィック データには、次の 2 種類があります。

* **フロー データ** - 道路区間のトラフィックのフローに関するメトリックを提供します。 これは一般に、道路を色分けするために使用します。 データは 2 分ごとに更新されます。
* **インシデント データ** - 工事、通行止め、事故、およびトラフィックに影響する可能性があるその他のインシデントについてのデータを提供します。 データは 1 分ごとに更新されます。

Bing 地図では、対話型のマップ コントロールでトラフィック フローとインシデントのデータが提供されるほか、インシデント データはサービスとしても公開されています。

また、Azure Maps の対話型のマップ コントロールには、インシデント データが統合されています。 Azure Maps では、次のトラフィック サービス API も用意されています。

-   [トラフィック フロー セグメント](/rest/api/maps/traffic/gettrafficflowsegment):指定された座標に最も近い道路フラグメントについて、速度と移動時間の情報を提供します。
-   [トラフィック フロー タイル](/rest/api/maps/traffic/gettrafficflowtile):トラフィック フローのデータが含まれるラスター タイルとベクター タイルを提供します。 これらは、Azure Maps のコントロールや、Leaflet などのサードパーティ製マップ コントロールで使用できます。 ベクター タイルは、高度なデータ分析にも使用可能です。
-   [トラフィック インシデントの詳細](/rest/api/maps/traffic/gettrafficincidentdetail):境界ボックス内、ズーム レベル内、およびトラフィック モデル内に含まれるトラフィック インシデントの詳細を提供します。
-   [トラフィック インシデント タイル](/rest/api/maps/traffic/gettrafficincidenttile):トラフィック インシデントのデータが含まれるラスター タイルとベクター タイルを提供します。
-   [トラフィック インシデント ビューポート](/rest/api/maps/traffic/gettrafficincidentviewport):トラフィック モデルの ID など、要求に記載されているビューポートの法的情報や技術情報を取得します。

次の表では、Bing 地図のトラフィック API パラメーターと、それに相当する Azure Maps 内のトラフィック インシデントの詳細 API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター  | 相当する Azure Maps API パラメーター   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` および `boundingZoom`      |
| `includeLocationCodes`   | N/A                                   |
| `severity` (`s`)         | 該当なし - すべてのデータが返されます               |
| `type` (`t`)             | 該当なし - すべてのデータが返されます               |
| `key`                    | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)          | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。 |
| `userRegion` (`ur`)      | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。 |

## <a name="get-a-time-zone"></a>タイム ゾーンを取得する

Azure Maps には、座標が位置する場所のタイム ゾーンを取得するための API が用意されています。 Azure Maps のタイム ゾーン API は、Bing 地図のタイム ゾーン API に相当します。

-   [座標によるタイム ゾーン](/rest/api/maps/timezone/gettimezonebycoordinates):座標を指定して、その座標が位置する場所のタイム ゾーンの詳細を取得します。

次の表では、Bing 地図 API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Bing 地図 API パラメーター | 相当する Azure Maps API パラメーター          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | 該当なし - 場所は最初にジオコーディングする必要があります。      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | 該当なし - Azure Maps からの応答には常に含まれます。 |
| `key`                   | `subscription-key` - ドキュメント「[Azure Maps による認証](./azure-maps-authentication.md)」も参照してください。 |
| `culture` (`c`)         | `language` - [サポートされている言語](./supported-languages.md)に関するドキュメントも参照してください。  |
| `userRegion` (`ur`)     | `view` - [サポートされているビュー](./supported-languages.md#azure-maps-supported-views)に関するドキュメントを参照してください。  |

Azure Maps プラットフォームでは、この API に加えて、タイム ゾーン名と ID の変換に役立つ他のタイム ゾーン API も多数用意されています。

-   [ID によるタイム ゾーン](/rest/api/maps/timezone/gettimezonebyid): 指定された IANA タイム ゾーン ID について現在、履歴、将来のタイム ゾーンに関する情報が返されます。
-   [タイム ゾーン列挙型 IANA](/rest/api/maps/timezone/gettimezoneenumiana): IANA タイム ゾーン ID の完全な一覧が返されます。 IANA サービスの更新は、1 日以内にシステムに反映されます。 
-   [タイム ゾーン列挙型 Windows](/rest/api/maps/timezone/gettimezoneenumwindows): Windows タイム ゾーン ID の完全な一覧が返されます。
-   [タイム ゾーン IANA バージョン](/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps で現在使用されている IANA のバージョン番号が返されます。 
-   [タイム ゾーン Windows から IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): 有効な Windows タイム ゾーン ID が指定された場合、対応する IANA ID が返されます。 1 つの Windows ID に対して複数の IANA ID が返される場合があります。

## <a name="spatial-data-services-sds"></a>Spatial Data Services (SDS)

Bing 地図の Spatial Data Services では、3 つの主要な機能が提供されます。

* バッチ ジオコーディング - 1 つの要求で複数の住所ジオコードが含まれる大規模バッチを処理します。
* 行政境界データの取得 - 座標を使用して、指定されたエンティティの種類について区分けの境界を取得します。
* 空間ビジネス データのホストおよび照会 – 単純な 2 次元のデータ テーブルをアップロードして、いくつかのシンプルな空間クエリによりテーブルにアクセスします。

### <a name="batch-geocode-data"></a>データのバッチ ジオコーディング

バッチ ジオコーディングとは、多数の住所または場所を受け取り、それらすべてを 1 つの要求でサービスに渡して並列でジオコーディングし、1 つの応答で結果を受け取るプロセスのことです。

Bing 地図では、1 つのバッチ ジオコーディング要求で最大 200,000 個の住所を解析できます。 この要求はキューに入れられ、データ セットのサイズとサービスにかかる負荷に応じて、通常は数分から数時間の間に処理されます。 要求に含まれる住所ごとに、トランザクションが生成されました。

Azure Maps にもバッチ ジオコーディング サービスはありますが、1 つの要求で渡すことのできる住所は最大 10,000 個であり、要求の処理にかかる時間は、データ セットのサイズとサービスにかかる負荷にもよりますが数秒から数分です。 要求に含まれる住所ごとに、トランザクションが生成されました。 Azure Maps でバッチ ジオコーディング サービスを利用できるのは、S1 サービス レベルのみです。

Azure Maps での大量の住所のジオコーディングでは、標準の検索 API に並列要求を行う方法もあります。 これらのサービスでは、要求ごとに 1 つの住所しか受け入れられませんが、使用量制限のない S0 サービス レベルでも使用可能です。 S0 サービス レベルでは、1 つのアカウントから 1 秒あたり最大 50 個の要求を Azure Maps プラットフォームに行うことができます。 そのため、これらの制限の範囲内で処理を行うのであれば、1 時間に最大 18 万個の住所をジオコーディングできます。 S1 サービス レベルでは、1 つのアカウントから 1 秒あたりに実行可能なクエリの数に関する制限の規定はないため、この価格レベルでは大量のデータを短時間で処理できます。ただし、バッチ ジオコーディング サービスを使うと、転送されるデータの総量を抑え、ネットワーク トラフィックを大幅に削減できます。

-   [自由形式の住所のジオコーディング](/rest/api/maps/search/getsearchaddress): 1 行の住所文字列 (`"1 Microsoft way, Redmond, WA"` など) を指定します。要求は直ちに処理されます。 このサービスは、個々の住所を迅速にジオコーディングする必要がある場合に推奨されます。
-   [構造化された住所のジオコーディング](/rest/api/maps/search/getsearchaddressstructured): 単一の住所の一部 (番地、市区町村、国、郵便番号など) を指定します。要求はただちに処理されます。 このサービスは、個々の住所を迅速にジオコーディングする必要があり、データが既に個別の住所の一部として解析されている場合に推奨されます。
-   [住所のバッチ ジオコーディング](/rest/api/maps/search/postsearchaddressbatchpreview): 最大 10,000 個の住所を含む要求を作成します。これらの住所は一定期間内に処理されます。 すべての住所はサーバーで並行してジオコーディングされ、完了すると、完全な結果セットをダウンロードできます。 このサービスは、大きなデータ セットをジオコーディングする場合に推奨されます。
-   [あいまい検索](/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。要求はただちに処理されます。 この API は、ユーザーが同じテキスト ボックスから住所や目的地を検索できるアプリケーションに適しています。
-   [あいまいバッチ検索](/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

### <a name="get-administrative-boundary-data"></a>行政境界データの取得

Bing 地図では、Geodata API 経由で国、州、郡、都市、郵便番号の行政境界が提供されています。 この API は、ジオコーディング対象の座標またはクエリを受け取ります。 渡したクエリはジオコーディングされ、最初の結果の座標が使用されます。 この API は座標を受け取り、指定されたエンティティの種類についてその座標と交わる境界を取得します。 この API では、クエリを渡した場合、必ずしも境界は返されないことに注意してください。 `"Seattle, WA"` に対するクエリを渡しても、エンティティの種類の値が国および地域に設定されている場合、返されるのは米国の境界です。

Azure Maps では、行政境界 (国、州、郡、都市、郵便番号) にアクセスすることもできます。 境界を取得するには、目的の境界 (`Seattle, WA` など) についていずれかの検索 API に対しクエリを実行する必要があります。 検索結果に関連する境界が含まれる場合、結果の応答でジオメトリ ID が提供されます。 その後、ポリゴン検索 API を使用して、1 つ以上の ID について正確な境界を取得できます。 この点が Bing 地図とは少し異なります。Azure Maps では検索対象の境界が返されますが、Bing 地図で返されるのは、特定座標にある指定されたエンティティの種類の境界です。 また、Azure Maps で返される境界データは GeoJSON 形式です。

要点は次のとおりです。

1.  次のいずれかの検索 API に、取得したい境界に関するクエリを渡します。
    -   [自由形式の住所のジオコーディング](/rest/api/maps/search/getsearchaddress)
    -   [構造化された住所のジオコーディング](/rest/api/maps/search/getsearchaddressstructured)
    -   [住所のバッチ ジオコーディング](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [あいまい検索](/rest/api/maps/search/getsearchfuzzy)
    -   [あいまいバッチ検索](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  目的の結果にジオメトリ ID が含まれていれば、その ID を[ポリゴン検索 API](/rest/api/maps/search/getsearchpolygon) に渡します。

### <a name="host-and-query-spatial-business-data"></a>空間ビジネス データのホストおよび照会

Bing 地図の Spatial Data Services では、ビジネス データをホストし空間 REST サービスとして公開できる、シンプルな空間データ ストレージ ソリューションが提供されます。 このサービスでは、プロパティ指定検索、近隣検索、境界ボックス内検索、1 マイル ルート検索の 4 つの主なクエリを利用できます。 通常、このサービスの利用企業の多くは、他のデータベースにデータを格納してその一部をこのサービスにアップロードすることで、店舗検索ツールのようなアプリケーションを実現しています。 キーベースの認証による基本的なセキュリティが提供されるため、このサービスは、公開データのみに使用することが推奨されています。

最初、ビジネスの位置情報データはデータベース内に保存されていることがほとんどです。 そのため、Azure SQL や Azure PostgreSQL (PostGIS プラグインを使用) などの Azure ストレージ ソリューションを使用することをお勧めします。 これらのストレージ ソリューションはともに空間データに対応しており、豊富な空間クエリ機能が用意されています。 データを適切なストレージ ソリューションに保存した後で、カスタムの Web サービスを作成するか、ASP.NET や Entity Framework などのフレームワークを使用することにより、データをアプリケーションに統合できます。 この方法では、より多くのクエリ機能が利用できるだけでなく、高度なセキュリティ オプションを使用可能です。

また、Azure Cosmos DB には限定的な空間機能が用意されており、シナリオによってはこれで十分な場合があります。

以下に、Azure で空間データをホストおよび照会する場合に役立つリソースをいくつか紹介します。

-   [Azure SQL の空間データ型の概要](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL の空間機能 - ニアレストネイバー クエリ](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Azure Cosmos DB の地理空間機能の概要](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>クライアント ライブラリ

Azure Maps には、次のプログラミング言語用のクライアント ライブラリが用意されています。

-   JavaScript、TypeScript、Node.js - [ドキュメント](./how-to-use-services-module.md) \| [NPM パッケージ](https://www.npmjs.com/package/azure-maps-rest)

その他のプログラミング言語用のオープンソース クライアント ライブラリには、次のものがあります。

* .NET Standard 2.0 - [GitHub プロジェクト](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet パッケージ](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップすべきリソースはありません。

## <a name="next-steps"></a>次のステップ

Azure Maps REST サービスの詳細について学習します。

> [!div class="nextstepaction"]
> [検索サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)
