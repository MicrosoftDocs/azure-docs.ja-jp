---
title: Azure Monitor ブックのタイルの視覚化
description: Azure Monitor ブックのタイルのすべての視覚化について説明します。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100601115"
---
# <a name="tile-visualizations"></a>タイルの視覚化

タイルは、ブックに概要データを表示するための便利な方法です。 次の図は、タイルの一般的なユース ケースを示しており、詳細グリッドの上部にアプリ レベルの概要があります。

[![タイルの概要ビューのスクリーンショット](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

ブックのタイルでは、タイトル、サブタイトル、大きなテキスト、アイコン、メトリック ベースのグラデーション、スパーク ライン/バー、フッターなどの表示がサポートされています。

## <a name="adding-a-tile"></a>タイルの追加

1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. **[追加]** 、 *[クエリの追加]* の順に選択して、ログ クエリ コントロールをブックに追加します。
3. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
4. クエリ エディターを使用して、分析用の KQL を入力します。

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. サイズを **[Full]\(最大\)** に設定します。
6. 視覚化を **[タイル]** に設定します。
7. **[タイルの設定]** ボタンを選択して、設定ペインを開きます。
    1. *[タイトル]* で、次のように設定します。
        * 使用する列: `name`。
    2. *[左]* で、次のように設定します。
        * 使用する列: `Requests`。
        * 列レンダラー: `Big Number`。
        * カラー パレット: `Green to Red`
        * 最小値: `0`。
    3. *[下]* で、次のように設定します。
        * 使用する列: `appName`。
8. ペインの下部にある **[保存して閉じる]** ボタンを選択します。

[![上記のクエリとタイル設定を含むタイルの概要ビューのスクリーンショット。](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

読み取りモードのタイル:

[![読み取りモードのタイルの概要ビューのスクリーンショット。](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>タイルのスパーク ライン

1. _[編集]_ ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. `TimeRange` という時間の範囲パラメーターを追加します。
    1. **[追加]** を選択して *[パラメーターの追加]* を選択します。
    2. パラメーター コントロールで、 **[パラメーターの追加]** を選択します。
    3. *[パラメーター名]* フィールドに `TimeRange` を入力し、 *[パラメーターの種類]* で `Time range picker` を選択します。
    4. ペインの上部にある **[保存]** を選択し、パラメーターコントロール内の **[Done Editing]\(編集の完了\)** を選択します。
3. **[追加]** 、 *[クエリの追加]* の順に選択して、パラメーター コントロールの下にあるログ クエリ コントロールを追加します。
4. クエリの種類として **[ログ]** を選択し、リソースの種類 (たとえば、Application Insights) と、ターゲットにするリソースを選択します。
5. クエリ エディターを使用して、分析用の KQL を入力します。

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. **[クエリの実行]** を選択します。 (クエリを実行する前に、`TimeRange` を選択した値に設定してください)。
7. *[視覚化]* を [タイル] に設定します。
8. *[サイズ]* を [Full]\(最大\) に設定します。
9. **[タイルの設定]** を選択します。
    1. *[タイル]* で、次のように設定します。
        * 使用する列: `name`。
    2. *[サブタイル]* で、次のように設定します。
        *  使用する列: `appNAme`。
    3. *[左]* で、次のように設定します。
        *  使用する列: `Requests`。
        * 列レンダラー: `Big Number`。
        * カラー パレット: `Green to Red`。
        * 最小値: `0`。
    4. *[下]* で、次のように設定します。
        * 使用する列: `Trend`。
        * 列レンダラー: `Spark line`。
        * カラー パレット: `Green to Red`。
        * 最小値: `0`。
10. ペインの下部にある **[保存して閉じる]** を選択します。

![スパーク ラインを使用したタイルの視覚化のスクリーンショット](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>タイルのサイズ

作成者には、タイルの設定でタイルの幅を設定するオプションがあります。

* `fixed` (既定値)

    タイルは既定で、同じ固定幅 (約 160 ピクセル幅) で表示され、タイルの周囲にスペースがあります。

    ![固定幅のタイルを表示するスクリーンショット](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    各タイトルはそのコンテンツに合わせて縮小または拡大されますが、タイルはタイルのビューの幅に制限されます (水平スクロールなし)。

    ![自動幅のタイルを表示するスクリーンショット](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    各タイトルは常に、タイルのビューの全幅で表示され、1 行につき 1 つのタイトルになります。

     ![フルサイズ幅のタイルを表示するスクリーンショット](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>次の手順

* タイルでは、複合棒グラフのレンダラーもサポートされています。 詳細については、[複合棒グラフに関するドキュメント](workbooks-composite-bar.md)を参照してください。
* `TimeRange` などの時間パラメーターの詳細については、[ブックの時間パラメーターに関するドキュメント](workbooks-time.md)を参照してください。