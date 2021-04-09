---
title: Azure Monitor ブックのマップの視覚化
description: Azure Monitor ブックのマップの視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100601135"
---
# <a name="map-visualization"></a>マップの視覚エフェクト

マップの視覚エフェクトは、各場所、国、地域にマップされたすべてのデータを集計する機能を提供することで、特定の地域の問題をピンポイントで特定し、監視データの高レベルの集計ビューを表示するのに役立ちます。

次のスクリーンショットは、さまざまなストレージ アカウントの合計トランザクションとエンドツーエンドの待ち時間を示しています。 ここでは、サイズはトランザクションの総数によって決定され、マップの下の色メトリックはエンドツーエンドの待ち時間を示します。 最初の観察から分かることは、**米国西部** リージョンのトランザクション数は **米国東部** リージョンと比べて少ないものの、**米国西部** リージョンのエンドツーエンドの待ち時間は **米国東部** リージョンよりも長くなっているということです。 このことから、**米国西部** に何らかの問題が発生しているという最初の分析情報が得られます。

![Azure の場所のマップを示すスクリーンショット](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>マップを追加する

基になるデータとメトリックに緯度と経度の情報、Azure リソース情報、Azure の場所情報または国と地域、名前、または国と地域のコードが含まれている場合は、マップを視覚化できます。

### <a name="using-azure-location"></a>Azure の場所を使用する

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[クエリの追加]* の順に選択します。
3. *[データソース]* を `Azure Resource Graph` に変更し、ストレージ アカウントを持つ任意のサブスクリプションを選択します。
4. 分析のために下のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. *[サイズ]* を `Large` に設定します。
6. *[視覚化]* を `Map` に設定します。
7. すべての設定が自動で入力されます。 カスタム設定の場合は、 **[マップの設定]** ボタンを選択して、設定ペインを開きます。
8. 選択したサブスクリプションの Azure リージョンごとのストレージ アカウントを示すマップの視覚化のスクリーンショットを次に示します。

![上記のクエリを使用した Azure の場所のマップを示すスクリーンショット](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Azure リソースを使用する

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[メトリックの追加]* の順に選択します。
3. ストレージ アカウントを持つサブスクリプションを使用します。
4. *[リソースの種類]* を `storage account` に変更し、 *[リソース]* で複数のストレージ アカウントを選択します。
5. **[メトリックの追加]** を選択し、[トランザクション] メトリックを追加します。
    1. 名前空間: `Account`
    2. メトリック: `Transactions`
    3. 集計: `Sum`
    
    ![トランザクション メトリックのスクリーンショット](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. **[メトリックの追加]** を選択し、[成功した場合の E2E 待機時間] メトリックを追加します。
    1. 名前空間: `Account`
    1. メトリック: `Success E2E Latency`
    1. 集計: `Average`
    
    ![成功した場合のエンドツーエンド待機時間メトリックのスクリーンショット](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. *[サイズ]* を `Large` に設定します。
1. *[視覚化]* サイズを `Map` に設定します。
1. **[マップの設定]** で、次のように設定します。
    1. Location info using (次を使用した場所情報): `Azure Resource`
    1. Azure resource field (Azure リソース フィールド): `Name`
    1. Size by (項目別サイズ): `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Aggregation for location (場所の集計): `Sum of values`
    1. 色分けの種類: `Heatmap`
    1. Color by (項目別の色): `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregation for color (色の集計): `Sum of values`
    1. カラー パレット: `Green to Red`
    1. 最小値: `0`
    1. メトリック値: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregate other metrics by (他のメトリックを集計する基準): `Sum of values`
    1. **[カスタムの書式設定]** ボックスを選択します
    1. 単位: `Milliseconds`
    1. スタイル: `Decimal`
    1. Maximum fractional digits (小数点以下の最大桁数): `2`

### <a name="using-countryregion"></a>国または地域を使用する

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. * *[追加]* 、 *[クエリの追加]* の順に選択します。
3. *[データ ソース]* を `Log` に変更します。
4. *[リソースの種類]* で `Application Insights` を選択し、pageViews データを含む任意の Application Insights リソースを選択します。
5. クエリ エディターを使用して、分析用の KQL を入力し、 **[クエリの実行]** を選択します。

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. サイズの値を `Large` に設定します。
7. 視覚化を `Map` に設定します。
8. すべての設定が自動で入力されます。 カスタム設定の場合は、 **[マップの設定]** を選択します。

### <a name="using-latitudelocation"></a>緯度と場所を使用する

1. **[編集]** ツール バー項目を選択して、ブックを編集モードに切り替えます。
2. * *[追加]* 、 *[クエリの追加]* の順に選択します。
3. *[データ ソース]* を `JSON` に変更します。
1. クエリ エディターで下の JSON データを入力し、 **[クエリの実行]** を選択します。
1. *[サイズ]* の値を `Large` に設定します。
1. *[視覚化]* を `Map` に設定します。
1. [メトリックの設定] にある **[マップの設定]** で、 *[メトリック ラベル]* を `displayName` に設定し、 **[保存して閉じる]** を選択します。

下に示すマップの視覚化では、選択されたメトリック ラベルで、各緯度と経度の場所のユーザーを示しています。

![選択されたメトリック ラベルで、各緯度と経度の場所のユーザーを示すマップの視覚化のスクリーンショット](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>地図の設定

### <a name="layout-settings"></a>レイアウトの設定

| 設定 | 説明 |
|:-------------|:-------------|
| `Location info using` | マップに表示される項目の場所を取得する方法を選択します。 <br> **緯度/経度**:緯度と経度の情報を含む列がある場合に、このオプションを選択します。 緯度と経度のデータを含む各行は、マップ上で個別の項目として表示されます。 <br> **Azure の場所**:Azure の場所 (eastus、westeurope、centralindia など) の情報を含む列がある場合に、このオプションを選択します。 その列を指定すると、Azure の各場所に対応する緯度と経度が取り込まれ、同じ場所の行 (指定された集計に基づく) がグループ化されてマップ上に場所が表示されます。 <br> **Azure リソース**:Azure リソース情報 (ストレージ カウント、cosmosdb アカウントなど) を含む列がある場合に、このオプションを選択します。 その列を指定すると、Azure の各リソースに対応する緯度と経度が取り込まれ、同じ場所 (Azure の場所) の行 (指定された集計に基づく) がグループ化されてマップ上に場所が表示されます。 <br> **国/地域**:国、地域の名前、コード (US、米国、IN、インド、CN、中国) の情報を含む列がある場合に、このオプションを選択します。 その列を指定すると、それぞれの国、地域、コードに対応する緯度と経度が取り込まれ、同じ国-地域コード、国-地域名で行がグループ化されてマップ上に場所が表示されます。 国名と国番号は、マップ上で 1 つのエンティティとしてグループ化されることはありません。
| `Latitude/Longitude` | これらの 2 つのオプションは、[場所情報] フィールドの値が次の場合に表示されます: 緯度/経度。 緯度フィールドに緯度が指定されている列と、経度フィールドに経度が指定されている列をそれぞれ選択します。 |
| `Azure location field` | このオプションは、[場所情報] フィールドの値が次の場合に表示されます: Azure の場所。 Azure の場所情報を含む列を選択します。 |
| `Azure resource field` | このオプションは、[場所情報] フィールドの値が次の場合に表示されます: Azure リソース。 Azure のリソース情報を含む列を選択します。 |
| `Country/Region field` | このオプションは、[場所情報] フィールドの値が次の場合に表示されます: 国または地域。 国/地域の情報を含む列を選択します。 |
| `Size by` | このオプションは、マップに表示される項目のサイズを制御します。 サイズは、ユーザーが指定した列の値によって異なります。 現在、円の半径は列の値の平方根に正比例します。 [なし...] を選択すると、すべての円に既定の地域サイズが表示されます。|
| `Aggregation for location` | このフィールドは、同じ Azure の場所、Azure リソース、国-地域を持つ **size by (項目別サイズ)** 列を集計する方法を指定します。 |
| `Minimum region size` | このフィールドは、マップに表示される項目の最小半径を指定します。 これは、size by (項目別サイズ) 列の値の間に大きな差があるため、小さい項目がマップにほとんど表示されない場合に使用されます。 |
| `Maximum region size` | このフィールドは、マップに表示される項目の最大半径を指定します。 これは、size by (項目別サイズ) 列の値が非常に大きく、それらが占めているマップの領域が非常に大きい場合に使用されます。|
| `Default region size` | このフィールドは、マップに表示される項目の既定の半径を指定します。 Size By (項目別サイズ) 列が [なし...] であるか、値が 0 の場合に、既定の半径が使用されます。|
| `Minimum value` | リージョンのサイズを計算するために使用する最小値。 指定しない場合、最小値は集計後の最も小さい値になります。 |
| `Maximum value` | リージョンのサイズを計算するために使用する最大値。 指定しない場合、最大値は集計後の最も大きい値になります。|
| `Opacity of items on Map` | このフィールドは、マップに表示される項目の透明度を指定します。 不透明度 1 は透明度がないことを意味し、不透明度が 0 の場合は、項目はマップに表示されません。 マップ上に非常に多くの項目がある場合は、重なり合う項目がすべて表示されるように、不透明度を低い値に設定することができます。|

### <a name="color-settings"></a>色の設定

| 色分けの種類 | 説明 |
|:------------- |:-------------|
| `None` | すべてのノードが同じ色になります。 |
| `Thresholds` | この種類のセルの色は、しきい値ルールによって設定されます (たとえば、_CPU > 90% => 赤、60% > CPU > 90% => 黄、CPU < 60% => 緑_)。 <ul><li> **Color by (項目別の色)** :この列の値は、しきい値とヒートマップ ロジックによって使用されます。</li> <li>**色の集計**:このフィールドは、同じ Azure の場所、Azure リソース、国-地域を持つ **color by (項目別の色)** 列を集計する方法を指定します。 </li> <ul> |
| `Heatmap` | この種類のセルは、カラー パレットと [Color by]\(項目別の色\) フィールドに基づいて色分けされます。 また、同じ **[Color by]\(項目別の色\)** と **[Aggregation for color]\(色の集計\)** オプションがしきい値として使用されます。 |

### <a name="metric-settings"></a>メトリックの設定
| 設定 | 説明 |
|:------------- |:-------------|
| `Metric Label` | このオプションは、[場所情報] フィールドの値が次の場合に表示されます: 緯度/経度。 この機能を使用すると、ユーザーはマップの下に表示されるメトリックに表示するラベルを選択できます。 |
| `Metric Value` | このフィールドは、マップの下に表示されるメトリック値を指定します。 |
| `Create 'Others' group after` | このフィールドは、「その他」グループが作成される前の制限を指定します。 |
| `Aggregate 'Others' metrics by` | このフィールドは、「その他」グループが表示される場合にそれに使用する集計を指定します。 |
| `Custom formatting` | このフィールドを使用して、数値の単位、スタイル、書式設定のオプションを設定します。 これは、[グリッドのカスタム書式設定](../visualize/workbooks-grid-visualizations.md#custom-formatting)と同じです。|

## <a name="next-steps"></a>次の手順

- [ブックで蜂の巣の視覚化](../visualize/workbooks-honey-comb.md)を作成する方法について説明します。