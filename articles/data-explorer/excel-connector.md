---
title: Microsoft Excel 用 Azure Data Explorer コネクタを使用してデータを視覚化する
description: この記事では、Azure Data Explorer 用の Excel コネクタを使用する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174490"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Excel 用 Azure Data Explorer コネクタを使用してデータを視覚化する

Azure Data Explorer には、Excel のデータに接続する方法が 2 つ用意されています。ネイティブ コネクタを使用する方法と、Azure Data Explorer からクエリをインポートする方法です。 この記事では、Excel でネイティブ コネクタを使用し、Azure Data Explorer クラスターに接続してデータを取得および視覚化する方法について説明します。

Azure Data Explorer Excel ネイティブ コネクタを使用すると、クエリ結果を Excel にエクスポートすることができます。 KQL クエリを Excel データ ソースとして追加し、追加の計算や視覚化を行うこともできます。

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto クエリを Excel データ ソースとして定義し、データを Excel に読み込む

1. **Microsoft Excel**を開きます。
1. **[データ]** タブで、 **[データの取得]**  >  **[Azure から]**  >  **[Azure Data Explorer から]** の順に選択します。

    ![Azure Data Explorer からデータを取得する](media/excel-connector/get-data-from-adx.png)

1. **[Azure Data Explorer (Kusto)]** ウィンドウで、次のフィールドに入力して **[OK]** を選択します。

    ![Azure Data Explorer (Kusto) ウィンドウ](media/excel-connector/adx-connection-window.png)
    
    |フィールド   |説明 |
    |---------|---------|
    |**クラスター**   |   クラスターの名前 (必須)      |    
    |**データベース**     |    データベースの名前      |    
    |**テーブル名または Azure Data Explorer クエリ**    |     テーブルの名前または Azure Data Explorer クエリ    | 
    
    **詳細オプション:**

     |フィールド   |説明 |
    |---------|---------|
    |**クエリ結果のレコード数を制限する**     |     Excel に読み込まれるレコードの数を制限します  |    
    |**クエリ結果のデータ サイズを制限する (バイト単位)**    |    データ サイズを制限します      |   
    |**結果セットの切り詰めを無効にする**    |         |      
    |**セミコロンで区切られた追加の SET ステートメント**    |    `set` ステートメントを追加してデータ ソースに適用します     |   

1.  **[ナビゲーター]** ウィンドウで、適切なテーブルに移動します。 テーブルのプレビュー ウィンドウで、 **[データの変換]** を選択してデータを変更するか、 **[読み込む]** を選択して Excel に読み込みます。

![テーブルのプレビュー ウィンドウ](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > **[データベース]** または **[テーブル名または Azure Data Explorer クエリ]** が既に指定されている場合は、適切なテーブルのプレビュー ウィンドウが自動的に開きます。 

## <a name="analyze-and-visualize-data-in-excel"></a>Excel でデータを分析して視覚化する

データが Excel に読み込まれて Excel シートで使用できるようになったら、リレーションシップとビジュアルを作成して、データの分析、集計、および視覚化を行うことができます。 

1.  **[テーブル デザイン]** タブで、 **[ピボットテーブルで集計]** を選択します。 **[ピボットテーブルの作成]** ウィンドウで、関連するテーブルを選択し、 **[OK]** を選択します。

    ![ピボット テーブルを作成する](media/excel-connector/create-pivot-table.png)

1. **[ピボットテーブル フィールド]** ウィンドウで、関連するテーブル列を選択して概要テーブルを作成します。 次の例では、 **[イベント ID]** と **[状態]** が選択されています。
    
    ![ピボットテーブル フィールドを選択する](media/excel-connector/pivot-table-pick-fields.png)

1. **[ピボットテーブル分析]** タブで、 **[ピボットグラフ]** を選択して、テーブルに基づいてビジュアルを作成します。 

    ![ピボット グラフ](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 次の例では、気象ベントに関する追加情報を表示するために、 **[イベント ID]** 、 **[開始時刻]** 、 **[イベントの種類]** が選択されています。

    ![データの視覚化](media/excel-connector/visualize-excel-data.png)

1. データを監視するための完全なダッシュボードを作成します。

