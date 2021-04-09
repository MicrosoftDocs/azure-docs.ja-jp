---
title: Data Catalog の系列のユーザー ガイド (プレビュー)
description: この記事では、Azure Purview のカタログ系列機能の概要について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 8b08a60d484aa3d52600b8aef2f53d6ca8a04f9b
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952169"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure Purview Data Catalog の系列のユーザー ガイド

この記事では、Azure Purview Data Catalog のデータ系列機能の概要について説明します。

## <a name="background"></a>バックグラウンド

Azure Purview のプラットフォーム機能の 1 つに、データ プロセスによって作成されたデータセット間の系列を表示する機能があります。 Data Factory、Data Share、Power BI などのシステムでは、データの移動時に系列がキャプチャされます。 カスタム系列レポートは、Atlas フックと REST API を通じてもサポートされます。

## <a name="lineage-collection"></a>系列の収集 
 エンタープライズ データ システムから Azure Purview に収集されたメタデータは、エンドツーエンドのデータ系列を表示するために合成されます。 Purview に系列が収集されるデータ システムは、次の 3 種類に大別されます。

### <a name="data-processing-system"></a>データ処理システム
データ統合と ETL ツールでは、実行時に Azure Purview に系列をプッシュできます。 Data Factory、Data Share、Synapse、Azure Databricks などのツールは、このカテゴリのデータ システムに属します。 データ処理システムでは、さまざまなデータベースおよびストレージ ソリューションからのデータセットをソースとして参照して、ターゲット データセットが作成されます。 系列のために Purview と現在統合されるデータ処理システムの一覧を次の表に示します。


