---
title: チュートリアル:Synapse Knowledge Center の探索を開始する
description: このチュートリアルでは、Synapse Knowledge Center を使用する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209204"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse Knowledge Center を探索する

このチュートリアルでは、Synapse Studio Knowledge Center を使用する方法について説明します。

## <a name="getting-to-the-knowledge-center"></a>Knowledge Center へのアクセス

Synapse Studio で Knowledge Center を検索するには、次の 2 つの方法があります。

  1. ページ右上付近にある [Home]\(ホーム\) ハブの **[Learn]\(詳細\)** をクリックします。
  2. 上部のメニュー バーで、 **[?]** 、 **[Knowledge Center]** の順にクリックします。

いずれかの方法を選択して、 **[Knowledge Center]** を開きます。

## <a name="overview"></a>概要

**Knowledge Center** では、次の 3 つを使用できます。
* **[Use samples immediately]\(サンプルをすぐに使用する\)** 。 Synapse の機能の例をすぐに確認したい場合は、このオプションを選択します。
* **[ギャラリーを参照]** 。 このオプションを使用すると、サンプル データ セットをリンクして、SQL スクリプト、ノートブック、パイプラインの形式でサンプル コードを追加できます。
* **[Tour Synapse studio]\(Synapse Studio のツアーを見る\)** 。 このオプションでは、Synapse Studio の基本的な部分についての簡単なツアーを見ることができます。 これは、Synapse Studio をこれまで使用したことがない場合に役立ちます。

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Blob Storage を探索する

1. **[Knowledge Center]** に移動し、 **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. **[Query data with SQL]\(SQL を使用してデータを照会する\)** を選択します。
1. **[Use sample]\(サンプルを使用する\)** をクリックします。
1. 新しいサンプル SQL スクリプトが開きます。
1. 最初のクエリ (28 行目から 32 行目) までスクロールして、クエリ テキストを選択します。
1. [実行] をクリックします。 選択したコードだけが実行されます。

## <a name="loading-more-nyc-taxi-data"></a>ニューヨーク市のタクシー データの追加の読み込み
1. **[Knowledge Center]** に移動し、 **[ギャラリーを参照]** をクリックします。
1. 上部にある **[SQL スクリプト]** タブを選択します。
1. **[Load the New York Taxicab dataset]\(ニューヨークのタクシー データセットを読み込む\)** データ インジェスト サンプルを選択し、 **[続行]** をクリックします。
1. **[SQL プール]** で、 **[既存のプールを選択する]** を選択して **[SQLPOOL1]** を選択し、前に作成した **SQLPOOL1** データベースを選択します。
1. **[スクリプトを開く]** をクリックします。
1. 新しいサンプル SQL スクリプトが開きます。
1. **[実行]**
1. これにより、すべてのニューヨーク市のタクシー データに対して複数のテーブルが作成され、T-SQL COPY コマンドを使用して読み込まれます。 前のクイック スタートの手順でこれらのテーブルを作成済みの場合は、存在しないテーブルの CREATE と COPY を実行するコードだけを選択して実行します。

    > [!NOTE] 
    > 専用 SQL プール (以前の SQL DW) に対する SQL スクリプトのサンプル ギャラリーを使用する場合、使用できるのは、既存の専用 SQL プール (以前の SQL DW) だけです。

## <a name="next-steps"></a>次の手順

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
