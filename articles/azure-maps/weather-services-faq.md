---
title: Microsoft Azure Maps Weather Service (プレビュー) のよく寄せられる質問 (FAQ)
description: Azure Maps Weather Service (プレビュー) のデータや機能に関する一般的な質問への回答を見つけてください。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678131"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Azure Maps Weather Service (プレビュー) のよく寄せられる質問 (FAQ)

> [!IMPORTANT]
> Azure Maps Weather Service は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Maps [Weather Service](/rest/api/maps/weather) のデータや機能に関する一般的な質問への回答を示します。 次のトピックが含まれています。

* データ ソースとデータ モデル
* Weather Service の対象範囲と可用性
* データの更新頻度
* Azure Maps SDK を使用した開発
* 気象データを視覚化するためのオプション (Microsoft Power BI 統合を含む)

## <a name="data-sources-and-data-models"></a>データ ソースとデータ モデル

**Azure Maps は気象データをどのように取得しますか?**

Azure Maps は、基になる気象データを提供している、AccuWeather を含む世界レベルのモビリティおよび位置情報テクノロジ パートナーのコラボレーションによって構築されています。 Azure Maps の AccuWeather とのコラボレーションのお知らせを読むには、[雨または晴れ: Azure Maps Weather Service による企業への分析情報の提供](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/)に関するページを参照してください。

AccuWeather には、主に、多数の国家政府気象機関とのパートナーシップやその他の独自の協定による、世界中どこでも使用可能なリアルタイムの気象および環境情報があります。 この基本情報の一覧を次に示します。

* 政府機関からの公開されている地球表面観測
* 政府および民間企業からの独自の表面観測データセット
* 40 を超える国/地域の高解像度レーダー データ
* クラス最高のリアルタイムのグローバルな落雷データ
* 60 を超える国/地域および特別地域の政府発行の気象警報
* 世界全体をカバーしている静止気象衛星からの衛星データ
* 内部の独自のモデリング、米国の Global Forecast System (GFS) などの政府モデル、民間企業によって提供される固有のスケールダウンされたモデルを含む 150 を超える数値予報モデル
* 大気質の観測
* 輸送部門からの観測

ユーザーが使用できるようになる現在の状態を作成し、それに影響を与えるために、数万の表面観測がその他のデータと共に組み込まれています。 これには、自由に使用できる標準データセットだけでなく、多数の国/地域 (インド、ブラジル、カナダを含む) の国家気象サービスから入手される固有の観測やその他の独自の入力も含まれます。 これらの固有のデータセットにより、ユーザーに対する現在の状態のデータの空間および時間的な解像度が向上します。 

これらのデータセットは、Digital Forecast System の精度のためにリアルタイムにレビューされます。このシステムは、AccuWeather の独自の人工知能アルゴリズムを利用して予報を継続的に変更することにより、常に最新のデータが組み込まれ、それによって精度が継続的に最大化されることを保証します。

**気象予測データはどのようなモデルで作成されますか?**

グローバルな予測を作成するために、多数の気象予測ガイダンス システムが利用されています。 150 を超える数値予報モデルが毎日、外部データセットと内部データセットの両方で使用されます。 これには、ヨーロッパの Centre ECMWF や米国の Global Forecast System (GFS) などの政府モデルが含まれます。 さらに、AccuWeather には、より高い精度で気象を予測するために、予測を特定の場所や戦略的な地域ドメインにスケールダウンする独自の高解像度モデルが組み込まれています。 AccuWeather の固有の混合および重み付けアルゴリズムは、過去数 10 年間にわたって開発されてきました。 これらのアルゴリズムは、多数の予測入力を最適に利用して、きわめて正確な予測を提供します。

## <a name="weather-services-preview-coverage-and-availability"></a>Weather Service (プレビュー) の対象範囲と可用性

**さまざまな国/地域に対して、どのような種類の対象範囲を期待できますか?**

Weather Service の対象範囲は、国/地域ごとに異なります。 すべての国/地域ですべての機能が使用できるわけではありません。 詳細については、[対象範囲のドキュメント](./weather-coverage.md)を参照してください。

## <a name="data-update-frequency"></a>データの更新頻度

**現在の状態のデータはどれくらいの頻度で更新されますか?**

現在の状態のデータは、少なくとも約 1 時間に 1 回更新されますが、温度の急激な変化、空の状態の変化、降水量の変化など、急速に変化する状態ではより頻繁に更新できます。 世界中のほとんどの観測所は、状態が変化すると、1 時間に何回も報告します。 ただし、いくつかの領域では引き続き、スケジュールされた間隔で 1 時間に 1 回、2 回、または 4 回しか更新しません。  

Azure Maps は、現在の状態のデータを最大 10 分間キャッシュして、発生したデータをほぼリアルタイムの更新頻度でキャプチャできるようにします。 キャッシュされた応答の有効期限がいつ切れるかを確認し、期限切れのデータが表示されないようにするには、Azure Maps API 応答の HTTP ヘッダーにある Expires ヘッダー情報を利用できます。

**日単位および時間単位の予測データはどれくらいの頻度で更新されますか?**

