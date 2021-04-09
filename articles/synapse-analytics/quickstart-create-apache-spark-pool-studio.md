---
title: 'クイックスタート:'
description: このガイドの手順に従って、Synapse Studio を使用してサーバーレス Apache Spark プールを作成します。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d280e96b354f38a86545ed5f6bcaa6c9a314c46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462030"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-using-synapse-studio"></a>クイックスタート:

Azure Synapse Analytics には、データの取り込み、変換、モデル化、分析、および提供に役立つさまざまな分析エンジンが用意されています。 Apache Spark プールは、オープンソースのビッグ データ コンピューティング機能を提供します。 お使いの Synapse ワークスペースに Apache Spark プールを作成した後、データを読み込み、モデル化し、処理し、提供して分析情報を得ることができます。  

このクイックスタートでは、Synapse Studio を使用して Synapse ワークスペースで Apache Spark プールを作成する手順を説明します。

> [!IMPORTANT]
> Spark インスタンスの料金は、それを使用しているかどうかに関係なく、分単位で課金されます。 必ず、Spark インスタンスの使用を終了した後にシャットダウンするか、短いタイムアウトを設定してください。 詳しくは、この記事の「**リソースのクリーンアップ**」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Synapse ワークスペース](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse ワークスペースに移動する

1. Synapse ワークスペースに移動します。ここでは、検索バーにサービス名 (またはリソース名を直接) 入力して、Apache Spark プールを作成します。

    ![「Synapse ワークスペース」と入力されている Azure portal の検索バー。](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. ワークスペースの一覧で、開くワークスペースの名前 (または名前の一部) を入力します。 この例では、**contosoanalytics** という名前のワークスペースを使用します。

    ![名前に Contoso を含むワークスペースを表示するためにフィルター処理された Synapse ワークスペースの一覧。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Synapse Studio を起動する 

ワークスペースの概要から **[Workspace web URL]\(ワークスペース Web URL\)** を選択して Synapse Studio を開きます。

![[Synapse Studio の起動] が強調表示されている Azure portal の Synapse ワークスペースの概要。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Synapse Studio で Apache Spark プールを作成する

1. Synapse Studio のホーム ページで、 **[管理]** アイコンを選択して、左側のナビゲーションの **管理ハブ** に移動します。

    ![管理ハブ セクションが強調表示されている Synapse Studio のホーム ページ。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. 管理ハブで、 **[Apache Spark プール]** セクションに移動して、ワークスペースで現在使用可能な Apache Spark プールの一覧を表示します。
    
    ![[Apache Spark プール] ナビゲーションが選択されている Synapse Studio の管理ハブ](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. **[+ 新規]** を選択します。新しい Apache Spark プールの作成ウィザードが表示されます。 

1. **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    | :------ | :-------------- | :---------- |
    | **[Apache Spark pool name]\(Apache Spark プール名\)** | contosospark | これは、Apache Spark プールの名前です。 |
    | **ノード サイズ** | Small (4 vCPU / 32 GB) (S (4 vCPU/32 GB)) | このクイックスタートのコストを削減するために、最小サイズに設定します |
    | **Autoscale** | 無効 | このクイックスタートでは、自動スケーリングは必要ありません |
    | **[Number of nodes]\(ノードの数\)** | 8 | このクイックスタートでは、コストを制限するために小さいサイズを使用します|
       
    ![Synapse Studio の新しい Apache Spark プールの基本](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Apache Spark プールで使用できる名前には、特定の制限があることに注意してください。 名前は、文字または数字のみを含み、15 文字以下である必要があります。さらに、文字で始まり、予約語を含まず、ワークスペース内で一意である必要があります。

1. 次のタブ ([追加設定]) では、すべての設定を既定値のままにします。

1. 現時点では、タグを追加しないため、 **[確認と作成]** を選択します。

1. **[確認と作成]** タブで、前に入力した内容に基づいて詳細に間違いがないことを確認し、 **[作成]** をクリックします。 

    ![Synapse Studio の新しい Apache Spark プールを作成する](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Apache Spark プールのプロビジョニング プロセスが開始されます。

1. プロビジョニングが完了すると、新しい Apache Spark プールが一覧に表示されます。
    
    ![Synapse Studio の新しい Apache Spark プールの一覧](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Synapse Studio を使用して Apache Spark プールのリソースをクリーンアップする

Synapse Studio を使用してワークスペースから Apache Spark プールを削除するには、次の手順に従います。
> [!WARNING]
> Spark プールを削除すると、ワークスペースから分析エンジンが削除されます。 プールに接続することはできなくなります。また、この Spark プールを使用するすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

Apache Spark プールを削除する場合は、次の手順を実行します。

1. Synapse Studio で管理ハブにある Apache Spark プールに移動します。
1. 削除する Apache プールの横にある省略記号 (この場合は、**contosospark**) を選択して、Apache Spark プールのコマンドを表示します。

    ![Apache Spark プールの一覧。最近作成されたプールが選択されています。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. **[削除]** を押します。
1. 削除を確認し、 **[削除]** を押します。
1. プロセスが正常に完了すると、Apache Spark プールはワークスペース リソースの一覧に表示されなくなります。 

## <a name="next-steps"></a>次のステップ

- 「[クイック スタート:Web ツールを使用した Synapse Studio での Apache Spark プールの作成](quickstart-apache-spark-notebook.md)。
- 「[クイック スタート:Azure portal を使用して Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)。
