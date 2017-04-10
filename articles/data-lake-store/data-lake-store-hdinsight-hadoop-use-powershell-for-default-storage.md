---
title: "PowerShell を使用して、Data Lake Store を既定のストレージとして使用する HDInsight クラスターを作成する | Microsoft Docs"
description: "Azure PowerShell を使用して、Azure Data Lake Store を使用する HDInsight クラスターを作成および使用します"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: e1101c92118e56768c668d19f6556517d5a79c51
ms.lasthandoff: 03/07/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>PowerShell を使用して、Data Lake Store を既定のストレージとして使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure Portal の使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Azure PowerShell を使用して、Azure Data Lake Store を既定のストレージとして使用する Azure HDInsight クラスターを構成する方法について説明します。 Data Lake Store を追加のストレージとして使用する HDInsight クラスターの作成方法については、[Data Lake Store を追加のストレージとして使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-powershell.md)に関する記事をご覧ください。

HDInsight で Data Lake Store を使用するための重要な考慮事項を次に示します。

* 既定のストレージとしての Data Lake Store にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.5 で使用できます。

* 既定のストレージとしての Data Lake Store にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight Premium クラスターでは*使用できません*。

* HBase クラスター (Windows および Linux) の場合、Data Lake Store は、既定のストレージとしても追加のストレージとしても*サポートされません*。

PowerShell を使用して Data Lake Store を操作できるように HDInsight を構成するには、次の&5; つのセクションで示す手順に従います。

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の要件を満たしていることを確認します。

