---
title: クイック スタート:Azure portal を使用して Synapse SQL プール (プレビュー) を作成する
description: このガイドの手順に従って、Azure portal を使用して新しい Synapse SQL プールを作成します。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 768b559ddaf7364726cb26a34d12f357d4126426
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274256"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Synapse SQL プール (プレビュー) を作成する

Azure Synapse Analytics には、データの取り込み、変換、モデル化、分析に役立つさまざまな分析エンジンが用意されています。 SQL プールには、T-SQL ベースのコンピューティングとストレージの機能が用意されています。 お使いの Synapse ワークスペースに SQL プールを作成した後、データを読み込み、モデル化し、処理し、提供して、分析情報を迅速に得ることができます。

このクイックスタートでは、Azure portal を使用して Synapse ワークスペースに SQL プールを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Synapse ワークスペース](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse ワークスペースに移動する

1. Synapse ワークスペースに移動します。ここでは、検索バーにサービス名 (またはリソース名を直接) 入力して、SQL プールを作成します。
![「Synapse ワークスペース」と入力されている Azure portal の検索バー。](media/quickstart-create-sql-pool/create-sql-pool-00a.png) 
1. ワークスペースの一覧で、開くワークスペースの名前 (または名前の一部) を入力します。 この例では、**contosoanalytics** という名前のワークスペースを使用します。
![名前に Contoso を含むワークスペースを表示するためにフィルター処理された Synapse ワークスペースの一覧。](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-sql-pool"></a>新しい SQL プールを作成する

1. SQL プールを作成する Synapse ワークスペースで、上部のバーにある **[新しい SQL プール]** コマンドをクリックします。
![新しい SQL プールを作成するためのコマンドが赤い枠で囲まれた Synapse ワークスペースの概要。](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    | :------ | :-------------- | :---------- |
    | **SQL プール名** | 有効な名前 | SQL プールの名前。 |
    | **パフォーマンス レベル** | DW100c | このクイックスタートのコストを削減するために、最小サイズに設定します |

  
    ![SQL プールの作成フロー - [基本] タブ。](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)
    > [!IMPORTANT]
    > SQL プールで使用できる名前には、特定の制限があることに注意してください。 名前には特殊文字を含めることはできず、15 文字以下である必要があり、予約語は使用できません。また、ワークスペース内で一意である必要があります。

3. **[Next:追加設定]** を選択します。
4. データなしで SQL プールをプロビジョニングするには、 **[None]\(なし\)** を選択します。 既定の照合順序を選択したままにします。
![SQL プールの作成フロー - 追加設定のタブ。](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. **[Review + create]\(レビュー + 作成\)** を選択します。
6. 以前に入力した内容に基づいて詳細が正しいことを確認します。 **［作成］** を選択します
![SQL プールの作成フロー - 設定確認のタブ。](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. この時点で、リソース プロビジョニングのフローが開始されます。
 ![SQL プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. プロビジョニングが完了した後にワークスペースに戻ると、新しく作成された SQL プールの新しいエントリが表示されます。
 ![SQL プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-sql-pool/create-sql-pool-portal-07.png)


SQL プールが作成されると、データの読み込み、ストリームの処理、レイクからの読み取りなどのために、ワークスペースで使用できるようになります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、ワークスペースから SQL プールを削除します。
> [!WARNING]
> SQL プールを削除すると、そのワークスペースから、分析エンジンと、削除された SQL プールのデータベースに格納されているデータの両方が削除されます。 SQL プールに接続することはできなくなります。また、この SQL プールに対して読み取りまたは書き込みを行うすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

SQL プールを削除するには、次の手順を完了します。

1. ワークスペース ブレードの [SQL プール] ブレードに移動します。
1. 削除する SQL プール (この場合は **contosowdw**) を選択します
1. 選択したら、 **[削除]** を押します
1. 削除を確認し、 **[削除]** ボタンを押します![SQL プールの概要 - 削除の確認が強調表示されています。](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. プロセスが正常に完了すると、SQL プールはワークスペース リソースの一覧に表示されなくなります。

## <a name="next-steps"></a>次のステップ

- 「[クイック スタート:Web ツールを使用した Synapse Studio での Apache Spark プールの作成](quickstart-apache-spark-notebook.md)。
- 「[クイック スタート:Azure portal を使用して Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)。