日単位および時間単位の予測データは 1 日に複数回、更新された観測が受信されたときに更新されます。  たとえば、予測された高/低温を超えた場合、予測データは次の更新サイクルで調整されます。 これは、さまざまな間隔で発生する可能性がありますが、通常は 1 時間以内に発生します。 多くの突然の天候により、予測データが変更される場合があります。 たとえば、暑い夏の午後に孤立した雷雨が突然現れ、雲の範囲が広がり、雨が激しくなる場合があります。 孤立した嵐により、温度が実質的に 10 度ほど下がることがあります。 この新しい温度値は、その日の残りの時間単位および日単位の予測に影響を与えるため、データセットの予測が更新されます。

Azure Maps の Forecast API は、最大 30 分間キャッシュされます。 キャッシュされた応答の有効期限がいつ切れるかを確認し、期限切れのデータが表示されないようにするには、Azure Maps API 応答の HTTP ヘッダーにある Expires ヘッダー情報を利用できます。 特定の製品のユース ケースや UI (ユーザー インターフェイス) に基づいて、必要に応じて更新することをお勧めします。

## <a name="developing-with-azure-maps-sdks"></a>Azure Maps SDK を使用した開発

**Azure Maps Web SDK は Weather Service (プレビュー) 統合をネイティブにサポートしていますか?**

Azure Maps の Web SDK は、サービス モジュールを提供します。 このサービス モジュールは、 JavaScript または TypeScript を使用して、Web または Node.js アプリケーションで Azure Maps REST サービスを簡単に使用できるようにするヘルパー ライブラリです。 使用を開始するには、この[ドキュメント](./how-to-use-services-module.md)を参照してください。

**Azure Maps Android SDK は Weather Service (プレビュー) 統合をネイティブにサポートしていますか?**

Azure Maps Android SDK は、x/y/ズーム表記、クアッド キー表記、または EPSG 3857 境界ボックス表記を使用できる、Mercator のタイル レイヤーをサポートしています。

Web SDK モジュールと同様の Java/Android 用のサービス モジュールを作成する予定です。 Android サービス モジュールにより、Java または Android アプリで、すべての Azure Maps サービスに簡単にアクセスできるようになります。  

## <a name="data-visualizations"></a>データ可視化  

**Azure Maps Power BI ビジュアルは Azure Maps の気象タイルをサポートしていますか?**

はい。 レーダーおよび赤外線衛星タイルを Microsoft Power BI ビジュアルに移行する方法については、[Power BI ビジュアルへのタイル レイヤーの追加](./power-bi-visual-add-tile-layer.md)に関するページを参照してください。 

**レーダーおよび衛星タイルに使用される色はどのように解釈したらよいですか?**

Azure Maps [Weather の概念に関する記事](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale)には、レーダーおよび衛星タイルに使用される色を解釈するために役立つガイドが含まれています。 この記事では、色のサンプルと 16 進カラー コードについて説明しています。
 
**レーダーおよび衛星タイルのアニメーションを作成できますか?**

はい。 リアルタイムのレーダーおよび衛星タイルに加えて、Azure Maps の顧客は、地図のオーバーレイを使用してデータの視覚化を強化するために過去と将来のタイルを要求できます。 これは、[Get Map Tile v2 API](/rest/api/maps/renderv2/getmaptilepreview) を直接呼び出すか、または Azure Maps Web SDK 経由でタイルを要求することによって行うことができます。 レーダー タイルは、以前は最大 1.5 時間、将来は最大 2 時間提供されます。 これらのタイルは 5 分間隔で入手できます。 赤外線タイルは、以前は最大 3 時間提供され、10 分間隔で入手できます。 詳細については、オープンソースの気象タイル アニメーションの[コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)を参照してください。  

**さまざまな天候のアイコンを提供していますか?**

はい。 アイコンとそれに対応するコードは、[ここ](./weather-services-concepts.md#weather-icons)で見つけることができます。 応答で *iconCode* を返すのは、Weather Service (プレビュー) API の一部 ([Get Current Conditions API](/rest/api/maps/weather/getcurrentconditionspreview) など) だけであることに注意してください。 詳細については、現在の WeatherConditions のオープンソースの[コード サンプル](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)を参照してください。

## <a name="next-steps"></a>次のステップ

この FAQ でご質問の回答が得られない場合は、次のチャネル (エスカレーション順) を通してお問い合わせいただくことができます。

* この記事のコメント セクション。
* [Azure Maps の MSFT Q&A ページ](/answers/topics/azure-maps.html)。
* Microsoft サポート。 新しいサポート要求を作成するには、[Azure portal](https://portal.azure.com/) の [ヘルプ] タブで、 **[ヘルプとサポート]** ボタンを選択してから **[新しいサポート要求]** を選択します。
* 機能要求を送信するための [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps)。

Azure Maps Weather Service (プレビュー) を使用して、リアルタイムの予測される気象データを要求する方法を学習する:
> [!div class="nextstepaction"]
> [リアルタイムの気象データを要求する ](how-to-request-weather-data.md)

Azure Maps Weather Service (プレビュー) の概念に関する記事:
> [!div class="nextstepaction"]
> [Weather Service の概念](weather-coverage.md)

Azure Maps Weather Service (プレビュー) API のドキュメントを確認する:

> [!div class="nextstepaction"]
> [Azure Maps Weather Service](/rest/api/maps/weather)