* **An Azure サブスクリプション**: [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* **Azure PowerShell 1.0 以上**: [PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)に関する記事をご覧ください。
* **Windows ソフトウェア開発キット (SDK)**: Windows SDK のインストール方法については、「[Windows 10 用のダウンロードとツール](https://dev.windows.com/en-us/downloads)」をご覧ください。 Windows SDK は、セキュリティ証明書の作成に使用します。
* **Azure Active Directory サービス プリンシパル**: このチュートリアルでは、Azure Active Directory (Azure AD) でサービス プリンシパルを作成する方法について説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    >[!NOTE]
    >Azure AD 管理者である場合にのみ、サービス プリンシパルを作成することができます。 Data Lake Store で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 「[証明書を使用したサービス プリンシパルの作成](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。
    >

## <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
Data Lake Store アカウントを作成するには、次の操作を行います。

1. デスクトップで、PowerShell ウィンドウを開き、次のスニペットを入力します。

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Data Lake Store リソース プロバイダーの登録時に `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` のようなエラーが発生した場合は、サブスクリプションが Data Lake Store のホワイトリストに登録されていない可能性があります。 Data Lake Store パブリック プレビューで Azure サブスクリプションを有効にするには、「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」をご覧ください。
    > 

2. サインインを求められたら、サブスクリプションの管理者または所有者としてサインインします。
3. Data Lake Store アカウントは、Azure リソース グループに関連付けられます。 まず、リソース グループを作成します。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure リソース グループの作成](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Azure リソース グループの作成")
3. Data Lake Store アカウントを作成します。 指定するアカウント名には、小文字と数字のみを含める必要があります。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake アカウントの作成](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake アカウントの作成")
4. アカウントが正常に作成されたことを確認します。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    この出力は **True** になります。

5. Data Lake Store を既定のストレージとして使用するには、クラスターの作成時にクラスター固有のファイルのコピー先となるルート パスを指定する必要があります。 ルート パス (このスニペットでは **/clusters/hdiadlcluster**) を作成するには、次のコマンドレットを使用します。

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Data Lake Store へのロールベースのアクセスの認証を設定する
すべての Azure サブスクリプションは Azure Active AD エンティティと関連付けられます。 Azure Portal か Azure Resource Manager API を使ってサブスクリプションのリソースにアクセスするユーザーやサービスは、最初に Azure AD での認証を実行する必要があります。 Azure のサブスクリプションやサービスにアクセス権を付与するには、Azure リソースに対する該当するロールを割り当てます。 サービスの場合は、サービス プリンシパルにより、Azure AD 内のサービスが識別されます。

このセクションでは、HDInsight のようなアプリケーション サービスに Azure のリソース (先ほど作成した Data Lake Store アカウント) へのアクセス権を付与する方法を説明します。 これを行うには、PowerShell を使用してアプリケーションのサービス プリンシパルを作成し、作成したサービス プリンシパルにロールを割り当てます。

Azure Data Lake の Active Directory 認証を設定するには、次の&2; つのセクションのタスクを実行します。

### <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成
このセクションの手順を進める前に、[Windows SDK](https://dev.windows.com/en-us/downloads) がインストールされていることを確認してください。 証明書の作成先となるディレクトリ (*C:\mycertdir* など) も作成しておく必要があります。

1. PowerShell ウィンドウで、Windows SDK をインストールした場所 (通常は *C:\Program Files (x86)\Windows Kits\10\bin\x86*) に移動し、[MakeCert][makecert] ユーティリティを使用して、自己署名証明書と秘密キーを作成します。 次のコマンドを使用します。

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    秘密キーのパスワードを入力するよう求められます。 コマンドが正常に実行されると、指定した証明書ディレクトリに **CertFile.cer** と **mykey.pvk** が表示されます。
2. [Pvk2Pfx][pvk2pfx] ユーティリティを使用して、MakeCert によって作成された .pvk ファイルと .cer ファイルを .pfx ファイルに変換します。 次のコマンドを実行します。

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    メッセージが表示されたら、先ほど指定した秘密キーのパスワードを入力します。 **-po** パラメーターに指定する値は、.pfx ファイルに関連付けられているパスワードです。 コマンドが正常に完了すると、指定した証明書ディレクトリに CertFile.pfx も表示されます。

### <a name="create-an-azure-ad-and-a-service-principal"></a>Azure AD とサービス プリンシパルの作成
このセクションでは、Azure AD アプリケーションのサービス プリンシパルを作成し、そのサービス プリンシパルにロールを割り当てて、証明書を指定することでサービス プリンシパルとして認証します。 Azure AD でアプリケーションを作成するには、次のコマンドを実行します。

1. PowerShell コンソール ウィンドウで、次のコマンドレットを貼り付けます。 **-DisplayName** プロパティに指定する値は一意になるようにしてください。 **-HomePage** と **-IdentiferUris** の値はプレースホルダー値であるため、確認されません。

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. アプリケーション ID を使用してサービス プリンシパルを作成します。

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Data Lake Store のルートと、先ほど指定したルート パス内のすべてのフォルダーに、サービス プリンシパルのアクセス権を付与します。 次のコマンドレットを使用します。

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Data Lake Store を既定のストレージとして使用する HDInsight Linux クラスターを作成する

このセクションでは、Data Lake Store を既定のストレージとして使用する HDInsight Hadoop Linux クラスターを作成します。 このリリースでは、HDInsight クラスターと Data Lake Store は同じ場所にある必要があります。

1. サブスクリプションのテナント ID を取得し、後で使用するために保存します。

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. 次のコマンドレットを使用して HDInsight クラスターを作成します。

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                          # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.5" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    コマンドレットが正常に完了すると、クラスターの詳細を一覧表示する出力が表示されます。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Data Lake Store を使用する HDInsight クラスターでテスト ジョブを実行する
HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、クラスターが Data Lake Store にアクセス可能であるかどうかをテストできます。 これを行うには、Data Lake Store (*<cluster root>/example/data/sample.log*) に既にあるサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

このセクションでは、作成した HDInsight Linux クラスターに Secure Shell (SSH) 接続し、サンプルの Hive クエリを実行します。

* クラスターへの SSH 接続に Windows クライアントを使用する場合は、[Windows からの、HDInsight 上の Linux ベースの Hadoop に対する SSH の使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)に関する記事をご覧ください。
* クラスターへの SSH 接続に Linux クライアントを使用する場合は、[Linux からの、HDInsight 上の Linux ベースの Hadoop に対する SSH の使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)に関する記事をご覧ください。

1. 接続したら、次のコマンドを使用して Hive コマンド ライン インターフェイス (CLI) を開始します。

        hive
2. CLI を使用して次のステートメントを入力し、サンプル データを使用して Data Lake Store 内に **vehicles** という名前の新しいテーブルを作成します。

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    クエリの結果は SSH コンソールに出力されます。

    >[!NOTE]
    >上の CREATE TABLE コマンドのサンプル データへのパスは `adl:///example/data/` です。`adl:///` にはクラスターのルートを指定します。 このチュートリアルで指定されたクラスターのルートの例に従うと、コマンドは `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` になります。 短縮バージョンを使用するか、またはクラスター ルートへの完全なパスを指定できます。
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>HDFS コマンドを使用して Data Lake Store にアクセスする
Data Lake Store を使用するように HDInsight クラスターを構成したら、Hadoop 分散ファイルシステム (HDFS) シェル コマンドを使用して Data Lake Store にアクセスできます。

このセクションでは、作成した HDInsight Linux クラスターに SSH 接続し、HDFS コマンドを実行します。

* クラスターへの SSH 接続に Windows クライアントを使用する場合は、[Windows からの、HDInsight 上の Linux ベースの Hadoop に対する SSH の使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)に関する記事をご覧ください。
* クラスターへの SSH 接続に Linux クライアントを使用する場合は、[Linux からの、HDInsight 上の Linux ベースの Hadoop に対する SSH の使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)に関する記事をご覧ください。

接続したら、次の HDFS ファイル システム コマンドを使用して、Data Lake Store 内のファイルを一覧表示します。

    hdfs dfs -ls adl:///

`hdfs dfs -put` コマンドを使用して Data Lake Store にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。

## <a name="see-also"></a>関連項目
* [Azure Portal: Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

