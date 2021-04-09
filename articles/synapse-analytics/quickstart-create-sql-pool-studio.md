---
title: 'クイックスタート: Synapse Studio を使用して専用 SQL プールを作成する'
description: このガイドの手順に従って、Synapse Studio を使用して専用 SQL プールを作成します。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541846"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>クイックスタート: Synapse Studio を使用して専用 SQL プールを作成する

Azure Synapse Analytics には、データの取り込み、変換、モデル化、分析に役立つさまざまな分析エンジンが用意されています。 専用 SQL プールには、T-SQL ベースのコンピューティングとストレージの機能が用意されています。 お使いの Synapse ワークスペースに専用 SQL プールを作成した後、データを読み込み、モデル化し、処理し、提供して、分析情報を迅速に得ることができます。

このクイックスタートでは、Synapse Studio を使用して Synapse ワークスペースで専用 SQL プールを作成する手順を説明します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Synapse ワークスペース](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse ワークスペースに移動する

1. Synapse ワークスペースに移動します。ここでは、検索バーにサービス名 (またはリソース名を直接) 入力して、専用 SQL プールを作成します。

    ![「Synapse ワークスペース」と入力されている Azure portal の検索バー。](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. ワークスペースの一覧で、開くワークスペースの名前 (または名前の一部) を入力します。 この例では、**contosoanalytics** という名前のワークスペースを使用します。

    ![名前に Contoso を含むワークスペースを表示するためにフィルター処理された Synapse ワークスペースの一覧。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Synapse Studio を起動する

1. ワークスペースの概要から **[Workspace web URL]\(ワークスペース Web URL\)** を選択して Synapse Studio を起動します。

    ![[Workspace web URL]\(ワークスペース Web URL\) が強調表示されている Azure portal Synapse ワークスペースの概要。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Synapse Studio で専用 SQL プールを作成する

1. Synapse Studio のホーム ページで、 **[管理]** アイコンを選択して、左側のナビゲーションの **管理ハブ** に移動します。

    ![管理ハブ セクションが強調表示されている Synapse Studio のホーム ページ。](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. 管理ハブで、 **[SQL プール]** セクションに移動して、ワークスペースで現在使用可能な SQL プールの一覧を表示します。

    ![[SQL プール] ナビゲーションが選択されている Synapse Studio の管理ハブ](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. **[+ 新規]** コマンドを選択します。新しい SQL プールの作成ウィザードが表示されます。 

    ![SQL プールの一覧が表示されている Synapse Studio の管理ハブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    | :------ | :-------------- | :---------- |
    | **SQL プール名** | contosoedw | これは、専用 SQL プールの名前です。 |
    | **パフォーマンス レベル** | DW100c | このクイックスタートのコストを削減するために、最小サイズに設定します |

    ![SQL プールの作成フロー - [基本] タブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > 専用 SQL プールで使用できる名前には、特定の制限があることに注意してください。 名前には特殊文字を含めることはできず、15 文字以下である必要があり、予約語は使用できません。また、ワークスペース内で一意である必要があります。

4. 次の **[追加設定]** タブで、 **[なし]** を選択して、データを含まない SQL プールをプロビジョニングします。 既定の照合順序は、選択されたままにします。

    復元ポイントから専用の SQL プールを復元する場合は、 **[復元ポイント]** を選択します。 復元を実行する方法の詳細については、[既存の専用 SQL プールを復元する方法](backuprestore/restore-sql-pool.md)に関するページをご覧ください

    ![SQL プールの作成フロー - 追加設定のタブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. ここではタグを追加しないため、次に **[確認と作成]** を選択します。

1. **[確認と作成]** タブで、以前に入力した内容に基づいて詳細が正しいことを確認し、 **[作成]** をクリックします。 

    ![SQL プールの作成フロー - 設定確認のタブ。](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. この時点で、リソース プロビジョニングのフローが開始されます。

1. プロビジョニングが完了した後にワークスペースに戻ると、新しく作成された SQL プールの新しいエントリが表示されます。

    ![SQL プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. 専用 SQL プールが作成されると、データの読み込み、ストリームの処理、レイクからの読み取りなどのために、ワークスペースで使用できるようになります。

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Synapse Studio を使用して専用 SQL プールをクリーンアップする    

Synapse Studio を使用してワークスペースから専用 SQL プールを削除するには、次の手順に従います。
> [!WARNING]
> 専用 SQL プールを削除すると、ワークスペースから分析エンジンが削除されます。 プールに接続することはできなくなります。また、この専用 SQL プールを使用するすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

専用 SQL プールを削除する場合は、次の手順を行います。

1. Synapse Studio で管理ハブにある SQL プールに移動します。
1. 削除する専用 SQL プールの省略記号 (この場合は、**contosoedw**) を選択して、専用 SQL プールのコマンドを表示します。

    ![最近作成されたプールが選択されている SQL プールの一覧。](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. **[削除]** を押します。
1. 削除を確認し、 **[削除]** を押します。
1. プロセスが正常に完了すると、専用 SQL プールはワークスペース リソースの一覧に表示されなくなります。

## <a name="next-steps"></a>次のステップ
 
- 「[クイック スタート:Apache Spark ノートブックの作成](quickstart-apache-spark-notebook.md)に関するページを参照してください。
- 「[クイック スタート:Azure portal を使用して専用 SQL プールを作成する](quickstart-create-sql-pool-portal.md)」を参照してください。
