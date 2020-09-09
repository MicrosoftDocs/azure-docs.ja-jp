---
title: チュートリアル:Google マップから Web サービスを移行する | Microsoft Azure Maps
description: Google マップから Microsoft Azure Maps に Web サービスを移行する方法。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 934a7546464cf552c355ee6b4e278b79a0f9ff90
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747495"
---
# <a name="migrate-web-service-from-google-maps"></a>Google マップから Web サービスを移行する

Azure Maps と Google マップでは、どちらの場合も REST Web サービスを介して空間 API にアクセスすることができます。 これらのプラットフォームの API インターフェイスは同様の機能を実行します。 ただし、使用される名前付け規則と応答オブジェクトが異なります。

次の表に、記載されている Google マップ サービス API と同様の機能を持つ Azure Maps サービス API を示します。

| Google マップ サービス API | Azure Maps サービス API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 道順              | [Route](https://docs.microsoft.com/rest/api/maps/route)                                     |
| 距離行列         | [ルート マトリックス](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| ジオコーディング               | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 場所の検索           | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 場所のオートコンプリート      | [Search](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Snap to Road            | 「[ルートと道順を計算する](#calculate-routes-and-directions)」セクションを参照してください。            |
| Speed Limits            | 「[座標の逆ジオコーディング](#reverse-geocode-a-coordinate)」セクションを参照してください。                  |
| 静的マップ              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| タイム ゾーン               | [タイム ゾーン](https://docs.microsoft.com/rest/api/maps/timezone)                              |

次のサービス API は、Azure Maps では現在使用できません。

- Elevation
- 地理的位置情報
- 場所の詳細と写真。電話番号と Web サイトの URL は、Azure Maps Search API で利用できます。
- Map URL
- Nearest Roads - Web SDK を使用して実現できますが ([こちら](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)を参照)、現在サービスとしては提供されていません。
- 静的ストリート ビュー

Azure Maps には、必要になる可能性のある追加の REST Web サービスがいくつか用意されています。

- [空間演算](https://docs.microsoft.com/rest/api/maps/spatial): ジオフェンシングなどの複雑な空間計算や空間演算をサービスにオフロードします。
- [交通情報](https://docs.microsoft.com/rest/api/maps/traffic): 交通流量や事故に関するリアルタイム データにアクセスできます。

## <a name="geocoding-addresses"></a>住所のジオコーディング

ジオコーディングは、住所を座標に変換するプロセスです。 たとえば、"1 Microsoft way, Redmond, WA" は、経度: -122.1298、緯度: 47.64005 に変換されます。 その後、マーカーをマップの中心に配置するなど、さまざまな目的に座標を使用できます。

Azure Maps により、住所をジオコーディングするための複数の方法が提供されます。

- [**自由形式の住所のジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): 1 行の住所文字列を指定します。要求は直ちに処理されます。 たとえば、"1 Microsoft way, Redmond, WA" は 1 行の住所文字列に該当します。 この API は、個々の住所を迅速にジオコーディングする必要がある場合に推奨されます。
- [**構造化された住所のジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): 単一の住所の一部 (番地、市区町村、国または地域、郵便番号など) を指定します。要求は直ちに処理されます。 この API は、個々の住所を迅速にジオコーディングする必要があり、データが既に個別の住所の一部として解析されている場合に推奨されます。
- [**住所のバッチ ジオコーディング**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): 最大 10,000 個の住所を含む要求を作成します。これらの住所は一定期間内に処理されます。 すべての住所はサーバーで並行してジオコーディングされ、完了すると、完全な結果セットをダウンロードできます。 これは、大きなデータ セットをジオコーディングする場合に推奨されます。
- [**あいまい検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 この API は、自由形式の文字列を受け入れます。 住所、場所、ランドマーク、目的地、または目的地のカテゴリをこの文字列として指定できます。 要求は、この API によってほぼリアルタイムで処理されます。 この API は、ユーザーが同じテキスト ボックスで住所や目的地を検索するアプリケーションに適しています。
- [**あいまいバッチ検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。これらは一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps 内の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` および `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 市区町村<br/>`municipalitySubdivision` - 地域、地区<br/>`countrySubdivision` - 都道府県<br/>`countrySecondarySubdivision` - 郡<br/>`countryTertiarySubdivision` - 区<br/>`countryCode` -2 文字の国または地域コード |
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `region`                    | `countrySet`                       |

検索サービスの使用方法の例については、[こちら](how-to-search-for-address.md)を参照してください。 [検索のベスト プラクティス](how-to-use-best-practices-for-search.md)をぜひご覧ください。

> [!TIP]
> 自由形式のアドレスのジオコーディング API とあいまい検索 API は、`&typeahead=true` を要求 URL に追加することにより、オートコンプリート モードで使用できます。 これにより、入力テキストが完全ではない可能性がサーバーに通知され、検索は予測モードになります。

## <a name="reverse-geocode-a-coordinate"></a>座標の逆ジオコーディング

逆ジオコーディングは、地理座標をおおよその住所に変換するプロセスです。 "経度: -122.1298、緯度: 47.64005" という座標は、"1 Microsoft way, Redmond, WA" に変換されます。

Azure Maps では、複数の逆ジオコーディング メソッドが提供されています。

- [**住所の逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): 1 つの地理座標を指定すると、その座標に対応するおおよその住所を取得できます。 要求は、ほぼリアルタイムで処理されます。
- [**交差点の逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): 1 つの地理座標を指定すると周辺の交差点情報を取得できます。要求は直ちに処理されます。 たとえば、次の交差点の 1st Ave や Main St. が返されます。
- [**住所のバッチ逆ジオコーダー**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): 最大 10,000 個の座標を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並列処理されます。 要求が完了したら、すべての結果をまとめてダウンロードできます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター   | 相当する Azure Maps API パラメーター   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

[検索のベスト プラクティス](how-to-use-best-practices-for-search.md)を参照してください。

Azure Maps の逆ジオコーディング API には、Google マップでは利用できない追加機能がいくつかあります。 ご自分のアプリケーションを移行する際に、それらの機能をアプリに統合することもできます。

- 速度制限のデータを取得する
- 道路使用情報を取得する (地方の道路、幹線道路、自動車専用道路、ランプなど)
- 座標が通りのどちら側に位置するかを取得する

## <a name="search-for-points-of-interest"></a>目的地を検索する

Google マップで目的地データを検索するには、場所検索 API を使用します。 この API により、目的地を検索するための 3 つの異なる方法が提供されます。

- **テキストから場所を検索する:** 目的地を名前、住所、または電話番号に基づいて検索します。
- **近隣検索**: ある場所から特定の距離内にある目的地を検索します。
- **テキスト検索:** 目的地や場所の情報を含む自由形式のテキストを使用して場所を検索します。 たとえば、"ニューヨークのピザ" や "メイン ストリート付近のレストラン" などです。

Azure Maps には、目的地を検索するための複数の検索 API が用意されています。

- [**POI 検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): 目的地を名前で検索します。 たとえば、"スターバックス" などです。
- [**POI カテゴリ検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): 目的地をカテゴリで検索します。 たとえば、"レストラン" などです。
- [**近隣検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): ある場所から特定の距離内にある目的地を検索します。
- [**あいまい検索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): この API は、住所のジオコーディングと目的地検索を組み合わせたものです。 この API では、住所、場所、ランドマーク、目的地、または目的地のカテゴリを自由形式の文字列として指定することができます。 要求は、ほぼリアルタイムで処理されます。 この API は、ユーザーが同じテキスト ボックスで住所や目的地を検索するアプリケーションに適しています。
- [**ジオメトリ内検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): 指定されたジオメトリ内にある目的地を検索します。 たとえば、多角形の範囲内の目的地を検索します。
- [**ルートに沿った検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): 指定されたルートに沿って目的地を検索します。
- [**あいまいバッチ検索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): 最大 10,000 個の住所、場所、ランドマーク、または目的地を含む要求を作成します。 要求が処理されるまでには、ある程度の時間がかかります。 すべてのデータはサーバーで並列処理されます。 要求の処理が完了したら、すべての結果をまとめてダウンロードできます。

現在、Azure Maps には、Google マップのテキスト検索 API に相当する API はありません。

> [!TIP]
> POI 検索、POI カテゴリ検索、あいまい検索の各 API は、`&typeahead=true` を要求 URL に追加することにより、オートコンプリート モードで使用できます。 これにより、入力テキストが完全ではない可能性がサーバーに通知されます。API によって検索が予測モードで実行されます。

[検索のベスト プラクティス](how-to-use-best-practices-for-search.md)に関するドキュメントをご覧ください。

### <a name="find-place-from-text"></a>テキストから場所を検索する

目的地を名前または住所で検索するには、Azure Maps の [POI 検索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)および[あいまい検索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)を使用します。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `locationbias`            | `lat`、`lon`、`radius`<br/>`topLeft` および `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>近隣検索

Azure Maps で付近の目的地を取得するには、[近隣検索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API を使用します。

次の表は、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `keyword`                   | `categorySet` および `brandSet`        |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `location`                  | `lat` および `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` および `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` および `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –`[サポートされている検索カテゴリ](supported-search-categories.md)に関するドキュメントを参照してください。   |

## <a name="calculate-routes-and-directions"></a>ルートと道順を計算する

Azure Maps を使用してルートと道順を計算します。 Azure Maps には、Google マップの経路探索サービスと同じ機能が数多く用意されています。たとえば、次のようなものがあります。

- 到着および出発の時刻。
- リアルタイムおよび予測ベースの交通路。
- さまざまな移動手段 (乗用車、徒歩、自転車など)。

> [!NOTE]
> Azure Maps では、すべてのウェイポイントを座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

Azure Maps のルート指定サービスでは、ルート指定の計算用として次の API が提供されます。

- [**ルート計算**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): ルートが計算されます。要求はただちに処理されます。 この API では、GET 要求と POST 要求の両方がサポートされます。 大量のウェイポイントを指定する場合、または多くのルート オプションを使用する場合は、URL 要求が長くなりすぎて問題が発生することがないように POST 要求が推奨されます。 Azure Maps の POST Route Direction には、数千の[サポート ポイント](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)を受け取り、それらを使用してポイント間の論理ルート パスを再作成するオプションがあります (Snap to Road)。 
- [**ルートのバッチ処理**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): 最大 1,000 個のルート要求を含む要求が作成されます。これらの座標は一定期間内に処理されます。 すべてのデータはサーバーで並行して処理され、完了すると、完全な結果セットをダウンロードすることができます。
- [**モビリティ サービス**](https://docs.microsoft.com/rest/api/maps/mobility): 公共輸送を使用するルートと道順が計算されます。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター    | 相当する Azure Maps API パラメーター  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` - `"lat0,lon0:lat1,lon1…."` 形式の座標  |
| `key`                          | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                     | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* - これはジオコーディング関連の機能です。 Azure Maps ジオコーディング API を使用する場合は、*countrySet* パラメーターを使用します。  |
| `traffic_model`               | *N/A* - 交通情報データを *traffic* パラメーターで使用する必要がある場合のみ指定できます。 |
| `transit_mode`                | [モビリティ サービスのドキュメント](https://docs.microsoft.com/rest/api/maps/mobility)を参照してください。 |
| `transit_routing_preference` | [モビリティ サービスのドキュメント](https://docs.microsoft.com/rest/api/maps/mobility)を参照してください。 |
| `units`                        | *N/A* - Azure Maps ではメートル法のみが使用されます。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Azure Maps ルート API から返されるのは、既定では概要だけです。 距離と時間、ルートの座標が返されます。 道案内の指示を取得するには、`instructionsType` パラメーターを使用します。 また、概要とルートをフィルター処理するには、`routeRepresentation` パラメーターを使用します。

Azure Maps のルート指定 API には、Google マップでは利用できない追加の機能があります。 ご自分のアプリを移行する際は、これらの機能の使用を検討してください。有効活用できる可能性があります。

- ルートの種類のサポート: 最短、最速、スリリング、最も燃費が良い。
- 追加の移動モードのサポート: バス、バイク、タクシー、トラック、ライトバン。
- 150 個のウェイポイントのサポート。
- 1 つの要求で複数の移動時間を計算: 過去の交通量、現在の交通量、交通量なし。
- 追加の道路の種類を回避: 自動車の相乗り用車線、未舗装道路、既に使用したことがある道路。
- 回避するカスタム領域を指定。
- ルートで上れる標高を制限。
- エンジンの仕様に基づくルート。 エンジンの仕様と、燃料または充電の残量とに基づいて内燃自動車または電気自動車のルートを計算。
- 商用車両のルート パラメーターをサポート (車両の寸法、重量、アクセル回数、積み荷の種類など)。
- 車両の最大速度を指定。

これに加えて、Azure Maps のルート サービスでは、[ルーティング可能な範囲の計算](https://docs.microsoft.com/rest/api/maps/route/getrouterange)もサポートされます。 ルーティング可能な範囲の計算は、等時間マップとも呼ばれます。 その際、出発地点から任意の方向に移動できる領域をカバーする多角形が生成されます。 そのすべては、指定された時間内または燃料や充電の残量内で行われます。

[ルート指定のベスト プラクティス](how-to-use-best-practices-for-routing.md)に関するドキュメントをご覧ください。

## <a name="retrieve-a-map-image"></a>マップ イメージを取得する

Azure Maps には、静的マップ イメージにデータを重ねてレンダリングするための API が用意されています。 Azure Maps の[マップ画像のレンダリング](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API は、Google マップの静的マップ API に相当します。

> [!NOTE]
> Azure Maps では、中央、すべてのマーカー、経路の位置を "経度,緯度" 形式の座標にする必要があります。 一方、Google マップでは、"緯度,経度" 形式が使用されます。 住所は、最初にジオコーディングする必要があります。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` - URL パスの一部として指定されます。 現時点では、PNG のみがサポートされています。 |
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `maptype`                   | `layer` および `style` - [サポートされているマップ スタイル](supported-map-styles.md)に関するドキュメントを参照してください。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* - これはジオコーディング関連の機能です。 Azure Maps のジオコーディング API を使用する場合は、`countrySet` パラメーターを使用します。  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` および `height` - 最大 8192 x 8192 のサイズを指定できます。 |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps のタイル システムでは、Google マップで使用されるマップ タイルの 2 倍のサイズが使用されます。 このため、Azure Maps のズーム レベル値では、Google マップと比べて 1 つ上のズーム レベルが表示されます。 この違いを相殺するため、移行する要求では、ズーム レベルを 1 つ下げてください。

詳細については、[マップ イメージのレンダリング API に関する攻略ガイド](how-to-render-custom-data.md)を参照してください。

Azure Maps のレンダリング サービスでは、静的マップ画像を生成できるだけでなく、ラスター (PNG) およびベクター形式のマップ タイルに直接アクセスすることもできます。

- [**マップ タイル**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): ベース マップ (道路、境界、背景) のラスター (PNG) タイルとベクター タイルを取得します。
- [**マップ イメージ タイル**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): 航空写真と衛星画像のタイルを取得します。

> [!TIP]
> 多くの Google マップ アプリケーションでは、数年前に対話型のマップ エクスペリエンスから静的マップ画像に切り替えられました。 これは、コスト削減の手段として行われたものです。 Azure Maps では、多くの場合、Web SDK の対話型マップ コントロールを使用する方がはるかに費用対効果が高くなります。 対話型マップ コントロールは、タイルの読み込み回数に基づいて課金されます。 Azure Maps では、サイズの大きいマップ タイルが使用されます。 多くの場合、数個のタイルだけで静的マップと同じマップ ビューを再作成することができます。 マップ タイルはブラウザーによって自動的にキャッシュされます。 このため、対話型のマップ コントロールでは、静的マップ ビューを再作成する際、トランザクションの一部を生成するだけで済むことがほとんどです。 パンおよびズームでは読み込まれるタイルは増えますが、マップ コントロール内にこの動作を無効にするオプションがあります。 対話型マップ コントロールには、静的マップ サービスで提供される以上の視覚化オプションも用意されています。

### <a name="marker-url-parameter-format-comparison"></a>マーカー URL パラメーターの形式の比較

**移行前:Google Maps**

マーカーは、URL の `markers` パラメーターを使用して追加します。 `markers` パラメーターには、次に示すように、スタイルと、そのスタイルを使用してマップにレンダリングされる場所の一覧が含まれます。

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

さらにスタイルを追加するには、URL に `markers` パラメーターを使用して、別のスタイルや場所のセットを指定します。

マーカーの場所は、"緯度,経度" の形式で指定します。

マーカーのスタイルを追加するには、`optionName:value` 形式を使用します。複数のスタイルを指定する場合は、"optionName1:value1\|optionName2:value2" のようにパイプ (\|) 文字で区切ります。 オプションの名前と値はコロン (:) で区切ることに注意してください。 Google マップでマーカーのスタイルを設定するには、次の名前のスタイル オプションを使用します。

- `color` - 既定のマーカー アイコンの色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、または値 `black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white` のいずれかを指定できます。
- `label` - アイコンの上に表示する 1 文字の大文字の英数字。
- `size` - マーカーのサイズ。 `tiny`、`mid`、または `small` を指定できます。

Google マップのカスタム アイコンには、次のスタイル オプション名を使用します。

- `anchor` - アイコン イメージを座標に合わせて配置する方法を指定します。 ピクセル (x,y) 値、または値 `top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft`、`bottomright` のいずれかを指定できます。
- `icon` - アイコン イメージを指す URL。

たとえば、マップの経度: -110、緯度: 45 の位置に、赤色で中サイズのマーカーを追加してみましょう。

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps のマーカー](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**後: Azure Maps**

静的マップ画像に対するマーカーは、URL で `pins` パラメーターを指定することによって追加します。 Google マップと同様、このパラメーターでスタイルと場所の一覧を指定します。 異なるスタイルのマーカーをサポートするために、`pins` パラメーターを複数回指定することができます。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

さらに別のスタイルを使用するには、URL に `pins` パラメーターを追加して、別のスタイルや場所のセットを指定します。

Azure Maps では、ピンの位置を "経度 緯度" 形式で指定する必要があります。 Google マップでは、"緯度,経度" 形式が使用されます。 Azure Maps 形式では、経度と緯度をコンマではなくスペースで区切ることにも注意してください。

作成するピンの種類は、`iconType` で指定します。 次のいずれかの値になります。

- `default` - 既定のピン アイコン。
- `none` - アイコンは表示されず、ラベルのみがレンダリングされます。
- `custom` - カスタム アイコンを使用することを指定します。 アイコン イメージを指す URL は、ピンの位置情報の後、`pins` パラメーターの末尾に追加できます。
- `{udid}` - Azure Maps データ ストレージ プラットフォームに格納されるアイコンの固有データ ID (UDID)。

ピンのスタイルは、`optionNameValue` 形式で追加します。 複数のスタイルは、パイプ (\|) 文字で区切ります。 (例: `iconType|optionName1Value1|optionName2Value2`)。 オプションの名前と値との間に区切りはありません。 マーカーにスタイルを設定するには、次のスタイル オプション名を使用します。

- `al` - マーカーの不透明度 (アルファ) を指定します。 0 から 1 までの数値を選択します。
- `an` - ピンのアンカーを指定します。 X と y のピクセル値を "x y" 形式で指定します。
- `co` - ピンの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定します。
- `la` - ラベルのアンカーを指定します。 X と y のピクセル値を "x y" 形式で指定します。
- `lc` - ラベルの色。 24 ビットの 16 進カラー値 (`000000` から `FFFFFF`) を指定します。
- `ls` - ラベルのサイズ (ピクセル単位)。 0 より大きい数値を選択します。
- `ro` - アイコンを回転させる角度の値。 -360 から 360 までの数値を選択します。
- `sc` - ピン アイコンのスケール値。 0 より大きい数値を選択します。

それぞれのピン位置に使用するラベルの値を指定します。 このアプローチの方が、場所の一覧内にあるすべてのマーカーに単一のラベル値を適用するよりも効率的です。 ラベル値には、複数の文字で構成される文字列を指定することができます。 また、誤ってスタイルや場所の値として解釈されないようにするため、その文字列を一重引用符で囲むこともできます。

赤色 (`FF0000`) の既定のアイコンを追加し、その下 (15 50) に "Space Needle" というラベルを配置しましょう。 アイコンの位置は、経度: -122.349300、緯度: 47.620180 です。

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps のマーカー](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

ラベル値が "1"、"2"、"3" の 3 つのピンを追加します。

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps の複数のマーカー](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>経路 URL パラメーターの形式の比較

**移行前:Google Maps**

静的マップ画像に対する線と多角形は、URL に `path` パラメーターを使用して追加します。 `path` パラメーターは、次に示すように、スタイルと、マップにレンダリングされる場所の一覧を受け取ります。

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

さらに別のスタイルを使用するには、URL に `path` パラメーターを追加して、別のスタイルや場所のセットを指定します。

経路の位置は、`latitude1,longitude1|latitude2,longitude2|…` 形式で指定します。 経路は、エンコードすることも、地点の住所を含めることもできます。

経路のスタイルは、`optionName:value` 形式で追加します。複数のスタイルを指定する場合は、パイプ (\|) 文字で区切ります。 さらに、オプションの名前と値はコロン (:) で区切ります。 たとえば「`optionName1:value1|optionName2:value2`」と入力します。 Google マップで経路のスタイルを設定するには、次のスタイル オプション名を使用します。

- `color` - 経路またはポリゴンの枠線の色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、32 ビットの 16 進カラー値 (`0xrrggbbbaa`)、または値 black、brown、green、purple、yellow、blue、gray、orange、red、white のいずれかを指定できます。
- `fillColor` - 経路領域 (ポリゴン) を塗りつぶす色。 24 ビットの 16 進カラー値 (`0xrrggbb`)、32 ビットの 16 進カラー値 (`0xrrggbbbaa`)、または値 black、brown、green、purple、yellow、blue、gray、orange、red、white のいずれかを指定できます。
- `geodesic` - 経路を地球の曲線に沿った線にする必要があるかどうかを示します。
- `weight` - 経路の線の太さ (ピクセル単位)。

マップ上の座標間に、赤色の線の不透明度と太さ (ピクセル) を URL パラメーターで追加します。 次の例では、線の不透明度は 50% で、太さは 4 ピクセルです。 座標は経度: -110、緯度: 座標 (経度: -100、緯度: 50) の間に追加するには、50 を使用しています。

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps のポリライン](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**後: Azure Maps**

静的マップ画像に対する線と多角形は、URL に `path` パラメーターを指定して追加します。 Google マップと同様、このパラメーターでスタイルと場所の一覧を指定します。 異なるスタイルの複数の円、線、多角形をレンダリングするには、`path` パラメーターを複数回指定します。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

経路の位置については、Azure Maps の場合、座標を "経度 緯度" 形式にする必要があります。 Google マップでは、"緯度,経度" 形式が使用されます。 Azure Maps 形式では、経度と緯度をコンマではなくスペースで区切ることにも注意してください。 Azure Maps では、エンコードされた経路または地点の住所はサポートされません。 大きなデータ セットは、GeoJSON ファイルとして Azure Maps データ ストレージ API に読み込みます。[こちら](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)を参照してください。

経路のスタイルは、`optionNameValue` 形式で追加します。 複数のスタイルは、パイプ (\|) 文字で区切ります (例: `optionName1Value1|optionName2Value2`)。 オプションの名前と値との間に区切りはありません。 Azure Maps で経路のスタイルを設定するには、次のスタイル オプション名を使用します。

- `fa` - ポリゴンをレンダリングする際に使用する塗りつぶしの色の不透明度 (アルファ)。 0 から 1 までの数値を選択します。
- `fc` - ポリゴンの領域をレンダリングするために使用される塗りつぶしの色。
- `la` - 線およびポリゴンの枠線をレンダリングする際に使用される線の色の不透明度 (アルファ)。 0 から 1 までの数値を選択します。
- `lc` - 線およびポリゴンの枠線をレンダリングするために使用される線の色。
- `lw` - 線の幅 (ピクセル単位)。
- `ra` - 円の半径をメートル単位で指定します。

座標間に、赤色の線の不透明度と太さ (ピクセル) を URL パラメーターで追加します。 次の例では、線の不透明度は 50% で、太さは 4 ピクセルです。 座標は、経度: -110、座標 (経度: -100、緯度: 50) の間に追加するには、50 を使用しています。

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps のポリライン](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>距離行列を計算する

Azure Maps には、距離行列 API が用意されています。 場所のセット間の移動時間と移動距離を距離行列を使用して計算するには、この API を使用します。 これは、Google マップの距離行列 API に相当します。

- [**ルート マトリックス**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): 出発地と目的地のセットについて移動時間と移動距離が非同期で計算されます。 要求あたり最大 700 個のセルがサポートされます。 これは出発地の数 × 目的地の数です。 この制限を念頭に置いて考えられるマトリックス ディメンションの例としては、700x1、50x10、10x10、28x25、10x70 などがあります。

> [!NOTE]
> 距離行列 API に対する要求は、要求の本文で出発地と目的地の情報を指定した POST 要求を使用してのみ作成できます。 さらに、Azure Maps では、すべての出発地および目的地を座標にする必要があります。 住所は、最初にジオコーディングする必要があります。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター      | 相当する Azure Maps API パラメーター  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` - POST 要求の本文で GeoJSON として指定します。 |
| `key`                          | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。 |
| `language`                     | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` - POST 要求の本文で GeoJSON として指定します。  |
| `region`                       | *N/A* - これはジオコーディング関連の機能です。 Azure Maps のジオコーディング API を使用する場合は、`countrySet` パラメーターを使用します。 |
| `traffic_model`                | *N/A* - 交通情報データを `traffic` パラメーターで使用する必要がある場合のみ指定できます。 |
| `transit_mode`                 | *N/A* - 輸送ベースの距離マトリックスは現在サポートされていません。  |
| `transit_routing_preference`   | *N/A* - 輸送ベースの距離マトリックスは現在サポートされていません。  |
| `units`                        | *N/A* - Azure Maps ではメートル法のみが使用されます。 |

> [!TIP]
> Azure Maps のルート指定 API で使用できる高度なルート指定オプションはすべて、Azure Maps 距離行列 API でサポートされます。 高度なルート指定オプションには、トラック ルート指定、エンジン仕様などがあります。

[ルート指定のベスト プラクティス](how-to-use-best-practices-for-routing.md)に関するドキュメントをご覧ください。

## <a name="get-a-time-zone"></a>タイム ゾーンを取得する

Azure Maps には、座標のタイム ゾーンを取得するための API が用意されています。 Azure Maps のタイム ゾーン API は、Google マップのタイム ゾーン API に相当します。

- [**座標によるタイム ゾーン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): 座標を指定して、その座標のタイム ゾーンの詳細を取得します。

次の表では、Google マップ API パラメーターと、それに相当する Azure Maps の API パラメーターを相互参照で示しています。

| Google マップ API パラメーター | 相当する Azure Maps API パラメーター   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` - ドキュメント「[Azure Maps による認証](azure-maps-authentication.md)」も参照してください。       |
| `language`                  | `language` - [サポートされている言語](supported-languages.md)に関するドキュメントも参照してください。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

この API 以外にも、Azure Maps には、多数のタイム ゾーン API が用意されています。 これらの API は、タイム ゾーンの名前または ID に基づいて時刻を変換します。

- [**ID によるタイム ゾーン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): 指定された IANA タイム ゾーン ID について現在、履歴、将来のタイム ゾーンに関する情報が返されます。
- [**タイム ゾーン列挙型 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): IANA タイム ゾーン ID の完全な一覧が返されます。 IANA サービスの更新は、1 日以内にシステムに反映されます。
- [**タイム ゾーン列挙型 Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Windows タイム ゾーン ID の完全な一覧が返されます。
- [**タイム ゾーン IANA バージョン**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Azure Maps で現在使用されている IANA のバージョン番号が返されます。
- [**タイム ゾーン Windows から IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): 有効な Windows タイム ゾーン ID が指定された場合、対応する IANA ID が返されます。 1 つの Windows ID に対して複数の IANA ID が返される場合があります。

## <a name="client-libraries"></a>クライアント ライブラリ

Azure Maps には、次のプログラミング言語用のクライアント ライブラリが用意されています。

- JavaScript、TypeScript、Node.js - [ドキュメント](how-to-use-services-module.md) \| [NPM パッケージ](https://www.npmjs.com/package/azure-maps-rest)

これらのオープンソース クライアント ライブラリは、その他のプログラミング言語用です。

- .NET Standard 2.0 - [GitHub プロジェクト](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet パッケージ](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>その他のリソース

Azure Maps REST サービスに関するその他のドキュメントとリソースを次に示します。

- [検索のベスト プラクティス](how-to-use-best-practices-for-search.md)
- [住所の検索](how-to-search-for-address.md)
- [ルート指定のベスト プラクティス](how-to-use-best-practices-for-routing.md)
- [Azure Maps REST サービス API のリファレンス ドキュメント](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>次のステップ

Azure Maps REST サービスの詳細について学習します。

> [!div class="nextstepaction"]
> [検索サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [ルート指定サービスを使用するためのベスト プラクティス](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [サービス モジュール (Web SDK) の使用方法](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)
