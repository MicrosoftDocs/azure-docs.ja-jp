---
title: チュートリアル:Data Factory を使用した Azure HDInsight でのオンデマンド クラスター
description: チュートリアル - Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する方法について説明します。
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: c61ad4d26c4a03889d9ac80332335543ec4140b7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868983"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>チュートリアル:Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

このチュートリアルでは、Azure Data Factory を使用して Azure HDInsight で [Apache Hadoop](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) クラスター (オンデマンド) を作成する方法について説明します。 その後 Azure Data Factory でデータ パイプラインを使用して Hive ジョブを実行し、クラスターを削除します。 このチュートリアルを完了すると、クラスターの作成、ジョブの実行、クラスターの削除がスケジュールに従って実行されるビッグ データ ジョブの実行を `operationalize` する方法を習得できます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure のストレージ アカウントの作成
> * Azure Data Factory のアクティビティを理解する
> * Azure Portal を使用してデータ ファクトリを作成する
> * リンクされたサービスを作成します
> * パイプラインを作成する
> * パイプラインをトリガーする
> * パイプラインを監視する
> * 出力を検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* インストール済みの PowerShell [Az モジュール](/powershell/azure/)。

* Azure Active Directory サービス プリンシパル。 サービス プリンシパルを作成したら、リンク先の記事の手順に従って、**アプリケーション ID** と **認証キー** を必ず取得してください。 このチュートリアルで、後ほどこれらの値が必要になります。 また、サービス プリンシパルが、サブスクリプションまたはクラスターが作成されるリソース グループの *共同作成者* ロールのメンバーであることを確認してください。 必要な値を取得し、適切なロールを割り当てる手順については、[Azure Active Directory サービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事をご覧ください。

## <a name="create-preliminary-azure-objects"></a>予備の Azure オブジェクトを作成します。

このセクションでは、オンデマンドで作成する HDInsight クラスターに使用する各種オブジェクトを作成します。 作成されるストレージ アカウントには、クラスターで実行されるサンプル Apache Hive ジョブのシミュレートに使用するサンプル HiveQL スクリプトの `partitionweblogs.hql` が含まれます。

このセクションでは、Azure PowerShell スクリプトを使用してストレージ アカウントを作成し、ストレージ アカウント内の必要なファイルをコピーします。 このセクションの Azure PowerShell サンプル スクリプトでは、次のタスクを実行します。

1. Azure へのサインイン
2. Azure リソース グループを作成します。
3. Azure Storage アカウントを作成します。
4. ストレージ アカウントに BLOB コンテナーを作成します。
5. サンプル HiveQL スクリプト (**partitionweblogs.hql**) を BLOB コンテナーにコピーします。 このスクリプトは、[https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) で入手できます。 サンプル スクリプトは、別のパブリック BLOB コンテナーで既に使用できます。 下記の PowerShell スクリプトでは、作成された Azure ストレージ アカウントにこれらのファイルのコピーを作成します。

### <a name="create-storage-account-and-copy-files"></a>ストレージ アカウントを作成してファイルをコピーする

> [!IMPORTANT]  
> スクリプトを使って作成する Azure リソース グループと Azure ストレージ アカウントの名前を指定します。
> スクリプトによって出力された **リソース グループ名**、**ストレージ アカウント名**、**ストレージ アカウント キー** を書き留めます。 これらは、次のセクションで必要になります。

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>ストレージ アカウントを確認する

1. [Azure Portal](https://portal.azure.com) にサインオンします。
1. 左側から、 **[すべてのサービス]**  >  **[全般]**  >  **[リソース グループ]** の順に移動します。
1. PowerShell スクリプトで作成したリソース グループの名前を選択します。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。
1. **[概要]** ビューには、リソース グループを他のプロジェクトと共有する場合を除き、リソースが 1 つだけ表示されています。 このリソースが、前の手順で指定した名前のストレージ アカウントです。 ストレージ アカウント名を選択します。
1. **[コンテナー]** タイルを選択します。
1. **adfgetstarted** コンテナーを選択します。 **`hivescripts`** というフォルダーが表示されます。
1. このフォルダーを開き、サンプル スクリプト ファイル (**partitionweblogs.hql**) があることを確認します。

## <a name="understand-the-azure-data-factory-activity"></a>Azure Data Factory のアクティビティを理解する

[Azure Data Factory](../data-factory/introduction.md) では、データの移動と変換を調整して自動化します。 Azure Data Factory を使用すると、入力データ スライスを処理するために HDInsight Hadoop クラスターをジャスト イン タイムで作成し、処理が完了したらクラスターを削除できます。

Azure Data Factory では、データ ファクトリに 1 つまたは複数のデータ パイプラインを設定できます。 データ パイプラインには、1 つ以上のアクティビティがあります。 次の 2 種類のアクティビティがあります。

* [データ移動アクティビティ](../data-factory/copy-activity-overview.md) - データ移動アクティビティを使用して、ソース データ ストアから宛先データ ストアにデータを移動します。
* [データ変換アクティビティ](../data-factory/transform-data.md) - データ変換アクティビティは、データを変換/処理するために使用します。 HDInsight Hive アクティビティは、Data Factory でサポートされるデータ変換アクティビティの 1 つです。 このチュートリアルでは、Hive 変換アクティビティを使用します。

この記事では、オンデマンドの HDInsight Hadoop クラスターを作成するように Hive アクティビティを構成します。 アクティビティを実行してデータ を処理するときには、次のことが行われます。

1. スライスを処理するために、HDInsight Hadoop クラスターが Just-In-Time 方式で自動的に作成されます。

2. クラスター上で HiveQL スクリプトを実行することによって入力データが処理されます。 このチュートリアルの Hive アクティビティに関連付けられた HiveQL スクリプトは、次のアクションを実行します。

    * 既存のテーブル (*hivesampletable*) を使用して別のテーブル (**HiveSampleOut**) を作成します。
    * **HiveSampleOut** テーブルに、元の *hivesampletable* の特定の列だけを設定します。

3. HDInsight Hadoop クラスターは、処理が完了し、(TimeToLive 設定で) 構成された時間アイドル状態になると、削除されます。 この TimeToLive アイドル時間内に次のデータ スライスを処理できる場合、スライスを処理するために同じクラスターが使用されます。  

## <a name="create-a-data-factory"></a>Data Factory の作成

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューで、 **[`+ Create a resource`]**  >  **[分析]**  >  **[データ ファクトリ]** の順に移動します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png" alt-text="ポータルの Azure Data Factory":::

3. **新しいデータ ファクトリ** タイルに以下の値を入力するか選択します。

    |プロパティ  |値  |
    |---------|---------|
    |名前 | データ ファクトリの名前を入力します。 この名前はグローバルに一意である必要があります。|
    |Version | **V2** のままにします。 |
    |サブスクリプション | Azure サブスクリプションを選択します。 |
    |Resource group | PowerShell スクリプトを使用して作成したリソース グループを選択します。 |
    |場所 | 場所は、リソース グループの作成時に指定した場所に自動的に設定されます。 このチュートリアルでは、場所は **[米国東部]** に設定されます。 |
    |Enable GIT|このボックスはオフにしてください。|

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png" alt-text="Azure portal を使用して Azure Data Factory を作成する":::

4. **［作成］** を選択します データ ファクトリの作成には、2 ～ 4 分ほどかかることがあります。

5. データ ファクトリが作成されると、 **[リソースに移動]** ボタンを含む **デプロイ成功** 通知が届きます。  **[リソースに移動]** を選択して、Data Factory の既定のビューを開きます。

6. **[作成と監視]** を選択して、Azure Data Factory の作成および監視ポータルを起動します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png" alt-text="Azure Data Factory ポータルの概要":::

## <a name="create-linked-services"></a>リンクされたサービスを作成します

このセクションでは、データ ファクトリ内に 2 つのリンクされたサービスを作成します。

* Azure ストレージ アカウントをデータ ファクトリにリンクする、**Azure Storage のリンクされたサービス**。 このストレージは、オンデマンドの HDInsight クラスターによって使用されます。 また、クラスター上で実行される Hive スクリプトも含まれています。
* **オンデマンドの HDInsight のリンクされたサービス**。 Azure Data Factory によって、HDInsight クラスターが自動的に作成され、Hive スクリプトが実行されます。 HDInsight クラスターは、事前に構成された時間だけアイドル状態になったら削除されます。

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する

1. **[始めましょう]** ページの左側のウィンドウで、 **[作成者]** アイコンをクリックします。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png" alt-text="Azure Data Factory のリンクされたサービスを作成する":::

2. ウィンドウの左下隅にある **[接続]** を選択し、 **[+ 新規]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png" alt-text="Azure Data Factory で接続を作成する":::

3. **[New Linked Service]\(新しいリンクされたサービス\)** ダイアログ ボックスで **[Azure Blob Storage]** を選択し、 **[続行]** をクリックします。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png" alt-text="Data Factory 用に Azure Storage のリンクされたサービスを作成する":::

4. ストレージのリンク サービスに次の値を指定します。

    |プロパティ |値 |
    |---|---|
    |名前 |「`HDIStorageLinkedService`」と入力します。|
    |Azure サブスクリプション |ドロップダウン リストからサブスクリプションを選択します。|
    |ストレージ アカウント名 |PowerShell スクリプトの一部として作成した Azure Storage アカウントを選択します。|

    **[テスト接続]** を選択し、成功した場合は **[作成]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png" alt-text="Azure Storage のリンクされたサービスの名前を指定する":::

### <a name="create-an-on-demand-hdinsight-linked-service"></a>オンデマンドの HDInsight のリンクされたサービスを作成する

1. **[+ 新規]** ボタンをもう一度選択して、別のリンクされたサービスを作成します。

2. **[New Linked Service]\(新しいリンクされたサービス\)** ウィンドウで、 **[Compute]** (計算) タブを選択します。

3. **[Azure HDInsight]** を選択し、 **[続行]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png" alt-text="Azure Data Factory 用に HDInsight のリンクされたサービスを作成する":::

4. **[New Linked Service]** (新しいリンク サービス) ウィンドウで 次の値を入力し、残りは既定値のままにしておきます。

    | プロパティ | 値 |
    | --- | --- |
    | 名前 | 「`HDInsightLinkedService`」と入力します。|
    | Type | **[On-demand HDInsight]\(オンデマンド HDInsight\)** を選択します。 |
    | Azure Storage のリンクされたサービス | [`HDIStorageLinkedService`] を選択します。 |
    | クラスターの種類 | **[hadoop]** を選択します。 |
    | Time to Live | HDInsight クラスターを使用できるようにしておく期間を指定します。この期間を過ぎると、クラスターは自動的に削除されます。|
    | サービス プリンシパル ID | 前提条件の 1 つとして作成した Azure Active Directory サービス プリンシパルのアプリケーション ID を指定します。 |
    | サービス プリンシパル キー | Azure Active Directory サービス プリンシパルの認証キーを指定します。 |
    | Cluster name prefix\(クラスター名のプレフィックス\) | データ ファクトリによって作成されるすべてのクラスターの種類にプレフィックスとして追加する値を指定します。 |
    |サブスクリプション |ドロップダウン リストからサブスクリプションを選択します。|
    | リソース グループの選択 | 以前に使用した PowerShell スクリプトの一部として作成したリソース グループを選択します。|
    | OS type/Cluster SSH ユーザー名 | SSH ユーザー名 (通常は`sshuser`) を入力します。 |
    | OS type/Cluster SSH パスワード | SSH ユーザーのパスワードを指定します。 |
    | OS type/Cluster ユーザー名 | クラスターのユーザー名 (通常は`admin`) を入力します。 |
    | OS type/Cluster パスワード | クラスター ユーザーのパスワードを指定します。 |

    **[作成]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png" alt-text="HDInsight のリンクされたサービスの値を指定する":::

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **+** (正符号) ボタンを選択し、 **[パイプライン]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png" alt-text="Azure Data Factory にパイプラインを作成する":::

1. **[アクティビティ]** ツールボックスで **[HDInsight]** を展開し、パイプライン デザイナー画面に **Hive** アクティビティをドラッグします。 **[全般]** タブで、アクティビティの名前を指定します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png" alt-text="Data Factory パイプラインにアクティビティを追加する":::

1. Hive アクティビティが選択されていることを確認し、 **[HDI Cluster]\(HDI クラスター\)** タブを選択します。 **[HDInsight Linked Service]\(HDInsight のリンクされたサービス\)** ドロップダウン リストで、HDInsight 用に以前に作成したリンク サービスの **HDInsightLinkedService** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png" alt-text="パイプラインの HDInsight クラスターの詳細を指定する":::

1. **[スクリプト]** タブを選択し、次の手順を実行します。

    1. **[スクリプトにリンクされたサービス]** でドロップダウン リストから **[HDIStorageLinkedService]** を選択します。 この値は、以前に作成したストレージのリンクされたサービスです。

    1. **[ファイル パス]** で **[ストレージを参照]** を選択し、サンプル Hive スクリプトがある場所に移動します。 以前に PowerShell スクリプトを実行した場合、この場所は `adfgetstarted/hivescripts/partitionweblogs.hql` になります。

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png" alt-text="パイプラインの Hive スクリプトの詳細を指定する":::

    1. **[詳細設定]**  >  **[パラメーター]** で、 **[`Auto-fill from script`]** を選択します。 このオプションを選択すると、実行時に値を必要とする、Hive スクリプトのパラメーターが検索されます。

    1. **[値]** ボックスに、`wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` 形式で既存のフォルダーを追加します。 パスの大文字と小文字は区別されます。 このパスにスクリプトの出力が格納されます。 ストレージ アカウントではセキュリティで保護された転送が既定で有効であることが必要になったため、`wasbs` スキーマが不可欠です。

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png" alt-text="Hive スクリプトのパラメーターを指定する":::

1. **[検証]** を選択してパイプラインを検証します。 **>>** (右矢印) ボタンを選択して、検証ウィンドウを閉じます。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png" alt-text="Azure Data Factory パイプラインを検証する":::

1. 最後に、 **[すべて発行]** を選択して、成果物を Azure Data Factory に発行します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png" alt-text="Azure Data Factory パイプラインを発行する":::

## <a name="trigger-a-pipeline"></a>パイプラインをトリガーする

1. デザイナー画面のツール バーで、 **[Add trigger] (トリガーを追加)**  >  **[Trigger Now]\(今すぐトリガー\)** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png" alt-text="Azure Data Factory パイプラインをトリガーする":::

2. ポップアップ サイド バーで **[OK]** を選択します。

## <a name="monitor-a-pipeline"></a>パイプラインを監視する

1. 左側で **[監視]** タブに切り替えます。 **[Pipeline Runs]\(パイプラインの実行\)** の一覧にパイプライン実行が表示されます。 **[状態]** 列で実行の状態を確認します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png" alt-text="Azure Data Factory パイプラインを監視する":::

1. **[最新の情報に更新]** を選択して、状態を更新します。

1. **[View Activity Runs]\(アクティビティの実行の表示\)** アイコンをクリックして、パイプラインに関連付けられているアクティビティの実行を表示することもできます。 作成したパイプラインにはアクティビティが 1 つしかないため、次のスクリーンショットでは、アクティビティの実行が 1 つしか表示されていません。 前のビューに戻るには、ページの上部にある **[パイプライン]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png" alt-text="Azure Data Factory パイプライン アクティビティを監視する":::

## <a name="verify-the-output"></a>出力を検証する

1. 出力を検証するには、Azure Portal 上で、このチュートリアルで使用したストレージ アカウントに移動します。 次のフォルダーまたはコンテナーが表示されます。

    * パイプラインの一部として実行された Hive スクリプトの出力が含まれた **adfgerstarted/outputfolder** が表示されます。

    * **adfhdidatafactory-\<linked-service-name>-\<timestamp>** コンテナーが表示されます。 このコンテナーは、パイプライン実行の一環として作成された HDInsight クラスターの既定のストレージの場所です。

    * Azure Data Factory のジョブ ログがある **adfjobs** コンテナーが表示されます。  

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png" alt-text="Azure Data Factory パイプライン出力を検証する":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

オンデマンドの HDInsight クラスターを作成した場合、HDInsight クラスターを明示的に削除する必要はありません。 クラスターは、パイプラインの作成時に指定した構成に基づいて削除されます。 クラスターを削除した後も、クラスターに関連付けられているストレージ アカウントは引き続き存在します。 データをそのまま保持できるように、この動作は仕様です。 データを保持する必要がない場合は、作成したストレージ アカウントを削除してかまいません。

また、このチュートリアルで作成したリソース グループ全体を削除することもできます。 このプロセスにより、作成したストレージ アカウントと Azure Data Factory が削除されます。

### <a name="delete-the-resource-group"></a>リソース グループを削除します

1. [Azure Portal](https://portal.azure.com) にサインオンします。
1. 左側のウィンドウの **[リソース グループ]** を選択します。
1. PowerShell スクリプトで作成したリソース グループの名前を選択します。 一覧表示されるリソース グループが多すぎる場合は、フィルターを使用します。 リソース グループが開きます。
1. **[リソース]** タイルには、リソース グループを他のプロジェクトと共有する場合を除き、既定のストレージ アカウントとデータ ファクトリが表示されます。
1. **[リソース グループの削除]** を選択します。 この操作を実行すると、ストレージ アカウントと、そのストレージ アカウントに格納されているデータが削除されます。

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png" alt-text="Azure portal でのリソース グループの削除":::

1. リソース グループ名を入力して削除を確認し、 **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Data Factory を使用してオンデマンドの HDInsight クラスターを作成し、Apache Hive ジョブを実行する方法を説明しました。 次の記事に進み、HDInsight クラスターをカスタム構成で作成する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure HDInsight クラスターをカスタム構成で作成する](hdinsight-hadoop-provision-linux-clusters.md)