---
title: Azure 上の SQL Server にデータをサンプリングする - Team Data Science Process
description: SQL または Python プログラミング言語を使用して Azure 上の SQL Server にサンプリングされたデータを格納した後、Azure Machine Learning にデータを移動します。
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321868"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Azure の SQL Server のデータをサンプリングする

この記事では、SQL または Python プログラミング言語を使って、Azure の SQL Server に格納されているデータをサンプリングする方法について説明します。 また、サンプリングしたデータをファイルを保存し、Azure BLOB にアップロードして、Azure Machine Learning Studio に読み込むことで、データを Azure Machine Learning に移動する方法についても説明します。

Python のサンプリングでは、[pyodbc](https://code.google.com/p/pyodbc/) ODBC ライブラリを使用して、Azure 上の SQL Server に接続し、[Pandas](https://pandas.pydata.org/) ライブラリを使用してサンプリングを実行します。

> [!NOTE]
> このドキュメントにあるサンプルの SQL コードは、データが Azure の SQL Server に存在することを前提としています。 SQL Server にデータが存在しない場合、データを Azure の SQL Server に移動する方法については、「[Azure VM の SQL Server へのデータの移動](move-sql-server-virtual-machine.md)」をご覧ください。
> 
> 

**データをサンプリングする理由**
分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 サンプリングすると、データの理解、探索、および特徴エンジニアリングが容易になります。 [Team Data Science Process (TDSP)](./index.yml) におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプを迅速に作成できるようにすることです。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](./index.yml)の 1 ステップです。

## <a name="using-sql"></a><a name="SQL"></a>SQL の使用
このセクションでは、SQL を使用して、データベース内のデータに対して簡単なランダム サンプリングを実行するいくつかの方法について説明します。 データのサイズとその分布に基づいて方法を選択してください。

次の 2 つの項目は、SQL Server の `newid` を使ってサンプリングを実行する方法を示しています。 選ぶ方法は、サンプルをどの程度ランダムにするかによって変わります (次のサンプル コードでは、pk_id は自動生成された主キーであるものと想定しています)。

1. 制限が少なめのランダム サンプル

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. よりランダムなサンプル 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Tablesample はデータのサンプリングに使うこともできます。 データのサイズが大きい場合 (異なるページのデータと相関関係がないと仮定すると)、クエリを妥当な時間内に完了するには、このオプションがより適したアプローチになることがあります。

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> このサンプリングされたデータを新しいテーブルに格納すると、このデータから探索および特徴の生成を行えます。
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Azure Machine Learning への接続
Azure Machine Learning の[データのインポート][import-data] モジュールで上記のサンプル クエリを直接使用して、データをその場でダウンサンプリングし、Azure Machine Learning の実験で使用できます。 リーダー モジュールを使ってサンプリングされたデータを読み取るスクリーンショットを次に示します。

![リーダー SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Python プログラミング言語の使用
このセクションでは、Python で [pyodbc ライブラリ](https://code.google.com/p/pyodbc/) を使用して SQL Server データベースに対する ODBC 接続を確立する方法について説明します。 データベース接続文字列は次のようになります (サーバー名、データベース名、ユーザー名、およびパスワードは、使用する構成に置き換えます)。

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Python の [Pandas ライブラリ](https://pandas.pydata.org/) には、Python プログラミングでデータを操作するためのデータ構造とデータ解析ツールの豊富なセットが用意されています。 次のコードでは、Azure SQL Database 内のテーブルから 0.1% のデータのサンプルが Pandas のデータに読み込まれます。

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

これで、Pandas データ フレームでサンプリングされたデータを操作できるようになりました。 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Azure Machine Learning への接続
次のサンプル コードを使用すると、ダウンサンプリングされたデータをファイルに保存し、Azure BLOB にアップロードすることができます。 BLOB 内のデータは、[データのインポート][import-data] モジュールを使用して Azure Machine Learning の実験に直接読み込むことができます。 手順は次のとおりです。 

1. Pandas データ フレームをローカル ファイルに書き込む

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. ローカル ファイルを Azure BLOB にアップロードする

    ```python
    from azure.storage import BlobService
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

3. 次のスクリーンショットに示すように、Azure Machine Learning の[データのインポート][import-data] モジュールを使って Azure BLOB からデータを読み取ります。

![リーダー BLOB][2]

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process の活用例
パブリック データセットを使った Team Data Science Process の例のチュートリアルについては、「[Team Data Science Process の活用: SQL Server の使用](sql-walkthrough.md)」をご覧ください。

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data