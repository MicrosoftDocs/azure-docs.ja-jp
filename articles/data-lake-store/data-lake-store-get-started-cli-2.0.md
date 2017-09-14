---
title: "Azure コマンド ライン 2.0 インターフェイスで Azure Data Lake Store の使用を開始する | Microsoft Docs"
description: "Azure クロスプラットフォーム コマンド ライン 2.0 を使用して、Data Lake Store アカウントを作成し、基本的な操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: c5971a137d9081be8c5978f481ec42a1f91e5a56
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Azure CLI 2.0 を使用して Azure Data Lake Store の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure CLI 2.0 を使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、[Data Lake Store の概要](data-lake-store-overview.md)に関する記事をご覧ください。

Azure CLI 2.0 は、Azure リソースを管理するための、Azure の新しいコマンド ライン エクスペリエンスです。 macOS、Linux、および Windows で使用できます。 詳細については、[Azure CLI 2.0 の概要](https://docs.microsoft.com/cli/azure/overview)に関するページを参照してください。 コマンドと構文の完全な一覧については、[Azure Data Lake Store CLI 2.0 リファレンス](https://docs.microsoft.com/cli/azure/dls)を参照することもできます。


## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure CLI 2.0** - 手順については、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="authentication"></a>認証

この記事では、Data Lake Store に対してエンド ユーザーとしてログインする比較的単純な認証方法を使用します。 その後、Data Lake Store アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Store には他の認証方法も存在します (**エンド ユーザー認証**と**サービス間認証**)。 認証方法の詳細については、[エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)または[サービス間認証](data-lake-store-authenticate-using-active-directory.md)に関するページを参照してください。


## <a name="log-in-to-your-azure-subscription"></a>Azure サブスクリプションにログイン

1. Azure サブスクリプションにログインします。

    ```azurecli
    az login
    ```

    使用するコードは次の手順で示します。 Web ブラウザーを使用してページ https://aka.ms/devicelogin を開き、認証のためのコードを入力します。 資格情報を使用してログインするように求めるメッセージが表示されます。

2. ログインすると、アカウントに関連付けられているすべての Azure サブスクリプションの一覧がウィンドウに表示されます。 次のコマンドを使用して、特定のサブスクリプションを使用します。
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する

1. 新しいリソース グループを作成します。 次のコマンドで、使用するパラメーター値を指定します。 場所の名前にスペースが含まれる場合は、名前を引用符で囲みます。 たとえば、"East US 2" などとします。 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Data Lake Store アカウントを作成します。
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Data Lake Store アカウントでフォルダーを作成する

Azure Data Lake Store アカウントにフォルダーを作成し、データの管理と保存を行うことができます。 次のコマンドを使用して、Data Lake Store のルートに **mynewfolder** という名前のフォルダーを作成します。

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` パラメーターを指定すると、フォルダーが作成されます。 このパラメーターを指定しない場合、コマンドによって Data Lake Store アカウントのルートに mynewfolder という名前の空のファイルが作成されます。
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Data Lake Store アカウントにデータをアップロードする

データは、Data Lake Store のルート レベルに直接アップロードするか、アカウント内で作成したフォルダーにアップロードすることができます。 以下のスニペットは、前のセクションで作成したフォルダー (**mynewfolder**) にいくつかのサンプル データをアップロードする方法を示します。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\ など) に保存します。

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> 保存先として、ファイル名を含む完全なパスを指定する必要があります。
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Data Lake Store アカウントのファイルを一覧表示する

Data Lake Store アカウントのファイルを一覧表示するには、以下のコマンドを使用します。

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

この出力は次のように表示されます。

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Data Lake Store アカウントのデータの名前変更、ダウンロード、および削除を行う 

* **ファイルの名前を変更する**には、次のコマンドを使用します。
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **ファイルをダウンロードする**には、次のコマンドを使用します。 既に存在するパスをダウンロード先として指定してください。
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > 対象フォルダーが存在しない場合は、作成されます。
    > 
    >

* **ファイルを削除する**には、次のコマンドを使用します。
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    フォルダー **mynewfolder** とファイル **vehicle1_09142014_copy.csv** を 1 つのコマンドでまとめて削除する場合は、--recurse パラメーターを使用します。

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Data Lake Store アカウントのアクセス許可と ACL を操作する

このセクションでは、Azure CLI 2.0 を使用して ACL とアクセス許可を管理する方法について説明します。 Azure Data Lake Store で ACL がどのように実装されているかの詳細な説明については、「[Azure Data Lake Store のアクセス制御](data-lake-store-access-control.md)」を参照してください。

* **ファイルまたはフォルダーの所有者を更新する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **ファイルまたはフォルダーのアクセス許可を更新する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **特定のパスの ACL を取得する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    出力は次のようになります。

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **ACL のエントリを設定する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **ACL のエントリを削除する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **既定の ACL エントリ全体を削除する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **既定でない ACL エントリ全体を削除する**には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Data Lake Store アカウントを削除する
Data Lake Store アカウントを削除するには、次のコマンドを使用します。

```azurecli
az dls account delete --account mydatalakestore
```

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Store CLI 2.0 リファレンス](https://docs.microsoft.com/cli/azure/dls)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md

