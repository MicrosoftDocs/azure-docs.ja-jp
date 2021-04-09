---
title: Microsoft Azure Maps の概要
description: Microsoft Azure Maps のサービスと機能、およびそれらをアプリケーションで使用する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: b53af0fda8bb24bf03d0e02f61ce7d195ae69503
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680817"
---
# <a name="what-is-azure-maps"></a>Azure Maps とは

Azure Maps は、最新のマッピング データを使用して、地理的なコンテキストを Web とモバイル アプリケーションに提供する一連の地理空間サービスおよび SDK です。 Azure Maps は以下を提供します。

* 複数のスタイルと衛星画像でベクター マップおよびラスター マップをレンダリングするための REST API。
* プライベートな屋内マップ データに基づいてマップを作成およびレンダリングするための Creator サービス (プレビュー)。
* 世界中の住所、場所、および目的地を見つけるための検索サービス。
* ポイント間、マルチポイント、マルチポイント最適化、等時線、電気自動車、商用車、影響を受ける交通、マトリックス ルーティングなど、さまざまなルーティングのオプション。
* リアルタイムの交通情報を必要とするアプリケーション向けのトラフィック フロー ビューとインシデント ビュー。
* 公共の輸送情報を要求する Mobility Service (プレビュー)。さまざまな移動手段とリアルタイムの到着を組み合わせて、ルートを計画します。
* タイム ゾーンおよび位置情報 (プレビュー) サービス。
* デジタル海抜モデルを使用した Elevation Service (プレビュー)
* Azure 内でホストされている位置情報を使用した、ジオフェンシング サービスとマッピング データ ストレージ。
* 地理空間分析による位置情報インテリジェンス。

加えて、Azure Maps サービスは、Web SDK および Android SDK を介して使用できます。 これらのツールは、開発者が、位置情報を Azure ソリューションに統合するソリューションの開発とスケーリングを迅速に行うのに役立ちます。

