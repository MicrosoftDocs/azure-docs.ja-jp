---
title: Azure HDInsight の Hadoop で Data Lake Storage Gen1 を使用する
description: Azure Data Lake Storage Gen1 のデータに対してクエリを実行し、分析結果を格納する方法について説明します。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: ed8884462030e10625f332b182bd900e833f34f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59272735"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する

> [!Note] 
> パフォーマンスを改善し、新機能を利用するには、[Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) を使用して新しい HDInsight クラスターをデプロイします。

HDInsight クラスターでデータを分析するには、[Azure Storage](../storage/common/storage-introduction.md)、[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)、または [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md) のいずれかにデータを格納できます。 いずれのストレージ オプションでも、計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

この記事では、HDInsight クラスターでの Data Lake Storage Gen1 の動作について説明します。 THDInsight クラスターでの Data Lake Store の動作については、「[Azure HDInsight クラスターで Azure Storage を使用する](hdinsight-hadoop-use-blob-storage.md)」をご覧ください。 HDInsight クラスターの作成について詳しくは、[HDInsight での Apache Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

> [!NOTE]  
> Data Lake Storage Gen1 へのアクセスには必ずセキュリティで保護されたチャネルが使われるため、`adls` ファイルシステム スキーム名はありません。 常に `adl` を使用します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight クラスターの可用性

Apache Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス時に、Azure Storage 内の BLOB コンテナーを既定のファイル システムとして指定できます。また、HDInsight 3.5 以降のバージョンでは、Azure Storage と Azure Data Lake Storage Gen1 のいずれかを既定のファイル システムとして選択できます (いくつか例外があります)。 

HDInsight クラスターでは、2 つの方法で Data Lake Storage Gen1 を使用できます。

* 既定のストレージとして
* 既定のストレージが Azure Storage Blob のときの追加ストレージとして

現時点では、Data Lake Storage Gen1 を既定ストレージおよび追加のストレージ アカウントとして使うことができるのは、HDInsight クラスターの一部の種類/バージョンのみです。

| HDInsight クラスターの種類 | 既定のストレージとしての Data Lake Storage Gen1 | 追加のストレージとしての Data Lake Storage Gen1| メモ |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight バージョン 4.0 | いいえ  | いいえ  |ADLS Gen1 は、HDInsight 4.0 ではサポートされていません。 |
| HDInsight Version 3.6 | はい | はい | HBase は例外|
| HDInsight Version 3.5 | はい | はい | HBase は例外|
| HDInsight Version 3.4 | いいえ  | はい | |
| HDInsight Version 3.3 | いいえ  | いいえ  | |
| HDInsight Version 3.2 | いいえ  | はい | |
| Storm | | |Data Lake Storage Gen1 を使って、Storm トポロジからデータを書き込むことができます。 また、Data Lake Storage を、Storm トポロジから読み取ることができる参照データとして使用することもできます。|

> [!WARNING]  
> HDInsight HBase は、Azure Data Lake Storage Gen1 ではサポートされていません。

Data Lake Storage Gen1 を追加のストレージ アカウントとして使っても、クラスターから Azure ストレージに対する読み取り/書き込みのパフォーマンスや機能には影響しません。

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>既定のストレージとして Data Lake Storage Gen1 を使用する

Data Lake Storage Gen1 を既定のストレージとして HDInsight がデプロイされている場合、クラスター関連のファイルは `adl://mydatalakestore/<cluster_root_path>/` に格納されます。`<cluster_root_path>` は、Data Lake Storage にお客様が作成したフォルダーの名前です。 クラスターごとにルート パスを指定することで、複数のクラスターに対して同じ Data Lake Storage アカウントを使うことができます。 このため、次の場所にセットアップを設定できます。

* Cluster1 は、パス `adl://mydatalakestore/cluster1storage` を使用できます
* Cluster2 は、パス `adl://mydatalakestore/cluster2storage` を使用できます

両方のクラスターが同じ Data Lake Storage Gen1 アカウント **mydatalakestore** を使用していることに注意してください。 クラスターそれぞれが、Data Lake Storage で独自のルート ファイルシステムにアクセスします。 特に Azure Portal をデプロイすると、ルート パスの **/clusters/\<clustername >** などのフォルダー名を使用するよう求められます。

既定のストレージとして Data Lake Storage Gen1 を使うには、サービス プリンシパルに次のパスへのアクセスを許可する必要があります。

- Data Lake Storage Gen1 アカウントのルート。  例: adl://mydatalakestore/。
- すべてのクラスター フォルダー用のフォルダー。  例: adl://mydatalakestore/clusters。
- クラスター用のフォルダー。  例: adl://mydatalakestore/clusters/cluster1storage。

サービス プリンシパルの作成とアクセスの許可の詳細については、「Data Lake Storage のアクセスを構成する」を参照してください。

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>クラスターの作成で使用するために Azure Key Vault から証明書を抽出する

新しいクラスターの既定のストレージとして Azure Data Lake Storage Gen1 を設定する際に、サービス プリンシパルの証明書が Azure Key Vault に格納されている場合は、証明書を正しい形式に変換するためにいくつか追加の手順が必要になります。 この変換の実行方法を、次のコード スニペットで示します。

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

Data Lake Storage Gen1 を、クラスターの追加のストレージとして使用することもできます。 この場合、クラスターの既定のストレージとしては、Azure Storage Blob アカウントまたは Data Lake Storage アカウントを使うことができます。 追加のストレージとしての Data Lake Storage に格納されているデータに対して HDInsight ジョブを実行する場合は、ファイルへの完全修飾パスを使う必要があります。 例: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

URL に **cluster_root_path** がないことに注意してください。 これは、Data Lake Storage が既定のストレージでないためです。必要な操作は、ファイルへのパスを指定することだけです。

追加のストレージとして Data Lake Storage Gen1 を使うには、ファイルが保存されているパスへのアクセスをサービス プリンシパルに許可することだけが必要です。  例: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

サービス プリンシパルの作成とアクセスの許可の詳細については、「Data Lake Storage のアクセスを構成する」を参照してください。


## <a name="use-more-than-one-data-lake-storage-accounts"></a>複数の Data Lake Storage アカウントを使用する

Data Lake Storage アカウントを追加ストレージとして使い、複数の Data Lake Storage アカウントを追加するには、1 つ以上の Data Lake Storage アカウント内のデータに HDInsight クラスターのアクセス許可を付与します。 「Data Lake Storage のアクセスを構成する」を参照してください。

## <a name="configure-data-lake-storage-access"></a>Data Lake Storage のアクセスを構成する

HDInsight クラスターから Data Lake Storage へのアクセスを構成するには、Azure Active Directory (Azure AD) のサービス プリンシパルが必要です。 サービス プリンシパルを作成できるのは、Azure AD 管理者だけです。 サービス プリンシパルは証明書で作成する必要があります。 詳細については、「[クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」および「[自己署名証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)」を参照してください。

> [!NOTE]  
> Azure Data Lake Storage Gen1 を HDInsight クラスターの追加のストレージとして使用する場合は、この記事で説明されているように、クラスターを作成するときにそうすることを強くお勧めします。 Azure Data Lake Storage Gen1 を既存の HDInsight クラスターに追加のストレージとして追加することは、サポートされていないシナリオです。
>

## <a name="access-files-from-the-cluster"></a>クラスターからファイルにアクセスする

複数の方法で、HDInsight クラスターから Data Lake Storage のファイルにアクセスできます。

* **完全修飾名の使用**。 この方法により、アクセスするファイルへの完全パスを指定します。

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **短縮されたパスの使用**。 この方法により、クラスター ルートへのパスを adl:/// に置き換えます。 上記の例では `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` を `adl:///` に置き換えることができます。

        adl:///<file path>

* **相対パスの使用**。 この方法により、アクセスするファイルへの相対パスのみを指定します。 たとえば、そのファイルへの完全パスが次のとおりだとします。

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    この sample.log ファイルには、次の相対パスを使用してアクセスできます。

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成する

Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成する方法の詳しい手順については、以下のリンクを参照してください。

* [ポータルの使用](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [PowerShell の使用 (Data Lake Storage Gen1 を既定のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell の使用 (Data Lake Storage Gen1 を追加のストレージとして使用)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure テンプレートの使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>HDInsight 証明書を更新し、Data Lake Storage Gen1 にアクセスする

次の PowerShell コード サンプルは、ローカル ファイルまたは Azure Key Vault の証明書を読み取り、Azure Data Lake Storage Gen1 にアクセスできるように新しい証明書で HDInsight クラスターを更新します。 独自の HDInsight クラスター名、リソース グループ名、サブスクリプション ID、アプリ ID、証明書のローカル パスを指定します。 入力を求められたら、パスワードを入力します。

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
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

## <a name="next-steps"></a>次の手順
この記事では、HDInsight で HDFS と互換性のある Azure Data Lake Storage Gen1 を使う方法について説明しました。 これにより、収集したデータを長期にわたって格納できるスケーラブルなソリューションを構築できます。さらに HDInsight を使用すると、格納されている構造化データと非構造化データから有益な情報を得ることができます。

詳細については、次を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Azure PowerShell を使用して、Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [HDInsight での Apache Hive の使用][hdinsight-use-hive]
* [HDInsight での Apache Pig の使用][hdinsight-use-pig]
* [Azure Storage の Shared Access Signature を使用した HDInsight でのデータへのアクセスの制限][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