| データ処理システム | サポートされるスコープ |
| ---------------------- | ------------|
| Azure Data Factory | [コピー アクティビティ](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [データ フロー アクティビティ](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [SSIS パッケージの実行アクティビティ](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [スナップショットの共有](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>データ ストレージ システム
データベースとストレージ ソリューション (SQL Server、Teradata、SAP など) には、スクリプト言語を使用してデータを変換するクエリ エンジンがあります。 ストアド プロシージャからのデータ系列は、Purview に収集され、他のシステムの系列と合成されます。

| データ ストレージ システム | サポートされるスコープ |
| ---------------------- | ------------|
| Teradata | ストアド プロシージャ

### <a name="data-analytics--reporting-systems"></a>データ分析とレポート システム
Azure ML や Power BI などデータ システムでは、Azure Purview に系列が報告されます。 これらのシステムでは、ストレージ システムからのデータセットを使用して、メタ モデルを介して処理し、BI ダッシュボードや ML の実験などが作成されます。

| データ分析とレポート システム | サポートされるスコープ |
| ---------------------- | ------------|
| Power BI | [データセット、データフロー、レポートとダッシュボード](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>系列の使用を開始する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

Purview の系列には、データセットとプロセスが含まれます。 データセットはノードとも呼ばれ、プロセスはエッジとも呼ばれます。

* **データセット (ノード)** :プロセスへの入力として提供されるデータセット (構造化または非構造化)。 たとえば、SQL テーブル、Azure BLOB、ファイル (.csv や .xml など) は、すべてデータセットと見なされます。 Purview の系列セクションでは、データセットは四角形のボックスで表されます。

* **プロセス (エッジ)** :データセットに対して実行されるアクティビティまたは変換は、プロセスと呼ばれます。 たとえば、ADF のコピー アクティビティ、Data Share のスナップショットなどです。 Purview の系列セクションでは、プロセスは端が丸いボックスによって表されます。

Purview で資産の系列情報にアクセスするには、次の手順に従います。

1. Azure portal で、[[Azure Purview アカウント]](https://aka.ms/purviewportal) ページに移動します。

1. 一覧から Azure Purview アカウントを選択し、 **[概要]** ページで **[Purview アカウントの起動]** を選択します。

1. Azure Purview の **[ホーム]** ページで、データセット名またはプロセス名 (ADF のコピーまたはデータ フロー アクティビティなど) を検索します。 Enter キーを押します。

1. 検索結果から資産を選択し、 **[系列]** タブを選択します。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="[系列] タブを選択する方法を示すスクリーンショット。" border="true":::

## <a name="asset-level-lineage"></a>資産レベルの系列

Azure Purview では、データセットとプロセスの資産レベルの系列がサポートされます。 資産レベルの系列を表示するには、カタログ内の現在の資産の **[系列]** タブに移動します。 現在のデータセットの資産ノードを選択します。 既定では、データに属する列の一覧が、左側のペインに表示されます。

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="[系列] ページで列の表示を選択する方法を示すスクリーンショット" border="true":::

## <a name="dataset-column-lineage"></a>データセット列の系列

データセットの列レベルの系列を表示するには、カタログ内の現在の資産の **[系列]** タブに移動して、次の手順に従います。

1. [系列] タブが表示されたら、左側のペインで、データ系列に表示する各列の横にあるチェック ボックスをオンにします。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="[系列] ページに表示する列を選択する方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. 列のマッピングを表示するには、左側のペインで選択されている列、または系列キャンバスのデータセットの上にマウス ポインターを置きます。 すべての列インスタンスが強調表示されます。

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="データ系列パスの列フローを強調表示するために、列名の上にマウス ポインターを置く方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. 列の数が左側のペインに表示できる数よりも多い場合は、フィルター オプションを使用して、名前で特定の列を選択します。 または、マウスを使用して一覧をスクロールすることができます。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="[系列] ページで、列名で列をフィルター処理する方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. 系列キャンバスにより多くのノードとエッジが含まれている場合は、フィルターを使用してデータ資産またはプロセスのノードを名前で選択します。 または、マウスを使用して系列ウィンドウをパンすることができます。

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="[系列] ページの名前別のデータ資産ノードを示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. 左側のペインのトグルを使用して、系列キャンバス内のデータセットの一覧を強調表示します。 トグルをオフにすると、選択した列のうち少なくとも 1 つが含まれている資産が表示されます。 トグルをオンにすると、すべての列を含むデータセットのみが表示されます。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="[系列] ページのノードの一覧をフィルター処理するためのトグルの使用方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>プロセス列の系列
データ プロセスで 1 つ以上の入力データセットを使用して 1 つ以上の出力を生成できます。 Purview では、プロセス ノードに列レベルの系列を使用できます。 
1. 列パネルのドロップダウンから入力と出力のデータセットを切り替えます。
2. 入力データセットから対応する出力データセットへの系列のフローを表示するには、1 つ以上のテーブルから列を選択します。

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="プロセス ノードの列の系列を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>系列の資産を参照する
1. 任意の資産で **[資産に切り替え]** を選択し、[系列] ビューから対応するメタデータを表示します。 これは、[系列] ビューからカタログ内の別の資産を参照する効果的な方法です。

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="系列データ資産で [資産に切り替え] を選択する方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. 系列の多いデータセットでは、系列キャンバスが複雑になることがあります。 見やすくするために、既定のビューには、フォーカスされている資産の 5 レベルの系列のみが表示されます。 系列の残りの部分は、系列キャンバスのバブルをクリックすると展開できます。 データ コンシューマーは、キャンバス内の不要な資産を非表示にすることもできます。 さらに見やすくするには、系列キャンバスの上部にある **[より多くの系列]** のトグルをオフにします。 この操作により、系列キャンバスのすべてのバブルが非表示になります。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="[より多くの系列] を切り替える方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. 系列キャンバスのスマート ボタンを使用すると、系列の最適なビューを表示できます。 [自動レイアウト]、[適したサイズに変更]、[拡大/縮小]、[全画面表示]、[ナビゲーション マップ] を使用すると、カタログのイマーシブな系列のエクスペリエンスを利用できます。

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="系列のスマート ボタンを選択する方法を示すスクリーンショット。" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>次のステップ

* [系列を収集するために Azure Data Factory にリンクする](how-to-link-azure-data-factory.md)
* [系列のための Azure Data Share へのリンク](how-to-link-azure-data-share.md)