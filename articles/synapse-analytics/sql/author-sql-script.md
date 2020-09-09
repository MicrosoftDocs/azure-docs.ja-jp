---
title: Azure Synapse Studio (プレビュー) 内の SQL スクリプト
description: Azure Synapse Studio (プレビュー) の SQL スクリプトの概要
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921390"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Azure Synapse Studio (プレビュー) での SQL スクリプトの使用

Azure Synapse Studio (プレビュー) は、SQL クエリを作成するための SQL スクリプト Web インターフェイスを提供します。 SQL プール (プレビュー) または SQL オンデマンド (プレビュー) に接続できます。 

## <a name="begin-authoring-in-sql-script"></a>SQL スクリプトの作成を開始する 

SQL スクリプトで作成のエクスペリエンスを開始するには、いくつかの方法があります。 新しい SQL スクリプトを作成するには、次の方法のいずれかを使用します。

1. [開発] メニューの **[+]** アイコンを選択し、 **[SQL スクリプト]** を選択します。

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. **[操作]** メニューの **[New SQL script]** \(新しい SQL スクリプト\) を選択します
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

または、次を実行できます。 

3. [開発] の [SQL スクリプト] の **[アクション]** メニューから、 **[インポート]** を選択し、お使いのローカル ストレージから既存の SQL スクリプトを選択します。
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL スクリプトを作成する

1. **[プロパティ]** ボタンを選択し、作成する SQL スクリプトの名前を選択して、SQL スクリプトに割り当てられた既定の名前を置き換えます。
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. **[接続先]** ドロップダウン メニューから、特定の SQL プールまたは SQL オンデマンドを選択します。 または、必要に応じて、 **[Use database]\(使用するデータベース\)** からデータベースを選択します。
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. IntelliSense 機能を使用して、SQL スクリプトの作成を開始します。

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL スクリプトを実行する

ご自分の SQL スクリプトを実行するには、 **[実行]** ボタンを選択します。 既定では、結果はテーブルに表示されます。

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>結果をエクスポートする

結果は、さまざまな形式 (CSV、Excel、JSON、XML など) でローカル ストレージにエクスポートできます。そのためには、[結果のエクスポート] を選択して、拡張子を選択します。

**[グラフ]** ボタンを選択して、SQL スクリプトの結果をグラフで視覚化することもできます。 [グラフの種類] と **[Category column]\(カテゴリ列\)** を選択します。 **[イメージとして保存]** を選択して、グラフを画像にエクスポートします。 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Parquet ファイルからデータを探索する

SQL スクリプトを使用してストレージ アカウント内の Parquet ファイルを探索して、ファイルの内容をプレビューすることができます。

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL テーブル、外部テーブル、ビュー

データの下の **[アクション]** メニューを選択すると、次などいくつかのアクションを選択できます。

- New SQL script (新しい SQL スクリプト)
- 上位 1000 行を選択
- CREATE
- DROP および CREATE 
 
SQL プールおよび SQL オンデマンドのノードを右クリックして、使用可能なジェスチャを調べます。
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>次のステップ

SQL スクリプトの作成の詳細については、「[Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)」を参照してください。
