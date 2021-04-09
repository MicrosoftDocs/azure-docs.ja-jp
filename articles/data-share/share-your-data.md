---
title: チュートリアル:組織の外部と共有する - Azure Data Share
description: チュートリアル - Azure Data Share を使用して顧客やパートナーとデータを共有する
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659626"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>チュートリアル:Azure Data Share を使用したデータの共有  

このチュートリアルでは、新しい Azure Data Share を設定して、Azure 組織の外部の顧客やパートナーとのデータの共有を開始する方法を学習します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * データ共有を作成する。
> * データ共有にデータセットを追加する。
> * データ共有のスナップショット スケジュールを有効にする。 
> * データ共有に受信者を追加する。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* 受信者の Azure ログイン用メール アドレス (メール エイリアスは無効です)。
* Data Share リソースの作成用とは異なる Azure サブスクリプションにソース Azure データ ストアが存在する場合、Azure データ ストアがあるサブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録してください。 

### <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有

* Azure Storage アカウント: まだお持ちでない場合は、[Azure Storage アカウント](../storage/common/storage-account-create.md)を作成できます。
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 このアクセス許可は、**共同作成者** ロール内に存在します。
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 このアクセス許可は、**所有者** ロール内に存在します。 


### <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有
SQL ソースからデータを共有するための前提条件の一覧を次に示します。 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW) から共有するための前提条件
[ステップ バイ ステップのデモ](https://youtu.be/hIE-TjJD8Dc)に従って、前提条件を構成できます。

* 共有するテーブルとビューを含む Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW)。
* SQL サーバー上のデータベースに書き込む権限。これは、*Microsoft.Sql/servers/databases/write* に含まれています。 このアクセス許可は、**共同作成者** ロール内に存在します。
* Data Share リソースのマネージド ID がデータベースにアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で、SQL サーバーに移動し、自分自身を **Azure Active Directory 管理者** に設定します。
    1. [クエリ エディター](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)、または Azure Active Directory 認証を使用する SQL Server Management Studio を使用して Azure SQL Database/Data Warehouse に接続します。 
    1. 次のスクリプトを実行し、Data Share リソースのマネージド ID を db_datareader として追加します。 SQL Server 認証ではなく Active Directory を使用して接続する必要があります。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。  

* 共有するテーブルまたはビューに移動して選択するための **"db_datareader"** アクセス権を持つ Azure SQL Database ユーザー。 

* SQL Server ファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で SQL サーバーに移動します。 左側のナビゲーションから *[ファイアウォールと仮想ネットワーク]* を選択します。
    1. **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** で *[はい]* をクリックします。
    1. **[+クライアント IP の追加]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL データを共有するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。
    1. **[保存]** をクリックします。 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics (ワークスペース) の SQL プールから共有するための前提条件

* * 共有するテーブルを含む Azure Synapse Analytics (ワークスペース) の専用 SQL プール。 ビューの共有は現在サポートされていません。 サーバーレス SQL プールからの共有は、現在サポートされていません。
* Synapse ワークスペースの SQL プールに対する書き込みアクセス許可。これは、*Microsoft.Synapse/workspaces/sqlPools/write* にあります。 このアクセス許可は、**共同作成者** ロール内に存在します。
* Data Share リソースのマネージド ID が Synapse ワークスペースの SQL プールにアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で Synapse ワークスペースに移動します。 左側のナビゲーションから SQL Active Directory 管理者を選択し、自分自身を **Azure Active Directory 管理者** に設定します。
    1. Synapse Studio を開き、左側のナビゲーションから *[管理]* を選択します。 [セキュリティ] で *[アクセス制御]* を選択します。 **SQL 管理者** または **ワークスペース管理者** ロールを自分に割り当てます。
    1. Synapse Studio で、左側のナビゲーションから *[開発]* を選択します。 SQL プールで次のスクリプトを実行して、Data Share リソースのマネージド ID を db_datareader として追加します。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。  

