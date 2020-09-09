---
title: Azure Blob ストレージのデータをサンプリングする - Team Data Science Process
description: Azure Blob Storage に格納されたデータをサンプリングするには、プログラムでデータをダウンロードしてから、Python で記述されたプロシージャを使用してサンプリングします。
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
ms.openlocfilehash: 04528d28e9f54710cd0a63372e32b099c2e07fb5
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026170"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Azure BLOB ストレージのデータをサンプリングする

この記事では、Azure Blob Storage に格納されたデータのサンプリングについて説明します。プログラムからデータをダウンロードし、Python で記述されたプロシージャを使ってそのデータをサンプリングします。

**データをサンプリングする理由**
分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 サンプリングすると、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)の 1 ステップです。

## <a name="download-and-down-sample-data"></a>データのダウンロードとダウン サンプリング
1. 次の Python のサンプル コードから、Blob service を使用して Azure BLOB ストレージからデータをダウンロードします。 

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

3. 次のスクリーン ショットに示すように、Azure Machine Learning の[データのインポート](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)を使用して Azure BLOB からデータを読み取ります。

![リーダー BLOB](./media/sample-data-blob/reader_blob.png)

