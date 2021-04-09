---
title: 'クイックスタート: 概要 - Synapse ワークスペースを作成する'
description: このチュートリアルでは、Synapse ワークスペース、専用 SQL プール、サーバーレス Apache Spark プールを作成する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171485"
---
# <a name="creating-a-synapse-workspace"></a>Synapse ワークスペースの作成

このチュートリアルでは、Synapse ワークスペース、専用 SQL プール、サーバーレス Apache Spark プールを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、**所有者** ロールが割り当てられているリソース グループにアクセスできる必要があります。 このリソース グループで Synapse ワークスペースを作成します。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure portal で Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、Enter キーを押さずに検索バーに「**Synapse**」と入力します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics]** を選択します。
1. **[追加]** を選択してワークスペースを作成します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、次のフィールドに入力します。
      1. **[サブスクリプション]** - 任意のサブスクリプションを選択します。
      2. **[リソース グループ]** - 任意のリソース グループを使用します。
      3. **[リソース グループ]** - 空白のままにします。
1. **[基本]** タブの **[ワークスペースの詳細]** で、次のフィールドに入力します。
      1. **[ワークスペース名]** - グローバルに一意の任意の名前を選択します。 このチュートリアルでは、**myworkspace** を使用します。
      1. **[リージョン]** - 任意のリージョンを選択します。
      1. **Data Lake Storage Gen 2 の選択**
        1. **[From subscription]\(サブスクリプションから\)** ボタンをクリックします。
        1. **[アカウント名]** で、 **[新規作成]** をクリックし、新しいストレージ アカウントに **contosolake** などの名前を付けます。この名前は一意である必要があります。
        1. **[File system name]\(ファイル システム名\)** で、 **[新規作成]** をクリックし、**users** という名前を付けます。 これにより、**users** というストレージ コンテナーが作成されます。 ワークスペースでは、このストレージ アカウントを Spark テーブルおよび Spark アプリケーション ログの "プライマリ" ストレージ アカウントとして使用します。
        1. "Data Lake Storage Gen2 アカウントのストレージ BLOB データ共同作成者ロールを自分に割り当てる" ためのボックスをオンにします。 
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

> [!NOTE]
> 既存の専用 SQL プール (以前の SQL DW) のワークスペース機能を有効にするには、[専用の SQL プール (以前の SQL DW) 用のワークスペースを有効にする方法](./sql-data-warehouse/workspace-connected-create.md)に関するページを参照してください。


## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開き、Synapse ワークスペースの **[概要]** セクションで、[Synapse Studio の起動] ボックスの **[開く]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。


## <a name="the-built-in-serverless-sql-pool"></a>組み込みのサーバーレス SQL プール

各ワークスペースは、**組み込み** と呼ばれる、事前構築されたサーバーレス SQL プールを備えています。 このプールを削除することはできません。 サーバーレス SQL プールでは、専用 SQL プールで容量を予約しなくても SQL を使用できます。 専用 SQL プールとは異なり、サーバーレス SQL プールの課金は、プールに割り当てられた容量の数ではなく、クエリを実行するためにスキャンされたデータの量に基づきます。


## <a name="create-a-dedicated-sql-pool"></a>専用 SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択します。
1. **[SQL プール名]** で **[SQLPOOL1]** を選択します。
1. **[パフォーマンス レベル]** で **[DW100C]** を選択します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 専用 SQL プールの準備は数分で完了します。 専用 SQL プールは、**SQLPOOL1** とも呼ばれる専用 SQL プール データベースに関連付けられます。

専用 SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

> [!NOTE] 
> ワークスペースに新しい専用 SQL プール (以前の SQL DW) を作成すると、専用の SQL プールのプロビジョニング ページが開きます。 プロビジョニングは、論理 SQL サーバー上で行われます。


## <a name="create-a-serverless-apache-spark-pool"></a>サーバーレス Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択します。 
1. **[Apache Spark プール名]** に「**Spark1**」と入力します。
1. **[ノード サイズ]** に「**Small**」と入力します。
1. **[ノード数]** で、最小数を 3 に、最大数を 3 に設定します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

Spark プールから Azure Synapse に対して、使用する Spark リソースの数が指示されます。 支払いの対象となるのは、使用したリソースの分だけです。 プールの使用を能動的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [専用 SQL プールを使用して分析する](get-started-analyze-sql-pool.md)
