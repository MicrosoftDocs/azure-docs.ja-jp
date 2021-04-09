---
title: Pandas を使用して Azure Blob Storage のデータを探索する - Team Data Science Process
description: Pandas Python パッケージを使用して Azure BLOB コンテナーに格納されているデータを探索する方法について説明します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98805671"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Pandas を使用して Azure Blob Storage のデータを探索する

この記事では、[Pandas](https://pandas.pydata.org/) Python パッケージを使用して Azure BLOB コンテナーに格納されているデータを探索する方法について説明します。

このタスクは、[Team Data Science Process](overview.md) の 1 ステップです。

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントの作成](../../storage/common/storage-account-create.md)」をご覧ください。
* Azure Blob Storage アカウントにデータが格納されている。 手順については、「 [Azure Storage との間でのデータの移動](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Pandas データ フレームにデータを読み込む
データセットを探索および操作するには、先にデータを BLOB ソースからローカル ファイルにダウンロードしておく必要があります。このローカル ファイルを、Pandas データ フレームに読み込むことができます。 この手順に必要な操作は次のとおりです。

1. Blob service を使用する次の Python コード サンプルで Azure BLOB からデータをダウンロードします。 次のコードの変数を、実際の値に置き換えます。

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. ダウンロードしたファイルから、Pandas データ フレームにデータを読み込みます。

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

これで、データを探索し、このデータセットでの特徴を生成する準備が整いました。

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Pandas を使用したデータ探索の例
次に、Pandas を使用してデータを探索する方法の例をいくつかを示します。

1. **行と列の数**

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. 次のデータセットの始めまたは終わりの数 **行** を **検査** します。

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. 次のサンプル コードを使用して、インポートされた各列の **データ型** を確認します。

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. 次のように、データ セット内の列の **基本的な統計** を確認します。

    ```python
    dataframe_blobdata.describe()
    ```

1. 次のように、各列の値のエントリの数を確認します。

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **不足値の数をカウント** します。

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. データ内の特定の列に **不足値** がある場合は、次のように削除できます。

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    不足値を置き換えるを別の方法として、モード関数を使用した以下のものを実行します。

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. 変数の分布をプロットする可変個の箱を使用して、 **ヒストグラム図** を作成します。

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. scatterplot を使用して、または組み込みの相関関数を使用して、変数間の **関連付け** を確認します。

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
