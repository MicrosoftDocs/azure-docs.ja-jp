---
title: PowerShell - Data Lake Storage Gen1 を使用する HDInsight - アドオン ストレージ - Azure
description: Azure PowerShell を使用して、Azure Data Lake Storage Gen1 を追加のストレージとして使用する HDInsight クラスターを構成する方法について説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b7cb9d5c5c2ca850678d3f3194a9af8de526ada4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103392"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Azure PowerShell を使用して、Azure Data Lake Storage Gen1 を (追加のストレージとして) 使用する HDInsight クラスターを作成する

> [!div class="op_single_selector"]
> * [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell を使用して、Azure Data Lake Storage Gen1 を **追加のストレージとして** 使用する HDInsight クラスターを構成する方法について説明します。 既定のストレージとして Data Lake Storage Gen1 を使用する HDInsight クラスターの作成方法については、[既定のストレージとして Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)に関する記事をご覧ください。

> [!NOTE]
> Data Lake Storage Gen1 を HDInsight クラスターの追加ストレージとして使用する場合は、この記事で説明されているように、クラスターを作成するときにそうすることを強くお勧めします。 Data Lake Storage Gen1 を既存の HDInsight クラスターに追加のストレージとして追加することは、複雑なプロセスであり、エラーも発生しやすくなります。
>

サポートされている種類のクラスターでは、Data Lake Storage Gen1 を既定のストレージまたは追加のストレージ アカウントとして使用できます。 Data Lake Storage Gen1 を追加のストレージとして使用した場合、クラスターの既定のストレージ アカウントは Azure Blob Storage (WASB) のままであり、クラスター関連のファイル (ログなど) は引き続きその既定のストレージに書き込まれますが、同時に処理対象のデータを Data Lake Storage Gen1 に格納することができます。 Data Lake Storage Gen1 を追加のストレージ アカウントとして使用しても、クラスターからストレージに対する読み取り/書き込みのパフォーマンスや機能は何も変化しません。

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight クラスター記憶域で Data Lake Storage Gen1 を使用する

HDInsight で Data Lake Storage Gen1 を使用するための重要な考慮事項を次に示します。

* 追加のストレージとしての Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.2、3.4、3.5、および 3.6 で使用できます。

PowerShell を使用して、Data Lake Storage Gen1 を使用するように HDInsight を構成するには、次の手順が必要です。

* Data Lake Storage Gen1 アカウントを作成する 
* Data Lake Storage Gen1 へのロールベースのアクセスの認証を設定する
* Data Lake Storage Gen1 への認証を使用して HDInsight クラスターを作成する
* クラスター上でテスト ジョブを実行する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。
* **Windows SDK**。 [ここ](https://dev.windows.com/en-us/downloads)からインストールできます。 この機能は、セキュリティ証明書の作成に使用します。
* **Azure Active Directory Service のプリンシパル**。 このチュートリアルの手順では、Azure AD でサービス プリンシパルを作成する方法を説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 Azure AD 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    **Azure AD 管理者でない場合** は、サービス プリンシパルの作成に必要な手順を実行することはできません。 その場合は、Data Lake Storage Gen1 で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[Create a service principal with certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)」 (証明書でサービス プリンシパルを作成する) で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントを作成する 
Data Lake Storage Gen1 アカウントを作成するには、次の手順に従います。

1. デスクトップで、新しい Azure PowerShell ウィンドウを開き、次のスニペットを入力します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Data Lake Storage Gen1 リソース プロバイダーの登録時に `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` のようなエラーが発生した場合は、サブスクリプションが Data Lake Storage Gen1 で承認されていない可能性があります。 こちらの[手順](data-lake-store-get-started-portal.md)に従って Data Lake Storage Gen1 で Azure サブスクリプションを有効にしていることを確認してください。
   >
   >
2. Data Lake Storage Gen1 を使用するストレージ アカウントは、Azure リソース グループに関連付けられます。 まず、Azure リソース グループを作成します。

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    出力は次のように表示されます。

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Data Lake Storage Gen1 を使用するストレージ アカウントを作成します。 指定するアカウント名には、小文字と数字のみを含める必要があります。

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    出力は次のように表示されます。

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Data Lake Storage Gen1 にいくつかのサンプル データをアップロードします。 このサンプル データは、HDInsight クラスターからデータにアクセスできることを確認するために、この記事の後半で使用します。 アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 へのロールベースのアクセスの認証を設定する

すべての Azure サブスクリプションは Azure Active Directory と関連付けられます。 Azure Portal か Azure Resource Manager API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初にその Azure Active Directory での認証を実行する必要があります。 Azure のサブスクリプションやサービスにアクセス権を付与するには、Azure リソースに対する該当するロールを割り当てます。  サービスの場合は、サービス プリンシパルにより、Azure Active Directory (Azure AD) 内のサービスが識別されます。 このセクションでは、Azure PowerShell を使用してアプリケーションのサービス プリンシパルを作成し、作成したサービス プリンシパルにロールを割り当てることで、HDInsight などのアプリケーション サービスに Azure のリソース (先ほど作成した Data Lake Storage Gen1 を使用するストレージ アカウント) へのアクセス権を付与する方法を説明します。

Data Lake Storage Gen1 の Active Directory 認証を設定するには、次のタスクを行う必要があります。

* 自己署名証明書の作成
* Azure Active Directory でのアプリケーションの作成、およびサービス プリンシパルの作成

### <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

このセクションの手順を進める前に、[Windows SDK](https://dev.windows.com/en-us/downloads) がインストールされていることを確認してください。 証明書の作成先となるディレクトリ (**C:\mycertdir** など) も作成しておく必要があります。

1. PowerShell ウィンドウで、Windows SDK をインストールした場所 (通常は `C:\Program Files (x86)\Windows Kits\10\bin\x86`) に移動し、[MakeCert][makecert] ユーティリティを使用して、自己署名証明書と秘密キーを作成します。 次のコマンドを使用します。

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    秘密キーのパスワードを入力するよう求められます。 コマンドが正常に実行されると、指定した証明書ディレクトリに **CertFile.cer** と **mykey.pvk** が表示されます。
2. [Pvk2Pfx][pvk2pfx] ユーティリティを使用して、MakeCert によって作成された .pvk ファイルと .cer ファイルを .pfx ファイルに変換します。 次のコマンドを実行します。

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    メッセージが表示されたら、先ほど指定した秘密キーのパスワードを入力します。 **-po** パラメーターに指定する値は、.pfx ファイルに関連付けられているパスワードです。 コマンドが正常に完了すると、指定した証明書ディレクトリに CertFile.pfx も表示されます。

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory とサービス プリンシパルの作成

このセクションでは、Azure Active Directory アプリケーションのサービス プリンシパルを作成し、そのサービス プリンシパルにロールを割り当てて、証明書を指定することでサービス プリンシパルとして認証する手順を実行します。 Azure Active Directory でアプリケーションを作成するには、次のコマンドを実行します。

1. PowerShell コンソール ウィンドウで、次のコマンドレットを貼り付けます。 **-DisplayName** プロパティに指定する値は一意になるようにしてください。 また、 **-HomePage** と **-IdentiferUris** の値はプレースホルダー値であるため、確認されません。

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. アプリケーション ID を使用してサービス プリンシパルを作成します。

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Data Lake Storage Gen1 フォルダーおよび HDInsight クラスターからアクセスするファイルへのアクセス権をサービス プリンシパルに付与します。 次のスニペットは、Data Lake Storage Gen1 を使用するストレージ アカウントのルート (サンプル データ ファイルをコピーした場所) とファイル自体へのアクセスを提供します。

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 を追加のストレージとして使用する HDInsight Linux クラスターを作成する

ここでは、Data Lake Storage Gen1 を追加のストレージとして使用する HDInsight Hadoop Linux クラスターを作成します。 このリリースでは、HDInsight クラスターと Data Lake Storage Gen1 を使用するストレージ アカウントは同じ場所にある必要があります。

1. 最初に、サブスクリプションのテナント ID を取得します。 この情報は後で必要になります。

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. このリリースでは、Hadoop クラスターの場合、Data Lake Storage Gen1 はクラスターの追加のストレージとしてのみ使用できます。 既定のストレージは、Azure Blob Storage (WASB) のままです。 そのため、クラスターに必要なストレージ アカウントとストレージ コンテナーを最初に作成します。

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. HDInsight クラスターを作成します。 次のコマンドレットを使用します。

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    コマンドレットが正常に完了すると、クラスターの詳細を一覧表示する出力が表示されます。


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Data Lake Storage Gen1 を使用する HDInsight クラスターでテスト ジョブを実行する
HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、HDInsight クラスターが Data Lake Storage Gen1 にアクセス可能であるかどうかをテストできます。 これを行うには、先ほど Data Lake Storage Gen1 を使用するストレージ アカウントにアップロードしたサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

このセクションでは、作成した HDInsight Linux クラスターに SSH でアクセスし、サンプルの Hive クエリを実行します。

* クラスターへの SSH アクセスに Windows クライアントを使用する場合は、「[Windows での PuTTY から HDInsight (Hadoop) で SSH を使用します](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
* クラスターへの SSH アクセスに Linux クライアントを使用する場合は、「[Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

1. 接続したら、次のコマンドを使用して Hive CLI を起動します。

    ```azurepowershell
    hive
    ```

2. CLI を使用して次のステートメントを入力し、サンプル データを使用して Data Lake Storage Gen1 内に **vehicles** という名前の新しいテーブルを作成します。

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    次のような出力が表示されます。

    ```output
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>HDFS コマンドを使用して Data Lake Storage Gen1 にアクセスする
Data Lake Storage Gen1 を使用するように HDInsight クラスターを構成したら、HDFS シェル コマンドを使用してストアにアクセスできます。

このセクションでは、作成した HDInsight Linux クラスターに SSH でアクセスし、HDFS コマンドを実行します。

* クラスターへの SSH アクセスに Windows クライアントを使用する場合は、「[Windows での PuTTY から HDInsight (Hadoop) で SSH を使用します](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
* クラスターへの SSH アクセスに Linux クライアントを使用する場合は、「[Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

接続されたら、次の HDFS ファイルシステム コマンドを使用して、Data Lake Storage Gen1 を使用するストレージ アカウント内のファイルを一覧表示します。

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

これにより、以前に Data Lake Storage Gen1 にアップロードしたファイルが一覧表示されます。

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

`hdfs dfs -put` コマンドを使用して Data Lake Storage Gen1 にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。

## <a name="see-also"></a>参照
* [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [ポータル:Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx