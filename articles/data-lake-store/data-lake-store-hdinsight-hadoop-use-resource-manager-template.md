---
title: "Azure テンプレートを使用して Azure HDInsight と Data Lake Store を作成する | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使って Azure Data Lake Store で HDInsight クラスターを作成し使用する"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 40a1d76cc4167858a9bebac9845230473cc71e3e
ms.lasthandoff: 03/07/2017


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Data Lake Store で HDInsight クラスターを作成する
> [!div class="op_single_selector"]
> * [ポータルの使用](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell の使用 (既定のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell の使用 (追加のストレージ)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager の使用](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell を使用して、Azure Data Lake Store を**追加のストレージとして**使用する HDInsight クラスターを構成する方法について説明します。 

サポートされている種類のクラスターでは、Data Lake Store を既定のストレージまたは追加のストレージ アカウントとして使用します。 Data Lake Store を追加のストレージとして使用した場合、クラスターの既定のストレージ アカウントは Azure Storage BLOB (WASB) のままであり、クラスター関連のファイル (ログなど) はその既定のストレージに書き込まれますが、一方で処理対象のデータは Data Lake Store アカウントに格納することができます。 Data Lake Store を追加のストレージ アカウントとして使用しても、クラスターからストレージに対する読み取り/書き込みのパフォーマンスや機能は何も変化しません。

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>HDInsight クラスター ストレージで Data Lake Store を使用する

HDInsight で Data Lake Store を使用するための重要な考慮事項を次に示します。

* Data Lake Store にアクセスできる HDInsight クラスターを既定のストレージとして作成するオプションは、HDInsight バージョン 3.5 で使用できます。

* Data Lake Store にアクセスできる HDInsight クラスターを追加のストレージとして作成するオプションは、HDInsight バージョン 3.2、3.4、3.5 で使用できます。

* HBase クラスター (Windows および Linux) の場合、Data Lake Store は、既定のストレージとしても追加のストレージとしても**使用できません**。


この記事では、追加のストレージとして Data Lake Store を使用して Hadoop クラスターをプロビジョニングします。 既定のストレージとして Data Lake Store を使用する Hadoop クラスターの作成方法については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。
* **Azure Active Directory Service のプリンシパル**。 このチュートリアルの手順では、Azure AD でサービス プリンシパルを作成する方法を説明します。 ただし、サービス プリンシパルを作成するには、Azure AD 管理者である必要があります。 Azure AD 管理者である場合は、この前提条件をスキップしてチュートリアルを進めることができます。

    **Azure AD 管理者でない場合**は、サービス プリンシパルの作成に必要な手順を実行することはできません。 その場合は、Data Lake Store で HDInsight クラスターを作成する前に、まず Azure AD 管理者がサービス プリンシパルを作成する必要があります。 また、「[Create a service principal with certificate](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)」 (証明書でサービス プリンシパルを作成する) で説明しているように、サービス プリンシパルは証明書を使って作成する必要があります。

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Azure Data Lake Store で HDInsight クラスターを作成する
Resource Manager テンプレートおよびテンプレート使用の前提条件は、GitHub の[新しい Data Lake Store での HDInsight Linux クラスターのデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)に関するページにあります。 このリンクで示されている手順に従って、HDInsight クラスターを Azure Data Lake Store で追加ストレージとして作成します。

上記リンクの手順には、PowerShell が必要です。 手順を進める前に、Azure アカウントにログインしていることを確認してください。 デスクトップで Azure PowerShell ウィンドウを新しく開き、次のスニペットを入力します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>サンプル データを Azure Data Lake Store にアップロードする
Resource Manager テンプレートでは、新しい Data Lake Store アカウントが作成され、HDInsight クラスターに関連付けられます。 この時点で、いくつかのサンプル データを Azure Data Lake Store にアップロードする必要があります。 このデータは、チュートリアルの後半で Data Lake Store 内のデータにアクセスする HDInsight クラスターからジョブを実行するために必要です。 データをアップロードする方法の詳細については、[Data Lake Store へのファイルのアップロード](data-lake-store-get-started-portal.md#uploaddata)に関するセクションを参照してください。 アップロードするサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。

## <a name="set-relevant-acls-on-the-sample-data"></a>サンプル データに関連 ACL を設定する
アップロードしたサンプル データに HDInsight クラスターからアクセスできるようにするには、HDInsight クラスターと Data Lake Store との間に ID を確立するのに使用した Azure AD アプリケーションに、アクセスしようとしているファイルやフォルダーへのアクセスを持たせる必要があります。 このためには、次の手順を実行します。

1. HDInsight クラスターと Data Lake Store に関連付けられている Azure AD アプリケーションの名前を確認します。 名前を確認する方法の&1; つは、Resource Manager テンプレートを使って作成した HDInsight クラスター ブレードを開き、**[クラスター AAD ID]** タブをクリックし、**[Service Principal Display Name]** (サービス プリンシパル表示名) の値を探すことです。
2. 次に、HDInsight クラスターからアクセスするファイルまたはフォルダーにおいて、この Azure AD アプリケーションにアクセス権を付与します。 Data Lake Store のファイルやフォルダーに正しい ACL を設定する方法については、[Data Lake Store に格納されているデータのセキュリティ保護](data-lake-store-secure-data.md#filepermissions)に関するページを参照してください。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Data Lake Store を使用する HDInsight クラスターでテスト ジョブを実行する
HDInsight クラスターを構成した後は、クラスターでテスト ジョブを実行し、HDInsight クラスターが Data Lake Store にアクセス可能であるかどうかをテストできます。 これを行うには、前に Data Lake Store にアップロードしたサンプル データを使用してテーブルを作成するサンプル Hive ジョブを実行します。

このセクションでは、HDInsight Linux クラスターに SSH でアクセスし、サンプルの Hive クエリを実行します。 Windows クライアントを使用している場合は、[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

1. 接続したら、次のコマンドを使用して Hive CLI を起動します。

   ```
   hive
   ```
2. CLI を使用して次のステートメントを入力し、サンプル データを使用して Data Lake Store 内に **vehicles** という名前の新しいテーブルを作成します。

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>HDFS コマンドを使用して Data Lake Store にアクセスする
Data Lake Store を使用するように HDInsight クラスターを構成したら、HDFS シェル コマンドを使用してストアにアクセスできます。

このセクションでは、HDInsight Linux クラスターに SSH でアクセスし、HDFS コマンドを実行します。 Windows クライアントを使用している場合は、[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) からダウンロードできる **PuTTY** を使用することをお勧めします。

PuTTY の使用については、「 [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)」をご覧ください。

接続されたら、次の HDFS ファイル システム コマンドを使用して、Data Lake Store 内のファイルを一覧表示します。

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

これにより、以前に Data Lake Store にアップロードしたファイルが一覧表示されます。

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

`hdfs dfs -put` コマンドを使用して Data Lake Store にいくつかのファイルをアップロードし、`hdfs dfs -ls` を使用してファイルが正常にアップロードされたかどうかを確認することもできます。


## <a name="next-steps"></a>次のステップ
* [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-wasb-distcp.md)

