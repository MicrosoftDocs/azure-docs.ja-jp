---
title: Azure HDInsight の Hadoop で Data Lake Storage Gen1 を使用する
description: Azure Data Lake Storage Gen1 のデータに対してクエリを実行し、分析結果を格納する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945403"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する

> [!Note]
> パフォーマンスを改善し、新機能を利用するには、[Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) を使用して新しい HDInsight クラスターをデプロイします。

HDInsight クラスターでデータを分析するには、[`Azure Blob storage`](../storage/common/storage-introduction.md)、[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)、または [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) のいずれかにデータを格納します。 いずれのストレージ オプションでも、計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

この記事では、HDInsight クラスターでの Data Lake Storage Gen1 の動作について説明します。 HDInsight クラスターでの Azure BLOB ストレージの動作については、「[Azure HDInsight クラスターで Azure BLOB ストレージを使用する](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。 HDInsight クラスターの作成について詳しくは、[HDInsight での Apache Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

> [!NOTE]  
> Data Lake Storage Gen1 へのアクセスには必ずセキュリティで保護されたチャネルが使われるため、`adls` ファイルシステム スキーム名はありません。 常に `adl` を使用します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight クラスターの可用性

Apache Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス中に、Azure Storage 内の BLOB コンテナーを既定のファイル システムとして指定します。 また、HDInsight 3.5 以降のバージョンでは、Azure BLOB Storage または Azure Data Lake Storage Gen1 のいずれかを既定のファイル システムとして選択できます (ただし、いくつかの例外があります)。 クラスターとストレージ アカウントは、同じリージョンに置く必要があります。

HDInsight クラスターでは、2 つの方法で Data Lake Storage Gen1 を使用できます。

* 既定のストレージとして
* 既定のストレージが Azure BLOB ストレージのときの追加ストレージとして

現時点では、Data Lake Storage Gen1 を既定ストレージおよび追加のストレージ アカウントとして使用することは、HDInsight クラスターの一部の種類/バージョンでのみサポートされています。

| HDInsight クラスターの種類 | 既定のストレージとしての Data Lake Storage Gen1 | 追加のストレージとしての Data Lake Storage Gen1| Notes |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight バージョン 4.0 | いいえ | いいえ |ADLS Gen1 は、HDInsight 4.0 ではサポートされていません |
| HDInsight Version 3.6 | はい | はい | HBase を除く|
| HDInsight Version 3.5 | はい | はい | HBase を除く|
| HDInsight Version 3.4 | いいえ | はい | |
| HDInsight Version 3.3 | いいえ | いいえ | |
| HDInsight Version 3.2 | いいえ | はい | |
| Storm | | |Data Lake Storage Gen1 を使って、Storm トポロジからデータを書き込むことができます。 また、Data Lake Storage Gen1 を、Storm トポロジから読み取ることができる参照データとして使用することもできます。|

> [!WARNING]  
> HDInsight HBase は、Azure Data Lake Storage Gen1 ではサポートされていません。

Data Lake Storage Gen1 を追加のストレージ アカウントとして使用しても、パフォーマンスには影響しません。 クラスターから Azure Blob Storage に対して読み取りまたは書き込みを行う機能についても同様です。

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>既定のストレージとして Data Lake Storage Gen1 を使用する

Data Lake Storage Gen1 を既定のストレージとして HDInsight がデプロイされている場合、クラスター関連のファイルは `adl://mydatalakestore/<cluster_root_path>/` に格納されます。`<cluster_root_path>` は、Data Lake Storage にお客様が作成したフォルダーの名前です。 クラスターごとにルート パスを指定することで、複数のクラスターに対して同じ Data Lake Storage アカウントを使うことができます。 このため、次の場所にセットアップを設定できます。

* Cluster1 は、パス `adl://mydatalakestore/cluster1storage` を使用できます
* Cluster2 は、パス `adl://mydatalakestore/cluster2storage` を使用できます

両方のクラスターが同じ Data Lake Storage Gen1 アカウント **mydatalakestore** を使用していることに注意してください。 クラスターそれぞれが、Data Lake Storage で独自のルート ファイルシステムにアクセスします。 Azure portal のデプロイ エクスペリエンスでは、ルート パスに **/clusters/\<clustername>** などのフォルダー名を使用するように求められます。

Data Lake Storage Gen1 を既定のストレージとして使用するには、サービス プリンシパルに次のパスへのアクセスを許可する必要があります。

* Data Lake Storage Gen1 アカウントのルート。  例: adl://mydatalakestore/。
* すべてのクラスター フォルダー用のフォルダー。  例: adl://mydatalakestore/clusters。
* クラスター用のフォルダー。  例: adl://mydatalakestore/clusters/cluster1storage。

サービス プリンシパルの作成とアクセスの許可の詳細については、「Data Lake Storage のアクセスを構成する」を参照してください。

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>クラスターの作成で使用するために Azure Key Vault から証明書を抽出する

サービス プリンシパルの証明書が Azure Key Vault に格納されている場合は、証明書を正しい形式に変換する必要があります。 次のコード スニペットは、変換を実行する方法を示しています。

最初に、Key Vault から証明書をダウンロードし、`SecretValueText` を抽出します。

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

次に、`SecretValueText` を証明書に変換します。

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

すると、次のスニペットに示すように、`$identityCertificate` を使用して新しいクラスターをデプロイできるようになります。

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>追加のストレージとして Data Lake Storage Gen1 を使用する

Data Lake Storage Gen1 を、クラスターの追加のストレージとして使用することもできます。 この場合、クラスターの既定のストレージは、Azure Blob Storage または Azure Data Lake Storage Gen1 のアカウントのいずれかにできます。 追加のストレージとしての Azure Data Lake Storage Gen1 に格納されているデータに対して HDInsight ジョブを実行するときは、完全修飾パスを使用します。 次に例を示します。

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

現在、URL には **cluster_root_path** がありません。 この理由は、この場合、Data Lake Storage は既定のストレージではないからです。 したがって、ファイルへのパスを指定することだけで済みます。

Data Lake Storage Gen1 を追加のストレージとして使用するには、ファイルが格納されているパスへのアクセスをサービス プリンシパルに許可します。  次に例を示します。

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

サービス プリンシパルの作成とアクセスの許可の詳細については、「Data Lake Storage のアクセスを構成する」を参照してください。

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>複数の Data Lake Storage Gen1 アカウントを使用する

Data Lake Storage アカウントを追加として追加したり、複数の Data Lake Storage アカウントを追加したりすることができます。 1 つまたは複数の Data Lake Storage アカウント内のデータに対する HDInsight クラスターのアクセス許可を付与します。 下の「Data Lake Storage Gen1 のアクセスの構成」をご覧ください。

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 のアクセスの構成

HDInsight クラスターから Azure Data Lake Storage Gen1 へのアクセスを構成するには、Azure Active Directory (Azure AD) のサービス プリンシパルが必要です。 サービス プリンシパルを作成できるのは、Azure AD 管理者だけです。 サービス プリンシパルは証明書で作成する必要があります。 詳細については、「[クイック スタート: HDInsight のクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)」および「[自己署名証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)」を参照してください。

> [!NOTE]  
> Azure Data Lake Storage Gen1 を HDInsight クラスターの追加のストレージとして使用する場合は、この記事で説明されているように、クラスターを作成するときにそうすることを強くお勧めします。 Azure Data Lake Storage Gen1 を既存の HDInsight クラスターに追加のストレージとして追加することは、サポートされていないシナリオです。

アクセス制御モデルの詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

## <a name="access-files-from-the-cluster"></a>クラスターからファイルにアクセスする

複数の方法で、HDInsight クラスターから Data Lake Storage のファイルにアクセスできます。

* **完全修飾名の使用**。 この方法により、アクセスするファイルへの完全パスを指定します。

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **短縮されたパスの使用**。 この方法により、クラスター ルートへのパスを次に置き換えます。

    ```
    adl:///<file path>
    ```

* **相対パスの使用**。 この方法により、アクセスするファイルへの相対パスのみを指定します。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>データ アクセスの例

例は、クラスターのヘッド ノードへの [ssh 接続](./hdinsight-hadoop-linux-use-ssh-unix.md)に基づいています。 この例では、3 つの URI スキームがすべて使用されています。 `DATALAKEACCOUNT` と `CLUSTERNAME` を関連する値に置き換えます。

#### <a name="a-few-hdfs-commands"></a>いくつかの hdfs コマンド

1. ローカル ストレージにファイルを作成します。

    ```bash
    touch testFile.txt
    ```

1. クラスター ストレージにディレクトリを作成します。

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. ローカル ストレージからクラスター ストレージにデータをコピーします。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. クラスター ストレージのディレクトリの内容を一覧表示します。

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive テーブルの作成

3 つのファイルの場所は説明目的で提示されています。 実際の実行では、`LOCATION` エントリを 1 つだけ使用します。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Ambari からストレージ パスを特定する

構成済みの既定のストアへの完全なパスを特定するには、 **[HDFS]**  >  **[Configs]\(構成\)** の順に移動し、フィルター入力ボックスに `fs.defaultFS` と入力します。

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成する

Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成する方法の詳しい手順については、以下のリンクを参照してください。

* [ポータルの使用](./hdinsight-hadoop-provision-linux-clusters.md)
* [PowerShell の使用 (Data Lake Storage Gen1 を既定のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell の使用 (Data Lake Storage Gen1 を追加のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure テンプレートの使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>HDInsight 証明書を更新し、Data Lake Storage Gen1 にアクセスする

次の PowerShell コード サンプルは、ローカル ファイルまたは Azure Key Vault の証明書を読み取り、Azure Data Lake Storage Gen1 にアクセスできるように新しい証明書で HDInsight クラスターを更新します。 独自の HDInsight クラスター名、リソース グループ名、サブスクリプション ID、`app ID`、証明書のローカル パスを指定します。 入力を求められたら、パスワードを入力します。

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>次のステップ

この記事では、HDInsight で HDFS と互換性のある Azure Data Lake Storage Gen1 を使う方法について説明しました。 このストレージにより、適応性のある長期的なアーカイブ データの取得ソリューションを構築できます。 また、HDInsight を使用して、格納されている構造化および非構造化データ内の情報のロックを解除します。

詳細については、次を参照してください。

* [クイック スタート: HDInsight のクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)
* [Azure PowerShell を使用して、Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [Azure Blob Storage の Shared Access Signature を使用した HDInsight でのデータへのアクセスの制限](hdinsight-storage-sharedaccesssignature-permissions.md)