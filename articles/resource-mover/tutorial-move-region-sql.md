---
title: Azure Resource Mover を使用して Azure SQL リソースをリージョン間で移動する
description: Azure Resource Mover を使用して Azure SQL リソースを別のリージョンに移動する方法について説明します
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4678a6128be13ac61dc4ac67bbd1a17e99c6d24d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820231"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>チュートリアル:Azure SQL Database リソースを別のリージョンに移動する

このチュートリアルでは、[Azure Resource Mover](overview.md) を使用して、Azure SQL データベースとエラスティック プールを別の Azure リージョンに移動する方法について説明します。

> [!NOTE]
> Azure Resource Mover は、現在プレビュー段階にあります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件と要件を確認する。
> * 移動するリソースを選択する。
> * リソースの依存関係を解決する。
> * SQL Server を準備し、ターゲット リージョンに移動する。
> * データベースとエラスティック プールを準備して移動する。
> * 移動を破棄するか、コミットするかを決定する。 
> * 必要に応じて、移動後にソース リージョンのリソースを削除する。 

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、既定のオプションを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。 次に、[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="prerequisites"></a>前提条件

-  移動するリソースを含むサブスクリプションに "*所有者*" アクセス権があることを確認します。
    - Azure サブスクリプションの特定のソースと宛先のペアに対してリソースを初めて追加すると、Resource Mover では、サブスクリプションによって信頼されている[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称: Managed Service ID (MSI)) が作成されます。
    - ID を作成し、必要なロール (ソース サブスクリプションの共同作成者またはユーザー アクセス管理者) に割り当てるには、リソースを追加するのに使用するアカウントに、サブスクリプションに対する "*所有者*" 権限が必要です。 Azure ロールの詳細については、[こちらを参照してください](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
- サブスクリプションには、ターゲット リージョンで移動するリソースを作成するのに十分なクォータが必要です。 クォータがない場合は、[追加の制限](../azure-resource-manager/management/azure-subscription-service-limits.md)を要求します。
- リソースの移動先となるターゲット リージョンに関連付けられている料金と課金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると便利です。
    

## <a name="check-sql-requirements"></a>SQL の要件を確認する

1. 別のリージョンへの移動でサポートされているデータベースまたはエラスティック プールの機能を[確認](support-matrix-move-region-sql.md)します。
2. ターゲット リージョンで、各ソース サーバーに対してターゲット サーバーを作成します。 [詳細については、こちらを参照してください](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users)。
4. データベースが Transparent Data Encryption (TDE) を使用して暗号化されており、Azure Key Vault で独自の暗号化キーを使用する場合は、キー コンテナーを別のリージョンに移動する[方法を参照](../key-vault/general/move-region.md)してください。
5. SQL データ同期が有効になっている場合は、メンバー データベースの移動がサポートされています。 移動後、新しいターゲット データベースへの SQL データ同期を設定する必要があります。
6. 移動前に、高度なデータ セキュリティ設定を削除します。 移動後、ターゲット リージョンの SQL Server レベルで[設定を構成](../azure-sql/database/azure-defender-for-sql.md)します。
7. 監査が有効になっている場合は、移動後にポリシーが既定にリセットされます。 移動後に再度[監査を設定](../azure-sql/database/auditing-overview.md)します。
7. ソース データベースのバックアップ保有ポリシーは、ターゲット データベースに引き継がれます。 移動後に設定を変更する方法の詳細について、[こちらを参照してください](../azure-sql/database/long-term-backup-retention-configure.md)。
8. 移動前に、サーバーレベルのファイアウォール規則を削除します。 移動中に、データベースレベルのファイアウォール規則がソース サーバーからターゲット サーバーにコピーされます。 移動後、ターゲット リージョンの SQL Server に対して[ファイアウォール規則を設定](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)します。
9. 移動前に自動チューニング設定を削除します。 移行後に再度[チューニングを設定](../azure-sql/database/automatic-tuning-enable.md)します。
10. 移動前に、データベースのアラート設定を削除します。 移動後に[リセット](../azure-sql/database/alerts-insights-configure-portal.md)します。
    
## <a name="select-resources"></a>リソースを選択する

移動するリソースを選択します。

- 選択したソース リージョンの任意のリソース グループで、サポートされている任意のリソースの種類を選択できます。
- ソース リージョンと同じサブスクリプションのターゲット リージョンにリソースを移動します。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

1. Azure portal で、*Resource Mover* を検索します。 その後、 **[サービス]** で **[Azure Resource Mover]** を選択します。

     ![Azure portal での Resource Mover の検索結果](./media/tutorial-move-region-sql/search.png)

2. **[概要]** で、 **[開始]** をクリックします。

    ![別のリージョンに移動するリソースを追加するボタン](./media/tutorial-move-region-sql/get-started.png)

3. **[リソースの移動]**  >  **[Source + destination]\(ソース + 宛先\)** で、ソース サブスクリプションおよびリージョンを選択します。
4. **[宛先]** で、リソースの移動先となるリージョンを選択します。 続けて、 **[次へ]** をクリックします。

    ![ソースと宛先のリージョンを選択するページ](./media/tutorial-move-region-sql/source-target.png)

6. **[移動するリソース]** で、 **[リソースの選択]** をクリックします。
7. **[リソースの選択]** で、リソースを選択します。 移動がサポートされているリソースのみを追加できます。 次に、 **[Done]** をクリックします。

    ![移動する SQL リソースを選択するためのページ](./media/tutorial-move-region-sql/select-resources.png)

8. **[移動するリソース]** で、 **[次へ]** をクリックします。

9. **[確認 + 追加]** で、ソースと宛先の設定を確認します。 移動に関するメタデータが、メタデータ リージョン内のこの目的のために作成されたリソース グループに格納されることを理解していることを確認します。


    ![設定を確認し、移動を続行するためのページ](./media/tutorial-move-region-sql/review.png)

10. **[続行]** をクリックして、リソースの追加を開始します。
11. 追加プロセスが正常に完了した後、通知アイコンの **[Adding resources for move]\(移動するリソースの追加\)** をクリックします。
12. 通知をクリックした後、 **[Across regions]\(リージョン間\)** ページでリソースを確認します。


> [!NOTE]
> 
> - これで、SQL Server は "*手動割り当てが保留中*" の状態になります。
> - 追加されたその他のリソースは、"*準備が保留中*" の状態にあります。
> - 移動コレクションからリソースを削除する場合、その方法は移動プロセスのどの段階であるかによって異なります。 [詳細については、こちらを参照してください](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>依存関係を解決する


1. **[Across regions]\(リージョン間\)** で、リソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンをクリックします。 検証プロセスが開始されます。
2. 依存関係が見つかった場合は、 **[依存関係の追加]** をクリックします。

    ![依存関係を追加するためのボタン](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. **[依存関係の追加]** で、依存リソース、 **[依存関係の追加]** の順に選択します。 通知で進行状況を監視します。

4. 必要に応じて依存関係をさらに追加し、依存関係をもう一度検証します。 

5. **[Across regions]\(リージョン間\)** ページで、リソースが現在、"*準備が保留中*" の状態になっており、問題がないことを確認します。

    !["準備が保留中" の状態のリソースを示すページ](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>SQL Server を移動する

ターゲット リージョンでターゲット SQL Server を割り当て、移動をコミットします。

### <a name="assign-a-target-sql-server"></a>ターゲット SQL Server を割り当てる

1. **[Across regions]\(リージョン間\)** で、SQL Server リソースの **[Destination configuration]\(宛先の構成\)** 列にある **[Resource not assigned]\(リソースが割り当てられていません\)** をクリックします。
2. ターゲット リージョンの既存の SQL Server リソースを選択します。 
    
    ![SQL Server の状態が "移動のコミットが保留中" に設定されていることを示すエントリ](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> ソース SQL Server の状態が "*移動のコミットが保留中*" に変わります。 

### <a name="commit-the-sql-server-move"></a>SQL Server の移動をコミットする

1. **[Across regions]\(リージョン間\)** で SQL Server を選択し、 **[Commit move]\(移動のコミット\)** をクリックします。
2. **[Commit resources]\(リソースのコミット\)** で、 **[コミット]** をクリックします。

    ![SQL Server の移動をコミットするためのページ](./media/tutorial-move-region-sql/commit-sql-server.png)

3. 通知バーで移動の進行状況を追跡します。

> [!NOTE]
> コミットが完了すると、SQL Server は "*ソースの削除が保留中*" の状態になります。


## <a name="prepare-resources-to-move"></a>移動するリソースを準備する

ソース SQL Server が移動されたので、他のリソースを移動する準備を行うことができます。

## <a name="prepare-an-elastic-pool"></a>エラスティック プールを準備する

1. **[Across regions]\(リージョン間\)** でソース エラスティック プール (このチュートリアルでは demo-test1-elasticpool) を選択し、 **[準備]** をクリックします。

    ![リソースを準備するためのボタン](./media/tutorial-move-region-sql/prepare-elastic.png)

2. **[リソースの準備]** で、 **[準備]** をクリックします。
3. 準備処理が正常に完了したことを示す通知が表示されたら、 **[更新]** をクリックします。

> [!NOTE]
> これで、エラスティック プールは "*移動の開始が保留中*" の状態になります。

## <a name="prepare-a-single-database"></a>単一のデータベースを準備する

1. **[Across regions]\(リージョン間\)** で単一のデータベースを選択し (エラスティック プール内ではなく)、 **[準備]** をクリックします。

    ![選択したリソースを準備するためのボタン](./media/tutorial-move-region-sql/prepare-db.png)

2. **[リソースの準備]** で、 **[準備]** をクリックします。
3. 準備処理が正常に完了したことを示す通知が表示されたら、 **[更新]** をクリックします。

> [!NOTE]
> これで、データベースは "*移動の開始が保留中*" の状態になり、ターゲット リージョン内に作成されました。


## <a name="move-the-pool-and-prepare-pool-databases"></a>プールを移動し、プール データベースを準備する

エラスティック プール内のデータベースを準備するには、エラスティック プールが "*移動のコミットが保留中*" の状態にある必要があります この状態に移行するには、プールの移動を開始します。

#### <a name="initiate-move---elastic-pool"></a>移動の開始 - エラスティック プール

1. **[Across regions]\(リージョン間\)** でソース エラスティック プール (このチュートリアルでは demo-test1-elasticpool) を選択し、 **[移動の開始]** をクリックします。
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。

    
    ![エラスティック プールの移動を開始するためのボタン](./media/tutorial-move-region-sql/initiate-elastic.png)

1. 通知バーで移動の進行状況を追跡します。
1. 移動が正常に完了したことを示す通知が表示されたら、 **[更新]** をクリックします。

> [!NOTE]
> これで、エラスティック プールは "*移動のコミットが保留中*" の状態になります。

#### <a name="prepare-database"></a>データベースを準備する

1. **[Across regions]\(リージョン間\)** でデータベース (このチュートリアルでは demo-test2-sqldb) を選択し、 **[準備]** をクリックします。
2. **[リソースの準備]** で、 **[準備]** をクリックします。

    ![エラスティック プール内のデータベースを準備するためのボタン](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

準備中に、ターゲット リージョン内にターゲット データベースが作成され、データ レプリケーションが開始されます。 準備が完了すると、データベースは "*移動の開始が保留中*" の状態になります。 

![エラスティック プールで、選択したデータベースを準備するためのボタン](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>データベースの移動

データベースの移動を開始します。
1. **[Across regions]\(リージョン間\)** で、"**移動の開始が保留中**" 状態のリソースを選択します。 その後、 **[移動の開始]** をクリックします。
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。

    ![移動を開始するためのページ](./media/tutorial-move-region-sql/initiate-move.png)

3. 通知バーで移動の進行状況を追跡します。

> [!NOTE]
> これで、データベースは "*移動のコミットが保留*" の状態になります。


## <a name="discard-or-commit"></a>破棄またはコミットしますか?

最初の移動後に、移動をコミットするか、破棄するかを決定できます。 

- **破棄**: テストする場合は移動を破棄できますが、実際にはソース リソースを移動したくありません。 移動を破棄すると、リソースが "**移動の開始が保留中**" の状態に戻ります。
- **コミット**:コミットすると、ターゲット リージョンへの移動が完了します。 コミット後、ソース リソースは "**ソースの削除が保留中**" の状態になり、削除するかどうかを決定できます。


## <a name="discard-the-move"></a>移動を破棄する 

移動は次のように破棄できます。

1. **[Across regions]\(リージョン間\)** で、"**移動のコミットが保留中**" 状態のリソースを選択し、 **[移動の破棄]** をクリックします。
2. **[移動の破棄]** で、 **[破棄]** をクリックします。
3. 通知バーで移動の進行状況を追跡します。


> [!NOTE]
> - リソースを破棄した後、リソースは "*移動の開始が保留中*" の状態になります。
> - エラスティック プールのみが存在する場合は、破棄が進行し、ターゲット リージョンに作成されたエラスティック プールが削除されます。
> - データベースが関連付けられているエラスティック プールが "*移動のコミットが保留中*" の状態にある場合、エラスティック プールを破棄することはできません。
> - SQL データベースを破棄しても、ターゲット リージョンのリソースは削除されません。 

破棄後にもう一度移動を開始する場合は、SQL データベースまたはエラスティック プールを選択し、移動を再度開始します。


## <a name="commit-the-move"></a>移動をコミットする

次のように、データベースとエラスティック プールの移動を終了します。


1. SQL Server が "*ソースの削除が保留中*" の状態になっていることを確認します。
2. コミットする前に、データベース接続文字列をターゲット リージョンに更新します。
3. **[Across regions]\(リージョン間\)** で SQL リソースを選択し、 **[Commit move]\(移動のコミット\)** をクリックします。
4. **[Commit resources]\(リソースのコミット\)** で、 **[コミット]** をクリックします。

    ![移動をコミットする](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. 通知バーでコミットの進行状況を追跡します。


> [!NOTE]
> コミット処理中に SQL データベースのダウンタイムが発生することがあります。
> これで、コミットされたデータベースおよびエラスティック プールは "*ソースの削除が保留中*" の状態になります。
> コミット後、ターゲット データベースでファイアウォール規則、ポリシー、アラートなどのデータベース関連の設定を更新します。


## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。 

> [!NOTE]
> SQL Server サーバーは、ポータルからは削除できません。リソースのプロパティ ページから削除する必要があります。

1. **[Across Regions]\(リージョン間\)** で、削除するソース リソースの名前をクリックします。
2. **[ソースの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure SQL データベースを別の Azure リージョンに移動しました。
> * Azure SQL エラスティック プールを別のリージョンに移動しました。

ここで、Azure VM を別のリージョンに移動しようとしています。

> [!div class="nextstepaction"]
> [Azure VM の移動](./tutorial-move-region-virtual-machines.md)