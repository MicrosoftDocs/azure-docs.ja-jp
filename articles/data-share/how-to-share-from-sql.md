---
title: Azure SQL Database と Azure Synapse Analytics からのデータの共有と受信
description: Azure SQL Database と Azure Synapse Analytics からデータを共有および受信する方法について説明します
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: f87ad76e9bb1db4d71716bf860d5fee2d413e8e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740377"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database と Azure Synapse Analytics からのデータの共有と受信

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share は、Azure SQL Database と Azure Synapse Analytics のスナップショット ベースの共有をサポートしています。 この記事では、これらのソースからデータを共有および受信する方法について説明します。

Azure Data Share は、Azure SQL Database と Azure Synapse Analytics (旧称 Azure SQL DW) からのテーブルとビュー両方の共有、および Azure Synapse Analytics (ワークスペース) 専用 SQL プールからのテーブルの共有をサポートしています。 Azure Synapse Analytics (ワークスペース) サーバーレス SQL プールからの共有は、現在サポートされていません。 データ コンシューマーは、データを Azure Data Lake Storage Gen2 または Azure Blob Storage に csv または parquet ファイルとして受け入れたり、Azure SQL Database と Azure Synapse Analytics にテーブルとして受け入れたりすることができます。

データを Azure Data Lake Store Gen2 または Azure Blob Storage に受け入れると、完全なスナップショットによってターゲット ファイル (存在する場合) の内容が上書きされます。
データが SQL テーブルに受け入れられるときにターゲット テーブルがまだ存在しない場合は、Azure Data Share によって、ソース スキーマを使用して SQL テーブルが作成されます。 同じ名前を持つターゲット テーブルが既に存在する場合、それは削除され、最新の完全なスナップショットで上書きされます。 増分スナップショットは現在はサポートされていません。

## <a name="share-data"></a>データの共有

### <a name="prerequisites-to-share-data"></a>データを共有するための前提条件

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* 受信者の Azure ログイン用メール アドレス (メール エイリアスは無効です)。
* Data Share リソースの作成用とは異なる Azure サブスクリプションにソース Azure データ ストアが存在する場合、Azure データ ストアがあるサブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録してください。 

### <a name="prerequisites-for-sql-source"></a>SQL ソースの前提条件
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

* 共有するテーブルを含む Azure Synapse Analytics (ワークスペース) の専用 SQL プール。 ビューの共有は現在サポートされていません。 サーバーレス SQL プールからの共有は、現在サポートされていません。
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

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

Azure リソース グループに Azure Data Share リソースを作成します。

1. ポータルの左上隅にあるメニュー ボタンを選択し、 **[リソースの作成]** (+) を選択します。

1. *Data Share* を検索します。

1. Data Share を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本的な詳細に、次の情報を入力します。 

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | Data Share アカウントに使う Azure サブスクリプションを選択します。|
    | リソース グループ | *test-resource-group* | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
    | 場所 | *米国東部 2* | Data Share アカウントのリージョンを選択します。
    | 名前 | *datashareaccount* | Data Share アカウントの名前を指定します。 |
    | | |

1. **[Review + create]\(レビュー + 作成\)** を選択し、 **[作成]** を選択して Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分以下です。 

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="create-a-share"></a>共有を作成する

