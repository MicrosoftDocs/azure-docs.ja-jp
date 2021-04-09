---
title: テンプレート - Data Lake Storage Gen1 を使用する HDInsight クラスター
description: Azure Resource Manager テンプレートを使用して、Azure Data Lake Storage Gen1 を使用する Azure HDInsight クラスターを作成および使用します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a7283ad4c4c61ecc293a55ffc4cb9626bb28d630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108730"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell を使用して、Azure Data Lake Storage Gen1 を **追加のストレージとして** 使用する HDInsight クラスターを構成する方法について説明します。

サポートされている種類のクラスターでは、Data Lake Storage Gen1 を既定のストレージまたは追加のストレージ アカウントとして使用できます。 Data Lake Storage Gen1 を追加のストレージとして使用した場合、クラスター用の既定のストレージ アカウントは Azure Blob Storage (WASB) のままであり、クラスター関連のファイル (ログなど) は引き続きその既定のストレージに書き込まれますが、一方で処理対象のデータは Data Lake Storage Gen1 アカウントに格納することができます。 Data Lake Storage Gen1 を追加のストレージ アカウントとして使用しても、クラスターからストレージに対する読み取り/書き込みのパフォーマンスや機能は何も変化しません。

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight クラスター記憶域で Data Lake Storage Gen1 を使用する

HDInsight で Data Lake Storage Gen1 を使用するための重要な考慮事項を次に示します。

* 既定のストレージとしての Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.5 および 3.6 で使用できます。

* 追加のストレージとしての Data Lake Storage Gen1 にアクセスできる HDInsight クラスターを作成するオプションは、HDInsight バージョン 3.2、3.4、3.5、および 3.6 で使用できます。

この記事では、追加のストレージとして Data Lake Storage Gen1 を使用して Hadoop クラスターをプロビジョニングします。 既定のストレージとして Data Lake Storage Gen1 を使用する Hadoop クラスターの作成方法については、[Azure portal を使用して Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する方法](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。
* **Azure Active Directory Service のプリンシパル**。 このチュートリアルの手順では、Azure AD でサービス プリンシパルを作成する方法を説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 Azure AD 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    **Azure AD 管理者でない場合** は、サービス プリンシパルの作成に必要な手順を実行することはできません。 その場合は、Data Lake Storage Gen1 で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[Create a service principal with certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)」 (証明書でサービス プリンシパルを作成する) で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 を使用する HDInsight クラスターの作成
Resource Manager テンプレートおよびテンプレート使用の前提条件は、GitHub の[新しい Data Lake Storage Gen1 での HDInsight Linux クラスターのデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)に関するページにあります。 このリンクで示されている手順に従って、Data Lake Storage Gen1 を追加ストレージとして使用する HDInsight クラスターを作成します。

上記リンクの手順には、PowerShell が必要です。 手順を進める前に、Azure アカウントにログインしていることを確認してください。 デスクトップで Azure PowerShell ウィンドウを新しく開き、次のスニペットを入力します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

テンプレートでは、次のリソースの種類をデプロイします。

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 へのサンプル データのアップロード
Resource Manager テンプレートによって、Data Lake Storage Gen1 を使用する新しいストレージ アカウントが作成され、HDInsight クラスターに関連付けられます。 この時点で、いくつかのサンプル データを Data Lake Storage Gen1 にアップロードする必要があります。 このデータは、チュートリアルの後半で Data Lake Storage Gen1 を使用するストレージ アカウント内のデータにアクセスするジョブを HDInsight クラスターから実行するために必要です。 データをアップロードする方法の手順については、[Data Lake Storage Gen1 へのファイルのアップロード](data-lake-store-get-started-portal.md#uploaddata)に関する記事を参照してください。 アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

## <a name="set-relevant-acls-on-the-sample-data"></a>サンプル データに関連 ACL を設定する
アップロードしたサンプル データに HDInsight クラスターからアクセスできるようにするには、HDInsight クラスターと Data Lake Storage Gen1 との間に ID を確立するのに使用した Azure AD アプリケーションに、アクセスしようとしているファイルやフォルダーへのアクセスを持たせる必要があります。 このためには、次の手順を実行します。

1. HDInsight クラスターと Data Lake Storage Gen1 を使用するストレージ アカウントに関連付けられている Azure AD アプリケーションの名前を見つけます。 名前を探す方法の 1 つは、Resource Manager テンプレートを使って作成した HDInsight クラスター ブレードを開き、 **[Cluster Azure AD Identity]\(クラスター Azure AD ID\)** タブをクリックし、 **[サービス プリンシパルの表示名]** の値を探すことです。
2. 次に、HDInsight クラスターからアクセスするファイルまたはフォルダーにおいて、この Azure AD アプリケーションにアクセス権を付与します。 Data Lake Storage Gen1 のファイルやフォルダーに正しい ACL を設定する方法については、[Data Lake Storage Gen1 に格納されているデータのセキュリティ保護](data-lake-store-secure-data.md#filepermissions)に関するページを参照してください。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Data Lake Storage Gen1 を使用する HDInsight クラスターでテスト ジョブを実行する
HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、HDInsight クラスターが Data Lake Storage Gen1 にアクセス可能であるかどうかをテストできます。 これを行うには、先ほど Data Lake Storage Gen1 を使用するストレージ アカウントにアップロードしたサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

このセクションでは、HDInsight Linux クラスターに SSH でアクセスし、サンプルの Hive クエリを実行します。 Windows クライアントを使用している場合は、[https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

1. 接続したら、次のコマンドを使用して Hive CLI を起動します。

   ```
   hive
   ```
2. CLI を使用して次のステートメントを入力し、サンプル データを使用して Data Lake Storage Gen1 内に **vehicles** という名前の新しいテーブルを作成します。

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   次のような出力が表示されます。

   ```
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

このセクションでは、HDInsight Linux クラスターに SSH でアクセスし、HDFS コマンドを実行します。 Windows クライアントを使用している場合は、[https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

接続されたら、次の HDFS ファイルシステム コマンドを使用して、Data Lake Storage Gen1 を使用するストレージ アカウント内のファイルを一覧表示します。

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

これにより、以前に Data Lake Storage Gen1 にアップロードしたファイルが一覧表示されます。

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

`hdfs dfs -put` コマンドを使用して Data Lake Storage Gen1 にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。


## <a name="next-steps"></a>次のステップ
* [Azure Storage Blob から Data Lake Storage Gen1 へのデータのコピー](data-lake-store-copy-data-wasb-distcp.md)
* [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)