---
title: クイック スタート:Synapse Studio を使用して Synapse SQL プールを作成する
description: このガイドの手順に従って、Synapse Studio を使用して新しい Synapse SQL プールを作成します。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f85731a7f3ffef0adf35812ee8da4e0bbd89124f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036575"
---
# <a name="quickstart-create-a-synapse-sql-pool-using-synapse-studio"></a>クイック スタート:Synapse Studio を使用して Synapse SQL プールを作成する

Azure Synapse Analytics には、データの取り込み、変換、モデル化、分析に役立つさまざまな分析エンジンが用意されています。 SQL プールには、T-SQL ベースのコンピューティングとストレージの機能が用意されています。 お使いの Synapse ワークスペースに SQL プールを作成した後、データを読み込み、モデル化し、処理し、提供して、分析情報を迅速に得ることができます。

このクイックスタートでは、Synapse Studio を使用して Synapse ワークスペースで SQL プールを作成する手順を説明します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Synapse ワークスペース](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse ワークスペースに移動する

1. Synapse ワークスペースに移動します。ここでは、検索バーにサービス名 (またはリソース名を直接) 入力して、SQL プールを作成します。
![「Synapse ワークスペース」と入力されている Azure portal の検索バー。](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. ワークスペースの一覧で、開くワークスペースの名前 (または名前の一部) を入力します。 この例では、**contosoanalytics** という名前のワークスペースを使用します。
![名前に Contoso を含むワークスペースを表示するためにフィルター処理された Synapse ワークスペースの一覧。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Synapse Studio を起動する

1. ワークスペースの概要で **[Synapse Studio の起動]** を選択して、SQL プールを作成する場所を開きます。 検索バーにサービス名を入力するか、またはリソース名を直接入力します。
![[Synapse Studio の起動] が強調表示されている Azure portal の Synapse ワークスペースの概要。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Synapse Studio で SQL プールを作成する

1. Synapse Studio のホーム ページで、 **[管理]** アイコンを選択して、左側のナビゲーションの**管理ハブ**に移動します。
![管理ハブ セクションが強調表示されている Synapse Studio のホーム ページ。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. 管理ハブで、 **[SQL プール]** セクションに移動して、ワークスペースで現在使用可能な SQL プールの一覧を表示します。
![[SQL プール] ナビゲーションが選択されている Synapse Studio の管理ハブ](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. **[+ 新規]** コマンドを選択します。新しい SQL プールの作成ウィザードが表示されます。 
![SQL プールの一覧が表示されている Synapse Studio の管理ハブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    | :------ | :-------------- | :---------- |
    | **SQL プール名** | contosoedw | これは、SQL プールの名前です。 |
    | **パフォーマンス レベル** | DW100c | このクイックスタートのコストを削減するために、最小サイズに設定します |

    ![SQL プールの作成フロー - [基本] タブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > SQL プールで使用できる名前には、特定の制限があることに注意してください。 名前には特殊文字を含めることはできず、15 文字以下である必要があり、予約語は使用できません。また、ワークスペース内で一意である必要があります。

4. 次の **[追加設定]** タブで、 **[なし]** を選択して、データを含まない SQL プールをプロビジョニングします。 既定の照合順序は、選択されたままにします。
![SQL プールの作成フロー - 追加設定のタブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. 現時点では、タグを追加しないため、 **[次へ: 確認と作成]** をクリックします。

1. **[確認と作成]** タブで、以前に入力した内容に基づいて詳細が正しいことを確認し、 **[作成]** をクリックします。 
![SQL プールの作成フロー - 設定確認のタブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. この時点で、リソース プロビジョニングのフローが開始されます。

1. プロビジョニングが完了した後にワークスペースに戻ると、新しく作成された SQL プールの新しいエントリが表示されます。
 ![SQL プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. SQL プールが作成されると、ワークスペースでデータの読み込み、ストリームの処理、レイクからの読み取りなどに使用できるようになります。

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Synapse Studio を使用して SQL プールをクリーンアップする    

Synapse Studio を使用してワークスペースから SQL プールを削除するには、次の手順に従います。
> [!WARNING]
> SQL プールを削除すると、ワークスペースから分析エンジンが削除されます。 プールに接続することはできなくなります。また、この SQL プールを使用するすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

SQL プールを削除する場合は、次の手順を行います。

1. Synapse Studio で管理ハブにある SQL プールに移動します。
1. 削除する SQL プールの省略記号 (この場合は、**contosoedw**) を選択して、SQL プールのコマンドを表示します。![最近作成されたプールが選択されている SQL プールの一覧。](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. **[削除]** を押します。
1. 削除を確認し、 **[削除]** を押します。
 ![選択した SQL プールの削除を確認するダイアログ。](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. プロセスが正常に完了すると、SQL プールはワークスペース リソースの一覧に表示されなくなります。

## <a name="next-steps"></a>次のステップ 
- 「[クイック スタート:Apache Spark ノートブックの作成](quickstart-apache-spark-notebook.md)に関するページを参照してください。
- 「[クイック スタート:Azure portal を使用した Synapse SQL プールの作成](quickstart-create-sql-pool-portal.md)。
