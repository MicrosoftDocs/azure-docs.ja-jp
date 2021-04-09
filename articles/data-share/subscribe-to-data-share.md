---
title: チュートリアル:データの受け入れと受信 - Azure Data Share
description: チュートリアル - Azure Data Share を使用したデータの受け入れと受信
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017051"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>チュートリアル:Azure Data Share を使用したデータの受け入れと受信  

このチュートリアルでは、Azure Data Share を使用してデータ共有の招待を受け入れる方法を学習します。 自分が共有しているデータを受信する方法と、自分が共有しているデータのスナップショットを常に最新に保つために通常の更新間隔を有効にする方法を学習します。 

> [!div class="checklist"]
> * Azure Data Share の招待を受け入れる方法
> * Azure Data Share アカウントを作成する
> * 自分のデータの受信先を指定する
> * スケジュールされた更新のため、自分のデータ共有のサブスクリプションを作成する

## <a name="prerequisites"></a>前提条件
データ共有の招待を受け入れる前に、次に一覧表示されている多くの Azure リソースをプロビジョニングする必要があります。 

データ共有の招待を受け入れる前に、すべての前提条件を満たしていることを確認します。 

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Share の招待:" **<yourdataprovider@domain.com>** からの Azure Data Share の招待" という件名の Microsoft Azure からの招待。
* Data Share リソースの作成先となる Azure サブスクリプションとターゲット Azure データ ストアがある Azure サブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録します。

### <a name="receive-data-into-a-storage-account"></a>ストレージ アカウントへのデータの受信

* Azure Storage アカウント: [Azure Storage アカウント](../storage/common/storage-account-create.md)をまだお持ちでない場合は、作成できます。 
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。  

### <a name="receive-data-into-a-sql-based-target"></a>SQL ベースのターゲットへのデータの受信
Azure SQL Database、Azure Synapse Analytics へのデータを受信することを選択した場合、前提条件の一覧を以下に示します。 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW) にデータを受信するための前提条件
[ステップ バイ ステップのデモ](https://youtu.be/aeGISgK1xro)に従って、前提条件を構成できます。

* Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW)。
* SQL サーバー上のデータベースに書き込む権限。これは、*Microsoft.Sql/servers/databases/write* に含まれています。 このアクセス許可は、**共同作成者** ロール内に存在します。 
* Data Share リソースのマネージド ID が Azure SQL Database または Azure Synapse Analytics にアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で、SQL サーバーに移動し、自分自身を **Azure Active Directory 管理者** に設定します。
    1. [クエリ エディター](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)、または Azure Active Directory 認証を使用する SQL Server Management Studio を使用して Azure SQL Database/Data Warehouse に接続します。 
    1. 次のスクリプトを実行し、Data Share のマネージド ID を "db_datareader、db_datawriter、db_ddladmin" として追加します。 SQL Server 認証ではなく Active Directory を使用して接続する必要があります。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。         

* SQL Server ファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal の SQL サーバーで、 *[ファイアウォールと仮想ネットワーク]* に移動します。
    1. **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** で *[はい]* をクリックします。
    1. **[+クライアント IP の追加]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL データを共有するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。
    1. **[保存]** をクリックします。 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics (ワークスペース) の SQL プールにデータを受信するための前提条件

* Azure Synapse Analytics (ワークスペース) の専用 SQL プール。 サーバーレス SQL プールへのデータの受信は、現在サポートされていません。
* Synapse ワークスペースの SQL プールに対する書き込みアクセス許可。これは、*Microsoft.Synapse/workspaces/sqlPools/write* にあります。 このアクセス許可は、**共同作成者** ロール内に存在します。
* Data Share リソースのマネージド ID が Synapse ワークスペースの SQL プールにアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で Synapse ワークスペースに移動します。 左側のナビゲーションから SQL Active Directory 管理者を選択し、自分自身を **Azure Active Directory 管理者** に設定します。
    1. Synapse Studio を開き、左側のナビゲーションから *[管理]* を選択します。 [セキュリティ] で *[アクセス制御]* を選択します。 **SQL 管理者** または **ワークスペース管理者** ロールを自分に割り当てます。
    1. Synapse Studio で、左側のナビゲーションから *[開発]* を選択します。 SQL プールで次のスクリプトを実行して、Data Share リソースのマネージド ID を "db_datareader、db_datawriter、db_ddladmin" として追加します。 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。  

* Synapse ワークスペースのファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で Synapse ワークスペースに移動します。 左側のナビゲーションから *[ファイアウォール]* を選択します。
    1. *[Azure サービスおよびリソースに、このワークスペースへのアクセスを許可する]* で **[オン]** をクリックします。
    1. **[+クライアント IP の追加]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL データを共有するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。
    1. **[保存]** をクリックします。 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターへのデータの受信:  

* データ プロバイダーの Data Explorer クラスターと同じ Azure データ センター内の Azure Data Explorer クラスター: まだお持ちでない場合は、[Azure Data Explorer クラスター](/azure/data-explorer/create-cluster-database-portal)を作成できます。 データ プロバイダーのクラスターの Azure データ センターがわからない場合は、後でこの手順の中でクラスターを作成できます。
* Azure Data Explorer クラスターに書き込む権限。これは、*Microsoft.Kusto/clusters/write* に含まれています。 この権限は、投稿者ロール内に存在します。 
* Azure Data Explorer クラスターにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="open-invitation"></a>招待を開く

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 招待は、メールから開くことができるほか、Azure portal から直接開くこともできます。 

   招待をメールから開くには、受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " **<yourdataprovider@domain.com> からの Azure Data Share の招待**" になっています。 **[招待を表示]** をクリックして、Azure で招待を確認します。 

   Azure portal から直接招待を開くには、Azure portal で **[データ共有への招待]** を検索します。 この操作により、Data Share の招待の一覧が表示されます。

   ![招待の一覧](./media/invitations.png "招待の一覧") 

