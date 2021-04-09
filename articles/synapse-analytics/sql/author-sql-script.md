---
title: Synapse Studio での SQL スクリプト
description: Azure Synapse Analytics での Synapse Studio SQL スクリプトの概要。
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98118695"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Synapse Studio SQL スクリプト 

Synapse Studio は、SQL クエリを作成するための SQL スクリプト Web インターフェイスを提供します。 

## <a name="begin-authoring-in-sql-script"></a>SQL スクリプトの作成を開始する 

SQL スクリプトで作成のエクスペリエンスを開始するには、いくつかの方法があります。 新しい SQL スクリプトを作成するには、次の方法のいずれかを使用します。

1. [開発] メニューの **[+]** アイコンを選択し、 **[SQL スクリプト]** を選択します。

2. **[操作]** メニューの **[New SQL script]** \(新しい SQL スクリプト\) を選択します

3. [開発]、[SQL スクリプト] の **[アクション]** メニューで、 **[インポート]** を選択します。 ローカル ストレージから既存の SQL スクリプトを選択します。
![新しい SQL スクリプト 3 アクション](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>SQL スクリプトを作成する

1. **[プロパティ]** ボタンを選択し、作成する SQL スクリプトの名前を選択して、SQL スクリプトに割り当てられた既定の名前を置き換えます。 
![新しい SQL スクリプトの名前の変更](media/author-sql-script/new-sql-script-rename.png)

2. **[接続先]** ドロップダウン メニューから、特定の専用 SQL プールまたはサーバーレス SQL プールを選択します。 または、必要に応じて、 **[Use database]\(使用するデータベース\)** からデータベースを選択します。 
![新しい SQL のプールの選択](media/author-sql-script/new-sql-choose-pool.png)

3. IntelliSense 機能を使用して、SQL スクリプトの作成を開始します。

## <a name="run-your-sql-script"></a>SQL スクリプトを実行する

ご自分の SQL スクリプトを実行するには、 **[実行]** ボタンを選択します。 既定では、結果はテーブルに表示されます。

![新しい SQL スクリプトの結果テーブル](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>結果をエクスポートする

結果は、さまざまな形式 (CSV、Excel、JSON、XML など) でローカル ストレージにエクスポートできます。そのためには、[結果のエクスポート] を選択して、拡張子を選択します。

**[グラフ]** ボタンを選択して、SQL スクリプトの結果をグラフで視覚化することもできます。 [グラフの種類] と **[Category column]\(カテゴリ列\)** を選択します。 **[イメージとして保存]** を選択して、グラフを画像にエクスポートします。 

![新しい SQL スクリプトの結果のグラフ](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet ファイルからデータを探索する

SQL スクリプトを使用してストレージ アカウント内の Parquet ファイルを探索して、ファイルの内容をプレビューすることができます。

![新しいスクリプト sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL テーブル、外部テーブル、ビュー

データの下の **[アクション]** メニューを選択すると、次などいくつかのアクションを選択できます。

- New SQL script (新しい SQL スクリプト)
- 上位 1000 行を選択
- CREATE
- DROP および CREATE 
 
SQL データベースのノードを右クリックして、使用可能なジェスチャを調べます。
 
![新しいスクリプト データベース](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>フォルダーを作成し、SQL スクリプトをフォルダーに移動する

[開発]、[SQL スクリプト] の [アクション] メニューから、[新しいフォルダー] を選択します。 次に、ポップアップ ウィンドウに新しいフォルダーの名前を入力します。 

> [!div class="mx-imgBorder"] 
> ![SQL スクリプトの例のスクリーンショット。[新しいフォルダー] が選択されています。](./media/author-sql-script/new-sql-script-create-folder.png)

SQL スクリプトをフォルダーに移動するには、SQL スクリプトを選択し、[アクション] メニューの [移動] を選択します。 次に、新しいウィンドウで目的のフォルダーを検索し、SQL スクリプトを選択済みフォルダーに移動します。SQL スクリプトをすばやくドラッグしてフォルダーにドロップすることもできます。  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>次のステップ

SQL スクリプトの作成の詳細については、「[Azure Synapse Analytics](../index.yml)」を参照してください。