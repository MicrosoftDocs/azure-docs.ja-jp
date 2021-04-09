---
title: Azure Blob Storage のデータをサンプリングする - Team Data Science Process
description: プログラムでダウンロードした後、Python で記述されたプロシージャを使用してサンプリングすることによって、Azure Blob Storage に格納されているデータをサンプリングします。
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
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788843"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Azure Blob Storage のデータをサンプリングする

この記事では、プログラムでダウンロードした後、Python で記述されたプロシージャを使用してサンプリングすることによって、Azure Blob Storage に格納されているデータをサンプリングする方法について説明します。

**データをサンプリングする理由**
分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 サンプリングすると、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](./index.yml)の 1 ステップです。

## <a name="download-and-down-sample-data"></a>データのダウンロードとダウン サンプリング
1. 次の Python のサンプル コードから、Blob service を使用して Azure Blob Storage からデータをダウンロードします。 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. 上記でダウンロードしたファイルから、Pandas データ フレームにデータを読み取ります。

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. 次のように、`numpy` の `random.choice` を使用してデータをダウンサンプリングします。

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

これで、上記の 1 パーセントのサンプルのデータ フレームを操作して、さらなる探索および特徴の生成を行えるようになりました。

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>データをアップロードし、Azure Machine Learning で読み取る
次のサンプル コードを使用すると、次のようにデータをダウンサンプリングして、 Azure Machine Learning で直接使用できます。

1. データ フレームをローカル ファイルに書き込む

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. 次のサンプル コードを使用してローカル ファイルを Azure BLOB にアップロードする

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. 次のスクリーン ショットに示すように、Azure Machine Learning の[データのインポート](/azure/machine-learning/studio-module-reference/import-data)を使用して Azure BLOB からデータを読み取ります。

![リーダー BLOB](./media/sample-data-blob/reader_blob.png)