1. 表示する共有を選択します。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 環境を準備し、招待を表示します。

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) コマンドを実行して、現在の招待を表示します。

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

次のセクションで使用するために招待 ID をコピーします。

---

## <a name="accept-invitation"></a>招待を受け入れる

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. **[使用条件]** を含む、すべてのフィールドを確認してください。 使用条件に同意する場合は、同意を示すボックスをオンにする必要があります。 

   ![使用条件](./media/terms-of-use.png "使用条件") 

1. *[Target Data Share Account]\(ターゲット データ共有アカウント\)* の下で、ご自分のデータ共有のデプロイ先となるサブスクリプションとリソース グループを選択します。 

   **[Data Share Account]\(データ共有アカウント\)** フィールドには、既存のデータ共有アカウントがない場合には、 **[新規作成]** を選択します。 それ以外の場合は、自分のデータ共有を受け入れる既存のデータ共有アカウントを選択します。 

   **[Received Share Name]\(受信した共有名\)** フィールドは、データの指定で指定された既定のままにしておくことも、受信した共有のために新しい名前を指定することもできます。 

   利用規約に同意し、受信した共有を管理するための Data Share アカウントを指定したら、 **[Accept and configure]\(受け入れと構成\)** を選択します。 共有サブスクリプションが作成されます。 

   ![受け入れオプション](./media/accept-options.png "受け入れオプション") 

   この操作により、Data Share アカウントに、受信した共有が表示されます。 

   招待を受け入れたくない場合は、*[拒否]* を選択します。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) コマンドを使用して、Data Share を作成します。

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>受信した共有を構成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

データを受信する場所を構成するには、次の手順に従います。

1. **[データセット]** タブを選択します。ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 **[+ ターゲットへのマップ]** を選択し、ターゲット データ ストアを選択します。 

   ![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

1. データを配置するターゲット データ ストアの種類を選択します。 パスと名前が同じターゲット データ ストア内のすべてのデータ ファイルまたはデータ テーブルが上書きされます。 

   インプレース共有の場合は、指定された場所にあるデータ ストアを選択します。 この場所は、データ プロバイダーのソース データ ストアが配置されている Azure データ センターです。 データセットがマップされたら、ターゲット パスのリンクに従ってデータにアクセスできます。

   ![ターゲット ストレージ アカウント](./media/dataset-map-target-sql.png "ターゲット ストレージ") 

1. スナップショットベースの共有の場合、データに定期的な更新を提供するためのスナップショット スケジュールがデータ プロバイダーによって作成されていれば、 **[スナップショット スケジュール]** タブを選択して、スナップショット スケジュールを有効にすることもできます。スナップショット スケジュールの横にあるチェック ボックスをオンにし、 **[+ Enable]\(+ 有効\)** を選択します。

   ![スナップショット スケジュールを有効にする](./media/enable-snapshot-schedule.png "スナップショット スケジュールを有効にする")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

これらのコマンドを使用して、データを受信する場所を構成します。

1. [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) コマンドを実行して、データ セット ID を取得します。

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを実行して、この Data Share のストレージ アカウントを作成します。

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. [az storage account show](/cli/azure/storage/account#az_storage_account_show) コマンドを使用して、ストレージ アカウント ID を取得します。

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. 次のコマンドを使用して、アカウント プリンシパル ID を取得します。

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用して、アカウント プリンシパルのロールの割り当てを作成します。

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. データセット ID に基づいてマッピング用の変数を作成します。

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) コマンドを使用して、データセットのマッピングを作成します。

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) コマンドを実行して、データセットの同期を開始します。

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) コマンドを実行して、同期の一覧を表示します。

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) コマンドを使用して、共有に設定されている同期設定を確認します。

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>スナップショットをトリガーする

### <a name="portal"></a>[ポータル](#tab/azure-portal)

これらの手順は、スナップショットベースの共有にのみ適用されます。

1. スナップショットをトリガーするには、 **[詳細]** タブ、 **[スナップショットのトリガー]** の順に選択します。 これで、データの完全なスナップショットまたは増分スナップショットをトリガーすることができます。 データ プロバイダーから初めてデータを受信する場合は、完全なコピーを選択します。 

   ![スナップショットのトリガー](./media/trigger-snapshot.png "スナップショットのトリガー") 

1. 最終実行状態が "*成功*" の場合、ターゲット データ ストアに移動して、受信したデータを表示します。 **[データセット]** を選択して、ターゲット パスのリンクをクリックしてください。 

   ![コンシューマー データセット](./media/consumer-datasets.png "コンシューマー データセットのマッピング") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) コマンドを実行して、スナップショットをトリガーします。

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> このコマンドは、スナップショットベースの共有にのみ使用します。

---

## <a name="view-history"></a>履歴を表示する
この手順は、スナップショットベースの共有にのみ適用されます。 スナップショットの履歴を表示するには、 **[履歴]** タブを選択します。ここには、過去 30 日間に生成されたすべてのスナップショットの履歴があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが不要になったら、**Data Share の [概要]** ページに移動し、 **[削除]** を選択して削除します。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Data Share の受け入れ方法と受信方法について学習しました。 Azure Data Share の概念の詳細については、Azure Data Share の用語に進んでください。

> [!div class="nextstepaction"]
> [Azure Data Share の概念](terminology.md)