1. Data Share の [概要] ページに移動します。

    ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します   

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

    ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

    ![共有にデータセットを追加する](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 前の手順で選択した共有の種類 (スナップショットまたはインプレース) によって異なる種類のデータセット一覧が表示されます。 

    ![AddDatasets](./media/add-datasets.png "データセットを追加する")    

1. ご利用の SQL サーバーまたは Synapse ワークスペースを選択し、資格情報を入力するよう求められたら入力し、 **[次へ]** を選択して共有したいオブジェクトに移動し、[データセットの追加] を選択します。 Azure SQL Database と Azure Synapse Analytics (旧称 Azure SQL DW) からのテーブルとビュー、または Azure Synapse Analytics (ワークスペース) 専用 SQL プールからのテーブルを選択できます。 

    ![SelectDatasets](./media/select-datasets-sql.png "データセットを選択する")    

1. [Recipients]\(受信者\) タブで、[+ Add Recipient]\(+ 受信者の追加\) を選択して、データ コンシューマーのメール アドレスを入力します。 このメール アドレスは、受信者の Azure ログイン メールである必要があります。

    ![AddRecipients](./media/add-recipient.png "受信者の追加") 

1. **[続行]** をクリックします。

1. スナップショット共有タイプを選択した場合は、データの更新をデータ コンシューマーに提供するスナップショット スケジュールを構成できます。 

    ![EnableSnapshots](./media/enable-snapshots.png "スナップショットを有効化する") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** をクリックします。

1. [Review + Create]\(レビュー + 作成\) タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **［作成］** を選択します

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。 

## <a name="receive-data"></a>データの受信

### <a name="prerequisites-to-receive-data"></a>データを受信するための前提条件
データ共有の招待を受け入れる前に、次に一覧表示されている多くの Azure リソースをプロビジョニングする必要があります。 

データ共有の招待を受け入れる前に、すべての前提条件を満たしていることを確認します。 

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Data Share の招待:" **<yourdataprovider@domain.com>** からの Azure Data Share の招待" という件名の Microsoft Azure からの招待。
* Data Share リソースの作成先となる Azure サブスクリプションとターゲット Azure データ ストアがある Azure サブスクリプションで [Microsoft.DataShare リソースプロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録します。

### <a name="prerequisites-for-target-storage-account"></a>ターゲット ストレージ アカウントの前提条件
Azure Storage へのデータを受信することを選択する場合、前提条件の一覧を以下に示します。

* Azure Storage アカウント: [Azure Storage アカウント](../storage/common/storage-account-create.md)をまだお持ちでない場合は、作成できます。 
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 このアクセス許可は、**共同作成者** ロール内に存在します。 
* Data Share リソースのマネージド ID のロール割り当てをストレージ アカウントに追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 このアクセス許可は、**所有者** ロール内に存在します。  

### <a name="prerequisites-for-sql-target"></a>SQL ターゲットの前提条件
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

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="open-invitation"></a>招待を開く

1. 招待は、メールから開くことができるほか、Azure portal から直接開くこともできます。 

   招待をメールから開くには、受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " **<yourdataprovider@domain.com> からの Azure Data Share の招待**" になっています。 **[招待を表示]** をクリックして、Azure で招待を確認します。 

   Azure portal から直接招待を開くには、Azure portal で **[データ共有への招待]** を検索します。 Data Share の招待が一覧表示されます。

   ![招待の一覧](./media/invitations.png "招待の一覧") 

1. 表示する共有を選択します。 

### <a name="accept-invitation"></a>招待を受け入れる
1. **[使用条件]** を含む、すべてのフィールドを確認してください。 使用条件に同意する場合は、同意を示すボックスをオンにする必要があります。 

   ![使用条件](./media/terms-of-use.png "使用条件") 

1. *[Target Data Share Account]\(ターゲット データ共有アカウント\)* の下で、ご自分のデータ共有のデプロイ先となるサブスクリプションとリソース グループを選択します。 

   **[Data Share Account]\(データ共有アカウント\)** フィールドには、既存のデータ共有アカウントがない場合には、 **[新規作成]** を選択します。 それ以外の場合は、自分のデータ共有を受け入れる既存のデータ共有アカウントを選択します。 

   **[Received Share Name]\(受信した共有名\)** フィールドは、データの指定で指定された既定のままにしておくことも、受信した共有のために新しい名前を指定することもできます。 

   利用規約に同意し、受信した共有を管理するための Data Share アカウントを指定したら、 **[Accept and configure]\(受け入れと構成\)** を選択します。 共有サブスクリプションが作成されます。 

   ![受け入れオプション](./media/accept-options.png "受け入れオプション") 

   これにより、Data Share アカウントに、受信した共有が表示されます。 

   招待を受け入れたくない場合は、*[拒否]* を選択します。 

### <a name="configure-received-share"></a>受信した共有を構成する
データを受信する場所を構成するには、次の手順に従います。

1. **[データセット]** タブを選択します。ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 **[+ ターゲットへのマップ]** を選択し、ターゲット データ ストアを選択します。 

   ![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

1. データを配置するターゲット データ ストアを選択します。 パスと名前が同じターゲット データ ストア内のすべてのデータ ファイルまたはデータ テーブルが上書きされます。 

   ![ターゲット ストレージ アカウント](./media/dataset-map-target-sql.png "ターゲット データ ストア") 

1. スナップショットベースの共有の場合、データに定期的な更新を提供するためのスナップショット スケジュールがデータ プロバイダーによって作成されていれば、 **[スナップショット スケジュール]** タブを選択して、スナップショット スケジュールを有効にすることもできます。スナップショット スケジュールの横にあるチェック ボックスをオンにし、 **[+ Enable]\(+ 有効\)** を選択します。

   ![スナップショット スケジュールを有効にする](./media/enable-snapshot-schedule.png "スナップショット スケジュールを有効にする")

### <a name="trigger-a-snapshot"></a>スナップショットをトリガーする
これらの手順は、スナップショットベースの共有にのみ適用されます。

1. スナップショットをトリガーするには、 **[詳細]** タブ、 **[スナップショットのトリガー]** の順に選択します。 これで、データの完全なスナップショットまたは増分スナップショットをトリガーすることができます。 データ プロバイダーから初めてデータを受信する場合は、完全なコピーを選択します。 SQL ソースの場合は、完全なスナップショットのみがサポートされます。 1 つのスナップショットが実行されている場合、後続のスナップショットは前のものが完了するまで開始されません。

   ![スナップショットのトリガー](./media/trigger-snapshot.png "スナップショットのトリガー") 

1. 最終実行状態が "*成功*" の場合、ターゲット データ ストアに移動して、受信したデータを表示します。 **[データセット]** を選択して、ターゲット パスのリンクをクリックしてください。 

   ![コンシューマー データセット](./media/consumer-datasets.png "コンシューマー データセットのマッピング") 

### <a name="view-history"></a>履歴を表示する
この手順は、スナップショットベースの共有にのみ適用されます。 スナップショットの履歴を表示するには、 **[履歴]** タブを選択します。ここには、過去 30 日間に生成されたすべてのスナップショットの履歴があります。 

## <a name="supported-data-types"></a>サポートされるデータ型
SQL ソースからデータを共有するとき、スナップショットの処理時には、SQL Server のデータ型から Azure Data Share の中間データ型への、以下のマッピングが使用されます。 

| SQL Server のデータ型 | Azure Data Share の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 1. 10 進の中間型にマップされるデータ型の場合、スナップショットでは、現在、最大 28 の有効桁数がサポートされています。 28 よりも大きな有効桁数を必要とするデータがある場合は、文字列に変換することを検討してください。 
> 1.  Azure SQL データベースから Azure Synapse Analytics にデータを共有している場合は、すべてのデータ型がサポートされるわけではありません。 詳細については、[専用 SQL プールでのテーブルのデータ型](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md)に関する記事を参照してください。 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted または動的データ マスク
現時点では、Azure Data Share では、Always Encrypted が構成された Azure SQL データベースはサポートされていません。 

動的データ マスクを使用するソース SQL テーブルの場合、データは受信側でマスクされて表示されます。

## <a name="sql-snapshot-performance"></a>SQL スナップショットのパフォーマンス
SQL スナップショットのパフォーマンスは、さまざまな要因の影響を受けます。 常に、独自のパフォーマンス テストを実施することをお勧めします。 パフォーマンスに影響するいくつかの要因の例を下に示します。

* ソースとターゲットの SQL データ ストアのハードウェア構成 (仮想コア、メモリ、DWU など)。 
* ソースとターゲットのデータ ストアへの同時アクセス。 同じ SQL データ ストアから複数のテーブルとビューを共有している場合、または複数のテーブルとビューを同じ SQL データ ストアに受信する場合、パフォーマンスに影響します。   
* ソースとターゲットのデータ ストアの場所。 

## <a name="troubleshoot-sql-snapshot-failure"></a>SQL スナップショット エラーのトラブルシューティング
スナップショット エラーの最も一般的な原因は、Data Share にソースまたはターゲットのデータ ストアへのアクセス許可がないことです。 ソースまたはターゲットの Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL DW) へのアクセス許可を Data Share に付与するには、Azure Active Directory 認証を使用して SQL データベースに接続するときに、提供された SQL スクリプトを実行する必要があります。 その他の SQL スナップショット エラーをトラブルシューティングするには、[スナップショット エラーのトラブルシューティング](data-share-troubleshoot.md#snapshots)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
Azure Data Share サービスを使用して SQL ソースからデータを共有および受信する方法について学習しました。 他のデータ ソースからの共有の詳細については、[サポートされているデータ ストア](supported-data-stores.md)に関するページに進んでください。