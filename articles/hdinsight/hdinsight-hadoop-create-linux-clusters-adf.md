---
title: 'チュートリアル: Data Factory を使用して Azure HDInsight でオンデマンドの Apache Hadoop クラスターを作成する '
description: Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/29/2018
ms.author: hrasheed
ms.openlocfilehash: 8b65cb05643ffca3cbf25a207dce683d2d60fd64
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361576"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>チュートリアル: Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

この記事では、Azure Data Factory を使用して Azure HDInsight で [Apache Hadoop](https://hadoop.apache.org/) クラスター (オンデマンド) を作成する方法について説明します。 その後 Azure Data Factory でデータ パイプラインを使用して Hive ジョブを実行し、クラスターを削除します。 このチュートリアルを完了すると、クラスターの作成、ジョブの実行、クラスターの削除がスケジュールに従って実行されるビッグ データ ジョブの実行を作動可能な状態にする方法を習得できます。

このチュートリアルに含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure のストレージ アカウントの作成
> * Azure Data Factory のアクティビティを理解する
> * Azure Portal を使用してデータ ファクトリを作成する
> * リンクされたサービスを作成します
> * パイプラインを作成する。
> * パイプラインをトリガーする
> * パイプラインを監視する
> * 出力を検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell。 手順については、 [Azure PowerShell のインストールおよび構成に関するページ](https://docs.microsoft.com/powershell/azure/install-az-ps)を参照してください。

- Azure Active Directory サービス プリンシパル。 サービス プリンシパルを作成したら、リンク先の記事の手順に従って、**アプリケーション ID** と**認証キー**を必ず取得してください。 このチュートリアルで、後ほどこれらの値が必要になります。 また、サービス プリンシパルが、サブスクリプションまたはクラスターが作成されるリソース グループの*共同作成者*ロールのメンバーであることを確認してください。 必要な値を取得し、適切なロールを割り当てる手順については、[Azure Active Directory サービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事をご覧ください。

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

このセクションでは、オンデマンドで作成する HDInsight クラスターの既定のストレージとして使用するストレージ アカウントを作成します。 このストレージ アカウントには、クラスターで実行されるサンプル [Apache Hive](https://hive.apache.org/) ジョブのシミュレートに使用するサンプル [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) スクリプト (**hivescript.hql**) も含まれます。

このセクションでは、Azure PowerShell スクリプトを使用してストレージ アカウントを作成し、ストレージ アカウント内の必要なファイルをコピーします。 このセクションの Azure PowerShell サンプル スクリプトでは、次のタスクを実行します。

1. Azure にログインします。
1. Azure リソース グループを作成します。
1. Azure Storage アカウントを作成します。
1. ストレージ アカウントに BLOB コンテナーを作成します。
1. サンプル HiveQL スクリプト (**hivescript.hql**) を BLOB コンテナーにコピーします。 このスクリプトは、[https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) で入手できます。 サンプル スクリプトは、別のパブリック BLOB コンテナーで既に使用できます。 下記の PowerShell スクリプトでは、作成された Azure ストレージ アカウントにこれらのファイルのコピーを作成します。


**Azure PowerShell を使用してストレージ アカウントを作成し、ファイルをコピーするには:**
> [!IMPORTANT]  
> スクリプトを使って作成する Azure リソース グループと Azure ストレージ アカウントの名前を指定します。
> スクリプトによって出力された**リソース グループ名**、**ストレージ アカウント名**、**ストレージ アカウント キー**を書き留めます。 これらは、次のセクションで必要になります。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzAccount
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**ストレージ アカウントの作成を確認するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
1. 左側のウィンドウの **[リソース グループ]** を選択します。
1. PowerShell スクリプトで作成したリソース グループの名前をダブルクリックします。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。
1. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有する場合を除き、リソースが 1 つだけ表示されています。 このリソースが、前の手順で指定した名前のストレージ アカウントです。 ストレージ アカウント名を選択します。
1. **[BLOB]** タイルを選択します。
1. **adfgetstarted** コンテナーを選択します。 **hivescripts** というフォルダーが表示されます。
1. このフォルダーを開き、サンプル スクリプト ファイル (**hivescript.hql**) があることを確認します。

## <a name="understand-the-azure-data-factory-activity"></a>Azure Data Factory のアクティビティを理解する

[Azure Data Factory](../data-factory/introduction.md) では、データの移動と変換を調整して自動化します。 Azure Data Factory を使用すると、入力データ スライスを処理するために HDInsight Hadoop クラスターをジャスト イン タイムで作成し、処理が完了したらクラスターを削除できます。 

Azure Data Factory では、データ ファクトリに 1 つまたは複数のデータ パイプラインを設定できます。 データ パイプラインには、1 つ以上のアクティビティがあります。 次の 2 種類のアクティビティがあります。

- [データ移動アクティビティ](../data-factory/copy-activity-overview.md) - データ移動アクティビティを使用して、ソース データ ストアから宛先データ ストアにデータを移動します。
- [データ変換アクティビティ](../data-factory/transform-data.md) -  データ変換アクティビティは、データを変換/処理するために使用します。 HDInsight Hive アクティビティは、Data Factory でサポートされるデータ変換アクティビティの 1 つです。 このチュートリアルでは、Hive 変換アクティビティを使用します。

この記事では、オンデマンドの HDInsight Hadoop クラスターを作成するように Hive アクティビティを構成します。 アクティビティを実行してデータ を処理するときには、次のことが行われます。

1. スライスを処理するために、HDInsight Hadoop クラスターが Just-In-Time 方式で自動的に作成されます。 

1. クラスター上で HiveQL スクリプトを実行することによって入力データが処理されます。 このチュートリアルの Hive アクティビティに関連付けられた HiveQL スクリプトは、次のアクションを実行します。
    
    - 既存のテーブル (*hivesampletable*) を使用して別のテーブル (**HiveSampleOut**) を作成します。
    - **HiveSampleOut** テーブルに、元の *hivesampletable* の特定の列だけを設定します。
    
1. HDInsight Hadoop クラスターは、処理が完了し、(TimeToLive 設定で) 構成された時間アイドル状態になると、削除されます。 この TimeToLive アイドル時間内に次のデータ スライスを処理できる場合、スライスを処理するために同じクラスターが使用されます。  

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. [Azure Portal](https://portal.azure.com/) にログインします。

1. 左側のメニューで、**[+ リソースの作成]** を選択します。

1. **[Azure Marketplace]** の **[分析]** を選択します。

1.  **[おすすめ]** の **[Data Factory]** を選択します。

    ![ポータルの Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "ポータルの Azure Data Factory")

1. 次のスクリーンショットに示すように値を入力または選択します。

    ![Azure Portal を使用して Azure Data Factory を作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Azure Portal を使用して Azure Data Factory を作成する")

    次の値を入力または選択します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**名前** |  データ ファクトリの名前を入力します。 この名前はグローバルに一意である必要があります。|
    |**サブスクリプション**     |  Azure サブスクリプションを選択します。 |
    |**[リソース グループ]**     | **[既存のものを使用]** を選択し、PowerShell スクリプトを使用して作成したリソース グループを選択します。 |
    |**バージョン**     | **[V2]** を選択します。 |
    |**場所**     | 場所は、リソース グループの作成時に指定した場所に自動的に設定されます。 このチュートリアルでは、場所は **[米国東部]** に設定されます。 |
    

1. **作成**を選択します。 データ ファクトリの作成には、2 ～ 4 分ほどかかることがあります。


1. データ ファクトリが作成されると、**[リソースに移動]** ボタンを含む**デプロイ成功**通知が届きます。  **[リソースに移動]** を選択して、Data Factory の既定のビューを開きます。

1. **[作成と監視]** を選択して、Azure Data Factory の作成および監視ポータルを起動します。

    ![Azure Data Factory の概要](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory の概要")

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このセクションでは、データ ファクトリ内に 2 つのリンクされたサービスを作成します。

- Azure ストレージ アカウントをデータ ファクトリにリンクする、**Azure Storage のリンクされたサービス**。 このストレージは、オンデマンドの HDInsight クラスターによって使用されます。 また、クラスター上で実行される Hive スクリプトも含まれています。
- **オンデマンドの HDInsight のリンクされたサービス**。 Azure Data Factory によって、HDInsight クラスターが自動的に作成され、Hive スクリプトが実行されます。 HDInsight クラスターは、事前に構成された時間だけアイドル状態になったら削除されます。

###  <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. **[始めましょう]** ページの左側のウィンドウで、**[編集]** アイコンをクリックします。

    ![Azure Data Factory のリンクされたサービスを作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Azure Data Factory のリンクされたサービスを作成する")

1. ウィンドウの左下隅にある **[接続]** を選択し、**[+ 新規]** を選択します。

    ![Azure Data Factory で接続を作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Azure Data Factory で接続を作成する")

1. **[New Linked Service]\(新しいリンクされたサービス\)** ダイアログ ボックスで **[Azure Blob Storage]** を選択し、**[続行]** をクリックします。

    ![Data Factory の Azure Storage のリンクされたサービスを作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Data Factory の Azure Storage のリンクされたサービスを作成する")

1. ストレージのリンクされたサービスの名前を指定し、PowerShell スクリプトの一部として作成した Azure ストレージ アカウントを選択して、**[完了]** をクリックします。

    ![Azure Storage のリンクされたサービスの名前を指定する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Azure Storage のリンクされたサービスの名前を指定する")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>オンデマンドの HDInsight のリンクされたサービスを作成する

1. **[+ 新規]** ボタンをもう一度選択して、別のリンクされたサービスを作成します。

1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、**[コンピューティング]** > **[Azure HDInsight]** の順に選択し、**[続行]** を選択します。

    ![Azure Data Factory の HDInsight のリンクされたサービスを作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Azure Data Factory の HDInsight のリンクされたサービスを作成する")

1. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで必要な値を入力します。

    ![HDInsight のリンクされたサービスの値を入力する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "HDInsight のリンクされたサービスの値を入力する")

    次の値を入力し、残りは既定値のままにしておきます。

    | プロパティ | 説明 |
    | --- | --- |
    | Name | HDInsight のリンクされたサービスの名前を入力します。 |
    | type | **[On-demand HDInsight]\(オンデマンド HDInsight\)** を選択します。 |
    | Azure Storage のリンクされたサービス | 以前に作成した Storage のリンクされたサービスを選択します。 |
    | クラスターの種類 | **[hadoop]** を選択します。 |
    | Time to Live | HDInsight クラスターを使用できるようにしておく期間を指定します。この期間を過ぎると、クラスターは自動的に削除されます。|
    | サービス プリンシパル ID | 前提条件の 1 つとして作成した Azure Active Directory サービス プリンシパルのアプリケーション ID を指定します。 |
    | サービス プリンシパル キー | Azure Active Directory サービス プリンシパルの認証キーを指定します。 |
    | Cluster name prefix\(クラスター名のプレフィックス\) | データ ファクトリによって作成されるすべてのクラスターの種類にプレフィックスとして追加する値を指定します。 |
    | リソース グループ | 以前に使用した PowerShell スクリプトの一部として作成したリソース グループを選択します。| 
    | Cluster SSH user name\(クラスターの SSH ユーザー名\) | SSH ユーザー名を入力します。 |
    | Cluster SSH password\(クラスターの SSH パスワード\) | SSH ユーザーのパスワードを指定します。 |

    **[完了]** を選択します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

1. **+** (正符号) ボタンを選択し、**[パイプライン]** を選択します。

    ![Azure Data Factory でパイプラインを作成する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Azure Data Factory でパイプラインを作成する")

1. **[アクティビティ]** ツールボックスで **[HDInsight]** を展開し、パイプライン デザイナー画面に **Hive** アクティビティをドラッグします。 **[全般]** タブで、アクティビティの名前を指定します。

    ![Data Factory パイプラインにアクティビティを追加する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Data Factory パイプラインにアクティビティを追加する")

1. Hive アクティビティが選択されていることを確認します。**[HDI Cluster]\(HDI クラスター\)** タブを選択し、**[HDInsight のリンクされたサービス]** ドロップダウンから HDInsight 用に以前に作成したリンクされたサービスを選択します。

    ![パイプラインの HDInsight クラスターの詳細を指定する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "パイプラインの HDInsight クラスターの詳細を指定する")

1. **[スクリプト]** タブを選択し、次の手順を実行します。
    
    1. **[スクリプトにリンクされたサービス]** で **[HDIStorageLinkedService]** を選択します。 この値は、以前に作成したストレージのリンクされたサービスです。
    
    1. **[ファイル パス]** で **[ストレージを参照]** を選択し、サンプル Hive スクリプトがある場所に移動します。 以前に PowerShell スクリプトを実行した場合、この場所は `adfgetstarted/hivescripts/hivescript.hql` になります。
    
        ![パイプラインの Hive スクリプトの詳細を指定する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "パイプラインの Hive スクリプトの詳細を指定する")
    
    1. **[詳細設定]** > **[パラメーター]** で、**[Auto-fill from script]\(スクリプトから自動入力\)** を選択します。 このオプションを選択すると、実行時に値を必要とする、Hive スクリプトのパラメーターが検索されます。 使用するスクリプト (**hivescript.hql**) には、**Output** パラメーターがあります。 Azure Storage の既存のフォルダーを参照する値を `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` の形式で指定します。 パスでは大文字と小文字が区別されます。 これはスクリプトの出力が格納されるパスです。
    
        ![Hive スクリプトのパラメーターを指定する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Hive スクリプトのパラメーターを指定する")

1. **[検証]** を選択してパイプラインを検証します。 **>>** (右矢印) ボタンを選択して、検証ウィンドウを閉じます。

    ![Azure Data Factory パイプラインを検証する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Azure Data Factory パイプラインを検証する")

1. 最後に、**[すべて発行]** を選択して、成果物を Azure Data Factory に発行します。

    ![Azure Data Factory パイプラインを発行する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Azure Data Factory パイプラインを発行する")

## <a name="trigger-a-pipeline"></a>パイプラインをトリガーする

1. デザイナー画面のツール バーで、**[トリガー]** > **[Trigger Now]\(今すぐトリガー\)** を選択します。

    ![Azure Data Factory パイプラインをトリガーする](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Azure Data Factory パイプラインをトリガーする")

1. ポップアップ サイド バーの **[完了]** をクリックします。

## <a name="monitor-a-pipeline"></a>パイプラインを監視する

1. 左側で **[監視]** タブに切り替えます。 **[Pipeline Runs]\(パイプラインの実行\)** の一覧にパイプライン実行が表示されます。 **[状態]** 列で実行の状態を確認します。

    ![Azure Data Factory パイプラインを監視する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Azure Data Factory パイプラインを監視する")

1. **[最新の情報に更新]** を選択して、状態を更新します。

1. **[View Activity Runs]\(アクティビティの実行の表示\)** アイコンをクリックして、パイプラインに関連付けられているアクティビティの実行を表示することもできます。 作成したパイプラインにはアクティビティが 1 つしかないため、次のスクリーンショットでは、アクティビティの実行が 1 つしか表示されていません。 前のビューに戻るには、ページの上部にある **[パイプライン]** を選択します。

    ![Azure Data Factory パイプラインのアクティビティを監視する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Azure Data Factory パイプラインのアクティビティを監視する")


## <a name="verify-the-output"></a>出力を検証する

1. 出力を検証するには、Azure Portal 上で、このチュートリアルで使用したストレージ アカウントに移動します。 次のフォルダーまたはコンテナーが表示されます。

    - パイプラインの一部として実行された Hive スクリプトの出力が含まれた **adfgerstarted/outputfolder** が表示されます。

    - **adfhdidatafactory-\<リンクされたサービスの名前>-\<タイムスタンプ>** コンテナーが表示されます。 このコンテナーは、パイプライン実行の一環として作成された HDInsight クラスターの既定のストレージの場所です。

    - Azure Data Factory のジョブ ログがある **adfjobs** コンテナーが表示されます。  

        ![Azure Data Factory パイプラインの出力を検証する](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Azure Data Factory パイプラインの出力を検証する")


## <a name="clean-up-the-tutorial"></a>このチュートリアルの仕上げ

オンデマンドの HDInsight クラスターを作成した場合、HDInsight クラスターを明示的に削除する必要はありません。 クラスターは、パイプラインの作成時に指定した構成に基づいて削除されます。 ただし、クラスターを削除した後も、クラスターに関連付けられているストレージ アカウントは引き続き存在します。 データをそのまま保持できるように、この動作は仕様です。 データを保持する必要がない場合は、作成したストレージ アカウントを削除して構いません。

また、このチュートリアルで作成したリソース グループ全体を削除することもできます。 これにより、作成したストレージ アカウントと Azure Data Factory が削除されます。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

1. [Azure Portal](https://portal.azure.com) にサインオンします。
1. 左側のウィンドウの **[リソース グループ]** を選択します。
1. PowerShell スクリプトで作成したリソース グループの名前を選択します。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。 リソース グループが開きます。
1. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有する場合を除き、既定のストレージ アカウントとデータ ファクトリが表示されます。
1. **[リソース グループの削除]** を選択します。 この操作を実行すると、ストレージ アカウントと、そのストレージ アカウントに格納されているデータが削除されます。

    ![リソース グループを削除する](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "リソース グループを削除する")

1. リソース グループ名を入力して削除を確認し、**[削除]** をクリックします。


## <a name="next-steps"></a>次の手順
この記事では、Azure Data Factory を使用してオンデマンドの HDInsight クラスターを作成し、[Apache Hive](https://hive.apache.org/) ジョブを実行する方法を説明しました。 次の記事に進み、HDInsight クラスターをカスタム構成で作成する方法を確認してください。

> [!div class="nextstepaction"]
>[Azure HDInsight クラスターをカスタム構成で作成する](hdinsight-hadoop-provision-linux-clusters.md)


