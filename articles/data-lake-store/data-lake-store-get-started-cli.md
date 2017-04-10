---
title: "コマンド ライン インターフェイスで Azure Data Lake Store の使用を開始する | Microsoft Docs"
description: "Azure クロスプラットフォーム コマンド ラインを使用して、Data Lake Store アカウントを作成し、基本的な操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e43a6ea9510c481518becb52cc571ec62e3b151d
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Azure コマンド ラインを使用して Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

> [!NOTE]
> 大量のデータ (サイズの大きいファイル、多数のファイル、またはその両方) をアップロードおよびダウンロードする場合は、[Python SDK](data-lake-store-get-started-python.md)、[.NET SDK](data-lake-store-get-started-net-sdk.md)、または [Azure PowerShell](data-lake-store-get-started-powershell.md) を使用することをお勧めします。 これらのオプションではスレッドを複数使用してデータ移動が並列化されるため、パフォーマンスが改善されます。
> 
>  

Azure コマンド ライン インターフェイスを使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、[Data Lake Store の概要](data-lake-store-overview.md)に関する記事をご覧ください。

Azure CLI は Node.js で実装されます。 Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。 Azure CLI はオープン ソースです。 ソース コードは GitHub (<a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>) で管理されています。 この記事では、Data Lake Store での Azure CLI の使用についてのみ説明します。 Azure CLI の使い方に関する一般的なガイドは、[Azure CLI の使い方][azure-command-line-tools]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure CLI** - インストールと構成に関する情報は、 [Azure CLI のインストールと構成](../cli-install-nodejs.md) に関するページを参照してください。 CLI をインストールした後で、コンピューターを再起動してください。

## <a name="authentication"></a>認証
この記事では、Data Lake Store に対してエンド ユーザーとしてログインする比較的単純な認証方法を使用します。 その後、Data Lake Store アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Store には他の認証方法も存在します (**エンド ユーザー認証**と**サービス間認証**)。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="login-to-your-azure-subscription"></a>Azure サブスクリプションへのログイン
1. [Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)に関する記事に記載されている手順に従い、`azure login` メソッドを使用してサブスクリプションに接続します。
2. ご使用のアカウントに関連付けられているサブスクリプションを `azure account list` コマンドで一覧表示します。
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    上記の出力結果を見ると、**Azure-sub-1** が現在有効になっており、それ以外に **Azure-sub-2** というサブスクリプションが存在しています。 
3. 使用するサブスクリプションを選択します。 Azure-sub-2 サブスクリプションを使用する場合は、`azure account set` コマンドを使用します。
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する
コマンド プロンプト、シェル、またはターミナル セッションを開き、次のコマンドを実行します。

1. 次のコマンドで Azure リソース マネージャー モードに切り替えます。
   
        azure config mode arm
2. 新しいリソース グループを作成します。 次のコマンドで、使用するパラメーター値を指定します。
   
        azure group create <resourceGroup> <location>
   
    場所の名前にスペースが含まれる場合は、名前を引用符で囲みます。 たとえば、"East US 2" などとします。
3. Data Lake Store アカウントを作成します。
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Data Lake Store にフォルダーを作成する
Azure Data Lake Store アカウントにフォルダーを作成し、データの管理と保存を行うことができます。 次のコマンドを使用すると、Data Lake Store のルートに "mynewfolder" という名前のフォルダーが作成されます。

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

次に例を示します。

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Data Lake Store にデータをアップロードする
データは、ルート レベルで直接 Data Lake Store に、またはアカウント内で作成したフォルダーにアップロードできます。 以下のスニペットは、前のセクションで作成したフォルダー (**mynewfolder**) にいくつかのサンプル データをアップロードする方法を示します。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\. など) に保存します。

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

次に例を示します。

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Data Lake Store 内のファイルを一覧表示する
Data Lake Store アカウントのファイルを一覧表示するには、以下のコマンドを使用します。

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

次に例を示します。

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

この出力は次のように表示されます。

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Data Lake Store からデータの名前変更、ダウンロード、および削除を行う
* **ファイルの名前を変更する**には、次のコマンドを使用します。
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    次に例を示します。
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **ファイルをダウンロードする**には、次のコマンドを使用します。 既に存在するパスをダウンロード先として指定してください。
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    次に例を示します。
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **ファイルを削除する**には、次のコマンドを使用します。
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    次に例を示します。
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、項目を削除します。

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Data Lake Store のフォルダーのアクセス制御リストを表示する
Data Lake Store フォルダーの ACL を表示するには、次のコマンドを使用します。 現在のリリースでは、ACL を設定できるのは Data Lake Store のルートに対してだけです。 したがって、次の path パラメーターは常にルート (/) です。

    azure datalake store permissions show <dataLakeStoreName> <path>

次に例を示します。

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Data Lake Store アカウントを削除する
Data Lake Store アカウントを削除するには、次のコマンドを使用します。

    azure datalake store account delete <dataLakeStoreAccountName>

次に例を示します。

    azure datalake store account delete mynewdatalakestore

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../cli-install-nodejs.md

