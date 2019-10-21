---
title: Shared Access Signature を使用してアクセスを制限する - Azure HDInsight
description: Azure Storage の BLOB に格納されたデータへの HDInsight のアクセスを制限するために、Shared Access Signature を使用する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 46cf7d3dd7efecff0280320c100af432367e25f2
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180829"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Azure Storage の Shared Access Signature を使用して HDInsight でのデータへのアクセスを制限する

HDInsight には、クラスターに関連付けられた Azure Storage アカウントのデータへのフル アクセス権があります。 BLOB コンテナーで Shared Access Signature を使って、データへのアクセスを制限できます。 Shared Access Signature (SAS) は、データへのアクセスを制限できる Azure Storage アカウントの機能です。 たとえば、データに読み取り専用のアクセス権を提供します。

> [!IMPORTANT]  
> Apache Ranger を使うソリューションでは、ドメインに参加している HDInsight を使うことを検討してください。 詳しくは、「[ドメイン参加済み HDInsight クラスターの構成](./domain-joined/apache-domain-joined-configure.md)」をご覧ください。

> [!WARNING]  
> HDInsight には、クラスターの既定のストレージへのフル アクセス権が必要です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](./hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* 既存の[ストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md)。  

* PowerShell を使用する場合は、[Az モジュール](https://docs.microsoft.com/powershell/azure/overview)が必要です。

* Azure CLI を使用したい場合で、なおかつまだインストールしていない場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

* [Python](https://www.python.org/downloads/) バージョン 2.7 以降 (Python を使用する場合)。

* C# を使用する場合は、Visual Studio のバージョンが 2013 以降である必要があります。

* ストレージ アカウントの [URI スキーム](./hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage で安全な転送が有効になっている場合、URI は `wasbs://` になります。 [安全な転送](../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

* Shared Access Signature の追加先となる既存の HDInsight クラスター。 ない場合は、Azure PowerShell を使用してクラスターを作成し、クラスターの作成時に Shared Access Signature を追加することができます。

* サンプル ファイルは、[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) にあります。 このリポジトリには、以下が含まれます。

  * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Visual Studio プロジェクト
  * HDInsight で使用するストレージ コンテナー、保存済みのポリシーおよび SAS を作成できる Python スクリプト
  * HDInsight クラスターを作成して、SAS を使用するように構成できる PowerShell スクリプト 以下では、更新したバージョンを使用しています。
  * サンプル ファイル: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Shared Access Signature

Shared Access Signature には、次の 2 つのフォームがあります。

* アドホック: 開始時刻、有効期限、SAS へのアクセス許可がすべて、SAS URI で指定されます。

* 保存されているアクセス ポリシー: 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナーなど) で定義されています。 ポリシーを使用して、1 つ以上の Shared Access Signature の制約を管理できます。 保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

1 つの重要なシナリオ、失効では、この 2 つの形式の相違点が重要です。 SAS は URL であるため、取得したユーザーはだれでも、どのユーザーが最初に要求したかに関係なく、SAS を使用できます。 SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。 配布された SAS は、次の 4 つの状況のいずれかになるまで有効です。

1. SAS に指定された有効期限に達した。

2. SAS によって参照されている保存されたアクセス ポリシーで指定されている有効期限に達した。 次のシナリオの場合、有効期限に達します。

    * 指定された期間が経過した。
    * 保存されているアクセス ポリシーが、過去の有効期限に変更された。 有効期限の変更は、SAS を失効させる方法の 1 つです。

3. SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。 保存されているアクセス ポリシーを、同じ名前で再作成すると、前のポリシーの SAS トークンがすべて有効になります (SAS の有効期限が過ぎていない場合)。 SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使ってください。

4. SAS の作成に使用したアカウント キーが再度生成された。 キーを再生成すると、前のキーを使うすべてのアプリケーションが、認証に失敗します。 すべてのコンポーネントを新しいキーに更新してください。

> [!IMPORTANT]  
> Shared Access Signature URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。 保存済みのアクセス ポリシーが指定されていない場合、Shared Access Signature を取り消すにはアカウント キーを変更する以外に方法はありません。

保存済みのアクセス ポリシーを常に使用することをお勧めします。 保存済みのポリシーを使用している場合、必要に応じて署名を失効させるか、有効期限を延長することができます。 ドキュメント内のこれらの手順は、保存済みのアクセス ポリシーを使用して、SAS を生成します。

Shared Access Signature の詳細については、「 [SAS モデルについて](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

## <a name="create-a-stored-policy-and-sas"></a>保存済みのポリシーと SAS を作成する

それぞれの方法の最後に生成される SAS トークンを保存します。 実際のトークンの例を次に示します。

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>PowerShell の使用

`RESOURCEGROUP`、`STORAGEACCOUNT`、`STORAGECONTAINER` は、既存のストレージ コンテナーの適切な値に置き換えてください。 ディレクトリを `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` に変更するか、または `Set-AzStorageblobcontent` の絶対パスを含むように `-File` パラメーターを修正します。 次の PowerShell コマンドを入力します。

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Azure CLI の使用

このセクションの変数の使用は、Windows 環境を前提としています。 bash やその他の環境では、若干の調整が必要となります。

1. `STORAGEACCOUNT` と `STORAGECONTAINER` は、既存のストレージ コンテナーの適切な値に置き換えてください。

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. 取得したプライマリ キーを、後で使用するために変数に設定します。 `PRIMARYKEY` を前の手順で取得した値に置き換えてから、次のコマンドを入力します。

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. ディレクトリを `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` に変更するか、または `az storage blob upload` の絶対パスを含むように `--file` パラメーターを修正します。 残りのコマンドを実行します。

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Python の使用

`SASToken.py` ファイルを開き、`storage_account_name`、`storage_account_key`、`storage_container_name` を、既存のストレージ コンテナーの適切な値に置き換えてから、スクリプトを実行します。

エラー メッセージ "`ImportError: No module named azure.storage`" が返された場合は、`pip install --upgrade azure-storage` を実行する必要があります。

### <a name="using-c"></a>C# の使用

1. Visual Studio でソリューションを開きます。

2. ソリューション エクスプローラーで **[SASExample]** プロジェクトを右クリックし、 **[プロパティ]** を選択します。

3. **[設定]** を選択し、次のエントリに値を追加します。

   * StorageConnectionString: 保存済みのポリシーと SAS を作成するストレージ アカウントの接続文字列。 `myaccount` がストレージ アカウントの名前で、`mykey` がストレージ アカウントのキーである場合、形式は `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` になります。

   * ContainerName: アクセスを制限するストレージ アカウントのコンテナー。

   * SASPolicyName: 作成する保存済みのポリシーに使用する名前。

   * FileToUpload: コンテナーにアップロードされるファイルのパス。

4. プロジェクトを実行します。 SAS ポリシー トークン、ストレージ アカウント名、コンテナー名を保存します。 これらの値は、HDInsight クラスターにストレージ アカウントを関連付けるときに使用されます。

## <a name="use-the-sas-with-hdinsight"></a>HDInsight での SAS の使用

HDInsight クラスターを作成するときは、プライマリ ストレージ アカウントを指定する必要があり、任意で追加のストレージ アカウントを指定することもできます。 これらのストレージを追加する両方の方法で、使用するストレージ アカウントとコンテナーへのフル アクセスが必要です。

コンテナーへのアクセスを制限するために Shared Access Signature を使用するには、カスタム エントリをクラスターの **core-site** 構成に追加します。 エントリは、クラスターの作成中に PowerShell を使用して、またはクラスターの作成後に Ambari を使用して追加することができます。

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS を使用するクラスターの作成

`CLUSTERNAME`、`RESOURCEGROUP`、`DEFAULTSTORAGEACCOUNT`、`STORAGECONTAINER`、`STORAGEACCOUNT`、`TOKEN` を適切な値に置き換えます。 次の PowerShell コマンドを入力します。

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> HTTP(S) または SSH のユーザー名とパスワードを求められると、次の条件を満たすパスワードを指定する必要があります。
>
> * 10 文字以上にする。
> * 数字を 1 つ以上含める。
> * 英数字以外を 1 文字以上含める。
> * 大文字または小文字を 1 文字以上含める。

このスクリプトが完了するにはしばらく時間がかかります。通常、約 15 分です。 エラーが発生することなく、このスクリプトが完了すると、クラスターが作成されています。

### <a name="use-the-sas-with-an-existing-cluster"></a>既存のクラスターで SAS を使用する

既存のクラスターがある場合は、次の手順を使用して、SAS を **core-site** 構成に追加することができます。

1. クラスターの Ambari Web UI を開きます。 このページのアドレスは `https://YOURCLUSTERNAME.azurehdinsight.net` です。 入力を要求されたら、クラスターを作成するときに使用した管理者名 (admin) とパスワードを使用してクラスターを認証します。

2. Ambari Web UI の左側から、 **[HDFS]** を選択して、ページ中央にある **[Configs]** タブを選択します。

3. **[詳細設定]** タブをクリックし、 **[カスタム core-site]** セクションが表示されるまでスクロールします。

4. **[カスタム core-site]** セクションを展開して、最後までスクロールし、 **[プロパティの追加...]** リンクを選択します。 **[キー]** と **[値]** フィールドに、次の値を使用します。

   * **キー**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **値**: これまでに実行したいずれかの方法で取得した SAS。

     `CONTAINERNAME` を C# または SAS のアプリケーションで使用したコンテナー名に置き換えます。 `STORAGEACCOUNTNAME` には、使用したストレージ アカウント名を指定します。

5. **[追加]** ボタンをクリックしてこのキーと値を保存し、 **[保存]** ボタンをクリックして構成の変更を保存します。 プロンプトが表示されたら、変更の説明 (「SAS ストレージ アクセスの追加」など) を追加し、 **[保存]** をクリックします。

    変更が完了したら、 **[OK]** をクリックします。

   > [!IMPORTANT]  
   > 変更を有効にするには、複数のサービスを再起動する必要があります。

6. Ambari Web UI で、左側の一覧から **[HDFS]** を選択して、右側の **[サービス アクション]** ドロップダウン リストから **[すべて再起動]** を選択します。 メッセージが表示されたら、 __[Confirm Restart All]\(すべての再起動の確認\)__ を選択します。

    この手順を MapReduce2 と YARN に対して繰り返します。

7. これらのサービスが再起動されたら、各サービスを選択して、 **[サービス アクション]** ドロップダウンからメンテナンス モードを無効化します。

## <a name="test-restricted-access"></a>制限付きアクセスをテストする

次の手順を使用して、SAS ストレージ アカウント上の項目を読み取りまたは一覧表示のみすることができることを確認します。

1. クラスターに接続します。 `CLUSTERNAME` をクラスターの名前に置き換えてから、次のコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. コンテナーの内容を一覧表示するには、プロンプトから次のコマンドを使用します。

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    `SASCONTAINER` を SAS ストレージ アカウントに対して作成されたコンテナーの名前に置き換えます。 `SASACCOUNTNAME` を SAS に使用するストレージ アカウントの名前に置き換えます。

    一覧には、コンテナーと SAS が作成されたときにアップロードされたファイルも含まれます。

3. 次のコマンドを使用して、ファイルの内容を読み取ることができることを確認します。 前の手順と同様に、`SASCONTAINER` と `SASACCOUNTNAME` を置き換えます。 `sample.log` を前のコマンドで表示されたファイルの名前に置き換えます。

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    このコマンドにより、ファイルの内容が一覧表示されます。

4. 次のコマンドを使用して、ローカル ファイル システムにファイルをダウンロードします。

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    このコマンドは、**testfile.txt** という名前のローカル ファイルにファイルをダウンロードします。

5. 次のコマンドを使用して、ローカル ファイルを SAS ストレージ上の **testupload.txt** という名前の新しいファイルにアップロードします。

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    次のテキストのようなメッセージが返されます。

        put: java.io.IOException

    このエラーは、ストレージの場所が読み取りと一覧表示のみであるために発生します。 次のコマンドを使用して、書き込み可能なクラスターの既定のストレージ上にデータを置きます。

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    このとき、操作が正常に完了する必要があります。

## <a name="next-steps"></a>次の手順

HDInsight クラスターにアクセスが制限されたストレージを追加する方法を学習しました。クラスターのデータと連携するその他の方法を確認してください。

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

