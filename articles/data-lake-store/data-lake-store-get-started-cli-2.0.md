---
title: Azure Data Lake Storage Gen1 アカウントの管理 - Azure CLI
description: Azure CLI を使用して、Data Lake Storage Gen1 アカウントを作成し、基本的な操作を実行します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103630"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Azure CLI を使用して Azure Data Lake Storage Gen1 の使用を開始する

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Azure CLI を使用して Azure Data Lake Storage Gen1 アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、ご自分のアカウントの削除などの基本操作を行う方法について説明します。Data Lake Storage Gen1 の詳細については、[Data Lake Storage Gen1 の概要](data-lake-store-overview.md)に関する記事をご覧ください。

Azure CLI は、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスです。 macOS、Linux、および Windows で使用できます。 詳細については、[Azure CLI の概要](/cli/azure)に関するページを参照してください。 コマンドと構文の完全な一覧については、[Azure Data Lake Storage Gen1 CLI リファレンス](/cli/azure/dls)を参照することもできます。


## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure CLI** - 手順については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="authentication"></a>認証

この記事では、Data Lake Storage Gen1 に対してエンド ユーザーとしてログインする比較的単純な認証方法を使用します。 その後、Data Lake Storage Gen1 アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Storage Gen1 には他の認証方法も存在します (**エンド ユーザー認証** と **サービス間認証**)。 認証方法の詳細については、[エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)または[サービス間認証](./data-lake-store-service-to-service-authenticate-using-active-directory.md)に関するページを参照してください。


## <a name="log-in-to-your-azure-subscription"></a>Azure サブスクリプションにログイン

1. Azure サブスクリプションにログインします。

    ```azurecli
    az login
    ```

    使用するコードは次の手順で示します。 Web ブラウザーを使用して https://aka.ms/devicelogin ページを開き、コードを入力して認証を行います。 資格情報を使用してログインするように求めるメッセージが表示されます。

2. ログインすると、アカウントに関連付けられているすべての Azure サブスクリプションの一覧がウィンドウに表示されます。 次のコマンドを使用して、特定のサブスクリプションを使用します。
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 アカウントを作成する

1. 新しいリソース グループを作成します。 次のコマンドで、使用するパラメーター値を指定します。 場所の名前にスペースが含まれる場合は、名前を引用符で囲みます。 たとえば、"East US 2" などとします。 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Data Lake Storage Gen1 アカウントを作成します。
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントでフォルダーを作成する

Data Lake Storage Gen1 アカウントにフォルダーを作成し、データを管理したり、保存したりできます。 次のコマンドを使用して、Data Lake Storage Gen1 アカウントのルートに **mynewfolder** という名前のフォルダーを作成します。

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> `--folder` パラメーターを指定すると、フォルダーが作成されます。 このパラメーターを指定しない場合、コマンドによって Data Lake Storage Gen1 アカウントのルートに mynewfolder という名前の空のファイルが作成されます。
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントへのデータのアップロード

データは Data Lake Storage Gen1 のルート レベルで直接アップロードするか、アカウント内で作成したフォルダーにアップロードすることができます。 以下のスニペットは、前のセクションで作成したフォルダー (**mynewfolder**) にいくつかのサンプル データをアップロードする方法を示します。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\ など) に保存します。

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> 保存先として、ファイル名を含む完全なパスを指定する必要があります。
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントのファイルを一覧表示する

Data Lake Storage Gen1 アカウントのファイルを一覧表示するには、次のコマンドを使用します。

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

この出力は次のように表示されます。

```output
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
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントのデータの名前変更、ダウンロード、削除を行う 

* **ファイルの名前を変更する** には、次のコマンドを使用します。
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **ファイルをダウンロードする** には、次のコマンドを使用します。 既に存在するパスをダウンロード先として指定してください。
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > 対象フォルダーが存在しない場合は、作成されます。
    > 
    >

* **ファイルを削除する** には、次のコマンドを使用します。
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    フォルダー **mynewfolder** とファイル **vehicle1_09142014_copy.csv** を 1 つのコマンドでまとめて削除する場合は、--recurse パラメーターを使用します。

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントのアクセス許可と ACL を操作する

このセクションでは、Azure CLI を使用して ACL とアクセス許可を管理する方法について説明します。 Azure Data Lake Storage Gen1 で ACL がどのように実装されているかについては「[Azure Data Lake Storage Gen1 のアクセス制御](data-lake-store-access-control.md)」に説明があります。そちらをご参照ください。

* **ファイルまたはフォルダーの所有者を更新する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **ファイルまたはフォルダーのアクセス許可を更新する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **特定のパスの ACL を取得する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    出力は次のようになります。

    ```output
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
    ```

* **ACL のエントリを設定する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **ACL のエントリを削除する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **既定の ACL エントリ全体を削除する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **既定でない ACL エントリ全体を削除する** には、次のコマンドを使用します。

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントの削除
Data Lake Storage Gen1 アカウントを削除するには、次のコマンドを使用します。

```azurecli
az dls account delete --account mydatalakestoragegen1
```

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="next-steps"></a>次のステップ
* [Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](data-lake-store-data-scenarios.md) 
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)