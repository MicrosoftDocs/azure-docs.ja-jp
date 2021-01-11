---
title: チュートリアル:組織の外部と共有する - Azure Data Share
description: チュートリアル - Azure Data Share を使用して顧客やパートナーとデータを共有する
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/28/2020
ms.openlocfilehash: 4f3c9d739f959670700182baf80c4a08e53d8587
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259066"
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

### <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有:

* Azure Storage アカウント: まだお持ちでない場合は、[Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)を作成できます。
* ストレージ アカウントに書き込む権限。これは、*Microsoft.Storage/storageAccounts/write* に含まれています。 この権限は、投稿者ロール内に存在します。
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。 


### <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有:

* 共有するテーブルとビューを含む Azure SQL Database または Azure Synapse Analytics (旧称 Azure SQL Data Warehouse)。
* SQL サーバー上のデータベースに書き込む権限。これは、*Microsoft.Sql/servers/databases/write* に含まれています。 この権限は、投稿者ロール内に存在します。
* データ共有からデータ ウェアハウスにアクセスするためのアクセス許可。 この操作を行うには、以下の手順を実行します。 
    1. 自分自身を SQL サーバーの Azure Active Directory 管理者として設定します。
    1. Azure Active Directory を使用して Azure SQL Database/Data Warehouse に接続します。
    1. クエリ エディター (プレビュー) を使用して次のスクリプトを実行し、Data Share リソースのマネージド ID を db_datareader として追加します。 SQL Server 認証ではなく Active Directory を使用して接続する必要があります。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* は、Data Share リソースの名前であることに注意してください。 Data Share リソースをまだ作成していない場合は、後でこの前提条件に戻ってくることが可能です。  

* 共有するテーブルまたはビューに移動して選択するための "db_datareader" アクセス権を持つ Azure SQL Database ユーザー。 

* クライアント IP SQL Server のファイアウォール アクセス。 この操作を行うには、以下の手順を実行します。 
    1. Azure portal の SQL サーバーで、 *[ファイアウォールと仮想ネットワーク]* に移動します。
    1. Azure サービスへのアクセスを許可するには、**オン** トグルをクリックします。
    1. **[+ クライアント IP の追加]** をクリックし、 **[保存]** をクリックします。 クライアントの IP アドレスは変わることがあります。 次回 Azure portal から SQL データを共有するときにも、このプロセスを繰り返すことが必要になる場合もあります。 IP 範囲を追加することもできます。 

### <a name="share-from-azure-data-explorer"></a>Azure Data Explorer からの共有
* 共有したいデータベースを含んだ Azure Data Explorer クラスター。
* Azure Data Explorer クラスターに書き込む権限。これは、*Microsoft.Kusto/clusters/write* に含まれています。 この権限は、投稿者ロール内に存在します。
* Azure Data Explorer クラスターにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* に含まれています。 この権限は、所有者ロール内に存在します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

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

## <a name="create-a-share"></a>共有を作成する

1. Data Share の [概要] ページに移動します。

    ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します   

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

    ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

    ![共有にデータセットを追加する](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 前の手順で選択した共有の種類 (スナップショットまたはインプレース) によって異なる種類のデータセット一覧が表示されます。 Azure SQL Database または Azure SQL Data Warehouse から共有する場合は、いくつかの SQL 資格情報を入力するように求められます。 前提条件の中で作成したユーザーを使用して、認証します。

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

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure データ共有を作成し、受信者を招待する方法について説明しました。 データ コンシューマーがデータ共有を受け入れて受信できるようにする方法については、続けて[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルを行ってください。 