* Synapse ワークスペースのファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal で Synapse ワークスペースに移動します。 左側のナビゲーションから *[ファイアウォール]* を選択します。
    1. *[Azure サービスおよびリソースに、このワークスペースへのアクセスを許可する]* で **[オン]** をクリックします。
    1. **[+クライアント IP の追加]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL データを共有するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。
    1. **[保存]** をクリックします。 


### <a name="share-from-azure-data-explorer"></a>Azure Data Explorer からの共有
* 共有したいデータベースを含んだ Azure Data Explorer クラスター。
* Azure Data Explorer クラスターに書き込む権限。これは、*Microsoft.Kusto/clusters/write* に含まれています。 このアクセス許可は、**共同作成者** ロール内に存在します。
* Azure Data Explorer クラスターにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 このアクセス許可は、**所有者** ロール内に存在します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure リソース グループに Azure Data Share リソースを作成します。

1. ポータルの左上隅にあるメニュー ボタンを選択し、 **[リソースの作成]** (+) を選択します。

1. *Data Share* を検索します。

1. Data Share を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本的な詳細に、次の情報を入力します。 

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | Data Share アカウントに使う Azure サブスクリプションを選択します。|
    | リソース グループ | *testresourcegroup* | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 場所 | *米国東部 2* | Data Share アカウントのリージョンを選択します。
    | 名前 | *datashareaccount* | Data Share アカウントの名前を指定します。 |
    | | |

1. **[Review + create]\(レビュー + 作成\)** を選択し、 **[作成]** を選択して Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分以下です。 

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure リソース グループに Azure Data Share リソースを作成します。

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

以下のコマンドを使用してリソースを作成します。

1. [az account set](/cli/azure/account#az_account_set) コマンドを使用して、お使いのサブスクリプションを現在の既定のサブスクリプションとして設定します。

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az provider register](/cli/azure/provider#az_provider_register) コマンドを実行して、リソース プロバイダーを登録します。

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成するか、既存のリソース グループを使用します。

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) コマンドを実行して、Data Share アカウントを作成します。

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) コマンドを実行して、Data Share アカウントを表示します。

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>共有を作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Data Share の [概要] ページに移動します。

    ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します   

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

    ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

    ![共有にデータセットを追加する](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 前の手順で選択した共有の種類 (スナップショットまたはインプレース) によって異なる種類のデータセット一覧が表示されます。 Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW) から共有する場合、テーブルを一覧表示するために SQL 資格情報の入力を求められます。

    ![AddDatasets](./media/add-datasets.png "データセットを追加する")    

1. 共有するオブジェクトに移動し、[Add Datasets]\(データセットの追加\) を選択します。 

    ![SelectDatasets](./media/select-datasets.png "データセットを選択する")    

1. [Recipients]\(受信者\) タブで、[+ Add Recipient]\(+ 受信者の追加\) を選択して、データ コンシューマーのメール アドレスを入力します。 

    ![AddRecipients](./media/add-recipient.png "受信者の追加") 

1. **[続行]** をクリックします。

1. スナップショット共有タイプを選択した場合は、データの更新をデータ コンシューマーに提供するスナップショット スケジュールを構成できます。 

    ![EnableSnapshots](./media/enable-snapshots.png "スナップショットを有効化する") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** をクリックします。

1. [Review + Create]\(レビュー + 作成\) タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **［作成］** を選択します

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを実行して、Data Share を作成します。

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. [az storage container create](/cli/azure/storage/container#az_storage_container_create) コマンドを使用して、前のコマンドの共有用のコンテナーを作成します。

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) コマンドを実行して、Data Share を作成します。

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) コマンドを使用して、指定したアドレスに対する招待を作成します。

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが不要になったら、**Data Share の [概要]** ページに移動し、 **[削除]** を選択して削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure データ共有を作成し、受信者を招待する方法について説明しました。 データ コンシューマーがデータ共有を受け入れて受信できるようにする方法については、続けてデータの受け入れと受信に関するチュートリアルを行ってください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Data Share を使用したデータの受け入れと受信](subscribe-to-data-share.md)