無料の [Azure Maps アカウント](https://azure.microsoft.com/services/azure-maps/)にサインアップして、開発を始めることができます。

次の動画では、Azure Maps について詳しく説明しています。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>マップ コントロール

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK では、独自のコンテンツや映像を使用して、インタラクティブ マップをカスタマイズすることができます。 この対話型マップは、Web とモバイル アプリケーションの両方に使用できます。 このマップ コントロールには、WebGL が利用されているため、大きなデータ セットをハイ パフォーマンスでレンダリングすることができます。 SDK で JavaScript または TypeScript を使用して開発できます。

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Azure Maps Web SDK を使用して作成された人口変動のマップの例":::

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK は、モバイル マッピング アプリケーションの作成に使用します。

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="モバイル デバイス上のマップの例":::

## <a name="services-in-azure-maps"></a>Azure Maps のサービス

Azure Maps は、Azure アプリケーションに地理的なコンテキストを提供できる以下のサービスで構成されます。

### <a name="data-service-preview"></a>Data Service (プレビュー)

データはマップに不可欠です。 Data Service は、空間演算や画像合成で使用する地理空間データをアップロードおよび格納するために使用します。  お客様のデータを Azure Maps サービスに近づけると、待ち時間が減少し、生産性が上がり、ご自分のアプリケーションで新しいシナリオが作成されます。 このサービスの詳細については、[Data Service のドキュメント](/rest/api/maps/data)を参照してください。

### <a name="geolocation-service-preview"></a>Geolocation Service (プレビュー)

Geolocation Service は、IP アドレスに関して取得した 2 文字の国および地域コードをプレビューするために使用します。 このサービスは、地理的な場所に基づいてカスタマイズされたアプリケーション コンテンツを提供することで、ユーザー エクスペリエンスを強化するのに役立てることができます。

詳細については、[Geolocation Service のドキュメント](/rest/api/maps/geolocation)を参照してください。

### <a name="mobility-services-preview"></a>Mobility Service (プレビュー) 

Azure Maps Mobility Service では、輸送経路を指定したり、公共輸送機関の最寄りの停留所を検索したりするなど、公共輸送機関機能を利用できるため、アプリケーションの開発時間が短縮されます。 ユーザーは、輸送の停留所、路線、スケジュールに関する詳細情報を取得できます。 Mobility Service では、ユーザーは停留所や路線の配置、停留所、路線、サービス エリアのアラート、公共輸送機関のリアルタイム到着アラートやサービス アラートを取得することもできます。 さらに、Mobility Service は、マルチモーダルな旅行計画オプションでルーティング機能を提供します。 マルチモードの旅行計画では、徒歩、自転車、公共輸送機関の選択肢がすべて 1 つの旅行に組み込まれます。 ユーザーはまた、段階式で詳しいマルチモードの旅程にアクセスできます。

このサービスの詳細については、[Mobility Service のドキュメント](/rest/api/maps/mobility)を参照してください。

### <a name="render-service"></a>Render Service

[Render Service V2 (プレビュー)](/rest/api/maps/renderv2) では、新しいバージョンの [Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview) が導入されています。 Get Map Tile V2 API では、Azure Maps Creator を使用して作成された Azure Maps の道路タイル、気象タイル、またはマップ タイルを要求できるようになりました。 新しい Get Map Tile V2 API を使用することをお勧めします。  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Render Service V2 からのマップの例":::

詳細については、[Render Service V2 のドキュメント](/rest/api/maps/renderv2)を参照してください。

GA (一般提供) 段階にある Render Service V1 の詳細については、[Render Service V1 のドキュメント](/rest/api/maps/render)を参照してください。  

### <a name="route-service"></a>Route Service

Route Service は、要求された各ルートの推定到着時間 (ETA) を計算するために使用できます。 Route API は、リアルタイムの交通情報や履歴交通データ (たとえば要求された曜日や時間帯の通常の道路速度) などの要因を考慮します。 API は、時間または距離に基づいて、複数の到着地に対して順番に、または最適化された順序で、利用できる最短または最速のルートを返します。 このサービスを使用すると、開発者は、自動車、トラック、自転車、徒歩、電気自動車などのいくつかの移動手段にわたる道順を計算できます。 このサービスでは、出発時刻、重量制限、危険物輸送などの入力も考慮されます。

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Route Service からのマップの例":::

Route Service では、以下のような一連の高度な機能が提供されます。

* 複数のルート要求のバッチ処理。
* 一連の出発点と目的地の間の移動時間と距離の行列。
* 時間または燃料の要件に基づいてユーザーが移動可能なルートまたは距離の検出。

ルーティング機能の詳細については、[Route Service のドキュメント](/rest/api/maps/route)を参照してください。

### <a name="search-service"></a>検索サービス

Search Service は、開発者が住所、場所、名前またはカテゴリ別の事業所一覧、およびその他の地理情報を検索するのに役立ちます。 また、緯度と経度に基づいて住所や交差点の[逆引き地理コード](https://en.wikipedia.org/wiki/Reverse_geocoding)を行うことができます。

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="マップ上での検索の例":::

Search Service では、次のような高度な機能も提供されます。

* ルートに沿った検索。
* より広い範囲内での検索。
* 複数の検索要求のバッチ処理。
* ブランド名による電気自動車の充電ステーションと目的地 (POI) データの検索。

検索機能の詳細については、[Search Service のドキュメント](/rest/api/maps/search)を参照してください。

### <a name="spatial-service"></a>空間サービス

空間サービスでは、位置情報をすばやく分析し、その時間と空間で発生している進行中のイベントをお客様にお知らせします。 ほぼリアルタイムの分析とイベントの予測モデリングを可能にします。

このサービスでは、お客様が、一般的な地理空間数学計算のライブラリを使用して、位置情報のインテリジェンスを強化できます。 一般的な計算の例としては、最接近点、大圏距離、バッファーが挙げられます。 このサービスとそのさまざまな機能の詳細については、[空間サービスのドキュメント](/rest/api/maps/spatial)を参照してください。

### <a name="timezone-service"></a>タイムゾーン サービス

Time Zone Service では、現在、過去、未来のタイム ゾーン情報のクエリを実行できます。 入力として、緯度と経度のペアを使用するか、[IANA ID](https://www.iana.org/) を使用することができます。 Time Zone Service では、次のこともできます。

* Windows タイム ゾーン ID を IANA タイム ゾーンに変換する。
* UTC に対するタイム ゾーン オフセットをフェッチする。
* 選択したタイム ゾーンにおける現在の時刻を取得する。

Time Zone Service に対するクエリの一般的な JSON 応答は、次のサンプルのようになります。

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

このサービスの詳細については、[Time Zone Service のドキュメント](/rest/api/maps/timezone)を参照してください。

### <a name="traffic-service"></a>Traffic Service

Traffic Service は、交通情報を必要とする Web またはモバイル アプリケーションの開発に使用できる Web サービスのスイートです。 サービスでは、次の 2 つのデータ型が提供されます。

* トラフィック フロー:ネットワーク内のすべての主要道路で観察されたリアルタイムの速度と移動時間。
* トラフィック インシデント:道路ネットワークでの交通渋滞およびインシデントに関する最新のビュー。

![交通情報を使用したマップの例](media/about-azure-maps/intro_traffic.png)

詳細については、[Traffic Service のドキュメント](/rest/api/maps/traffic)を参照してください。

### <a name="weather-services-preview"></a>Weather Service (プレビュー) 

Weather Service では、開発者が特定の場所の気象情報を取得するために使用できる API シリーズが提供されます。 この情報には、観測日時、気象条件の短い説明、気象アイコン、降水量インジケーター フラグ、気温、風速情報などの詳細が含まれています。 RealFeel™ の気温と UV の指数などの追加情報も返されます。

開発者は[ルートに沿った気象の取得 API](/rest/api/maps/weather/getweatheralongroutepreview) を使用して、特定のルートに沿った気象情報を取得できます。 また、このサービスでは、洪水や大雨など、気象災害の影響を受ける通過地点に関する気象通知の生成もサポートされています。

[Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview) を使用すると、過去、現在、および未来のレーダー タイルおよび衛星タイルを要求できます。

![リアルタイムの気象レーダー タイルを使用したマップの例](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Maps Creator サービス (プレビュー) 

Maps Creator サービスは、屋内マップ データに基づくマップ機能があるアプリケーションを作成するために開発者が使用できる Web サービスのスイートです。

Maps Creator では、次の 3 つの主要なサービスが提供されています。

* [Dataset サービス](/rest/api/maps/dataset)。 変換された Drawing パッケージ データからデータセットを作成するには、Dataset サービスを使用します。 Drawing パッケージの要件については、「Drawing パッケージの要件」を参照してください。

* [変換サービス](/rest/api/maps/dataset)。 変換サービスを使用すると、DWG 設計ファイルを屋内マップ用の Drawing パッケージ データに変換できます。

* [Tileset サービス](/rest/api/maps/tileset)。 データセットのベクター ベースの表現を作成するには、Tileset サービスを使用します。 アプリケーションでは、タイルセットを使用して、データセットの視覚的なタイルベースのビューを提供できます。

* [地物状態サービス](/rest/api/maps/featurestate)。 動的なマップ スタイル設定をサポートするには、地物状態サービスを使用します。 動的なマップ スタイル設定を使用すると、アプリケーションは、IoT システムによって提供される空間にリアルタイムのイベントを反映できます。

* [WFS サービス](/rest/api/maps/featurestate)。 WFS サービスを使用すると、屋内マップ データに対するクエリを実行できます。 WFS サービスは、1 つのデータセットに対してクエリを行う [Open Geospatial Consortium API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) 標準に準拠しています。

### <a name="elevation-service-preview"></a>Elevation Service (プレビュー)

Azure Maps Elevation Service は、開発者が地球の表面のどこからでも海抜データを取得するために使用できる Web サービスです。

Elevation Service を使用すると、海抜データを次の 2 つの形式で取得できます。

* **GeoTIFF ラスター形式**。 [Render V2 - Get Map Tile API](/rest/api/maps/renderv2) を使用して、タイル形式で海抜データを取得します。

* **GeoJSON 形式**。 [海抜 API](/rest/api/maps/elevation) シリーズを使用して、パス沿い、定義された境界ボックス内、または特定の座標にあるサンプリングされた海抜データを要求します。 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="海抜データを含む地図の例":::


## <a name="programming-model"></a>プログラミング モデル

Azure Maps は、モビリティ向けに構築されており、クロスプラットフォーム アプリケーションの開発に役立てることができます。 言語に依存せず、[REST API シリーズ](/rest/api/maps/)を介して JSON 出力をサポートするプログラミング モデルを使用します。

さらに、Azure Maps は、便利な [JavaScript マップ コントロール](/javascript/api/azure-maps-control)を単純なプログラミング モデルで提供します。 Web アプリケーションであれ、モバイル アプリケーションであれ、すばやく簡単に開発することができます。





## <a name="power-bi-visual"></a>Power BI 視覚エフェクト

Power BI 用の Azure Maps ビジュアルを使用すると、地図上の空間データに対してさまざまなをデータ視覚化を提供できます。 80% を超えるビジネス データに位置情報コンテキストがあると推定されています。 Azure Maps ビジュアルを使用することで、この位置情報コンテキストとビジネス データの関連性や、ビジネス データへの影響といった分析情報を得るための、コード不要のソリューションが提供されます。

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Azure Maps ビジュアルを使用してビジネス データを表示した Power BI Desktop":::

詳細については、[Azure Maps Power BI ビジュアル](power-bi-visual-getting-started.md)に関するドキュメントを参照してください。

## <a name="usage"></a>使用法

Azure Maps サービスにアクセスするには、[Azure portal](https://portal.azure.com) に移動し、Azure Maps アカウントを作成します。

Azure Maps では、キーベースの認証スキームが使用されます。 アカウントを作成すると、2 つのキーが生成されます。 Azure Maps サービスに対して認証するには、いずれかのキーを使用します。

注: Azure Maps では、顧客が入力した住所や場所のクエリ ("クエリ") を、マッピング機能の向上のためにサードパーティの TomTom と共有します。 クエリが TomTom と共有される際、クエリはどの顧客やエンド ユーザーにもリンクされないため、個人を識別するために使用されることはありません。 Moovit および AccuWeather との統合を含むモビリティおよび気象サービスは、現在[プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階です。

Microsoft は現在、オンライン サービスの下請業者の一覧に TomTom、Moovit、および AccuWeather を追加するプロセスを実行中です。

## <a name="supported-regions"></a>サポートされているリージョン

Azure Maps サービスは現在、以下を除くすべての国とリージョンで利用できます。

* 中国
* 韓国

ご利用中の IP アドレスの場所が、サポート対象の国/地域に存在していることを確認してください。

## <a name="next-steps"></a>次のステップ

Azure Maps を紹介するサンプル アプリを試す。

[クイック スタート: Web アプリを作成する](quick-demo-map-app.md)

Azure Maps に関する最新情報を受け取る。

[Azure Maps のブログ](https://azure.microsoft.com/blog/topics/azure-maps/)