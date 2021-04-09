---
title: Azure BLOB データを高度な分析を使用して処理する - Team Data Science Process
description: 高度な分析を使用してデータを探索し、Azure BLOB ストレージに保存されているデータから特徴を生成します。
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93307162"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>高度な分析を使用して Azure BLOB データを処理する
このドキュメントでは、データの探索および Azure BLOB ストレージに保存されたデータからの特徴の生成について説明します。 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Pandas データ フレームにデータを読み込む
データセットを探索および操作するには、データを BLOB ソースからローカル ファイルにダウンロードする必要があります。このローカル ファイルは、Pandas データ フレームに読み込むことができます。 この手順に必要な操作は次のとおりです。

1. BLOB サービスを使用する次の Python のサンプル コードによって、Azure BLOB からデータをダウンロードします。 次のコードの変数を、実際の値に置き換えます。 
   
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
2. ダウンロードしたファイルから、Pandas データ フレームにデータを読み込みます。
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

これで、データを探索し、このデータセットでの特徴を生成する準備が整いました。

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>データの探索
次に、Pandas を使用してデータを探索する方法の例をいくつかを示します。

1. 行と列の数を調べる 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. 次に示すように、データセット内の最初と最後の数行を検査します。
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. 次のサンプル コードを使用して、インポートされた各列のデータ型を確認します。
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. 次のように、データ セット内の列の基本的な統計を確認します。
   
    ```python
    dataframe_blobdata.describe()
    ```
5. 次のように、各列の値のエントリの数を確認します。
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. 次のサンプル コードを使用して、各列の実際のエントリの数により不足値の数をカウントします。
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. データ内の特定の列に不足値がある場合は、次のように削除できます。
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   不足値を置き換えるを別の方法として、モード関数を使用した以下のものを実行します。
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. 変数の分布をプロットする可変個の箱を使用して、ヒストグラム図を作成します。    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. scatterplot を使用して、または組み込みの相関関数を使用して、変数間の関連付けを確認します。
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>特徴の生成
次のように、Python を使用して特徴を生成できます。

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>インジケーター値ベースの特徴の生成
カテゴリの特徴は、次のように作成できます。

1. カテゴリの列の分布を検査します。
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. 列の値ごとにインジケーター値を生成します。
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. インジケーター列と元のデータ フレームを結合します。 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. 元の変数自体を削除します。
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>ビン分割特徴の生成
ビン分割特徴を生成するには、次のように進めます。

1. 数値列をビン分割する列のシーケンスを追加します。
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. ビン分割を一連のブール型の変数に変換します。
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. 最後に、ダミー変数を元のデータ フレームと結合します。
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Azure BLOB にデータを書き戻して Azure Machine Learning で使用する
データを探索して必要な特徴を作成したら、次の手順を使用して、Azure BLOB に (サンプリングまたは特徴を生成した) データをアップロードして Azure Machine Learning で使用します。Azure Machine Learning Studio (クラシック) でも、追加の特徴を作成できます。 

1. ローカル ファイルへのデータ フレームの書き込み
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. 次のように、データを Azure BLOB にアップロードします。
   
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
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. これで、次の画面に示すように、Azure Machine Learning の [データのインポート][import-data] モジュールを使用して BLOB からデータを読み取ることができます。

![リーダー BLOB][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data