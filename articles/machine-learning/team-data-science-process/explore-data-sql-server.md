---
title: SQL Server 仮想マシンに保存されたデータを探索する - Team Data Science Process
description: SQL または Python などのプログラミング言語を使用して Azure 上の SQL Server VM 内に格納されているデータを探索する方法です。
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
ms.openlocfilehash: 33b55afb7796b197f7130ec9288abb01cc115651
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "86085652"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Azure の SQL Server Virtual Machine に格納されているデータを探索する

この記事では、Azure 上の SQL Server VM に格納されているデータを探索する方法について説明します。 データを調べるには、SQL または Python を使用します。

このタスクは、[Team Data Science Process](overview.md) の 1 ステップです。

> [!NOTE]
> このドキュメントのサンプルの SQL ステートメントは、データが SQL Server に存在することを前提としています。 存在しない場合は、クラウド データ サイエンス プロセス マップを参照して、SQL Server へデータを移動する方法を確認してください。
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>SQL スクリプトを使用して SQL データを探索する
SQL Server のデータ ストアの探索に使用できるいくつかのサンプル SQL スクリプトを次に示します。

1. 1 日ごとの所見の数を取得する
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. カテゴリ列内のレベルを取得する 
   
    `select  distinct <column_name> from <databasename>`
3. 2 つのカテゴリ列の組み合わせ内のレベルの数を取得する 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. 数値型列の分布を取得する
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> 実用的な例として、[NYC タクシー データセット](https://www.andresmh.com/nyctaxitrips/)を使用し、エンドツーエンドのチュートリアルの「[IPython Notebook と SQL Server を使用した NYC データの処理](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)」というタイトルの IPNB を参照することができます。
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>Python を使用して SQL データを探索する
データが SQL Server に格納されている場合に、Python を使用してデータを探索し、特徴を生成する手順は、「[Azure BLOB データを高度な分析を使用して処理する](data-blob.md)」で説明されているように、Python を使用して Azure BLOB のデータを処理する手順と似ています。 データは、データベースから pandas データフレームに読み込むと、さらに処理できるようになります。 このセクションでは、データベースに接続して、データ フレームにデータを読み込むプロセスについて記載します。

次の接続文字列形式を使用して pyodbc を使用し Python から SQL Server データベースに接続することができます (サーバー名、データベース名、ユーザー名およびパスワードは使用する特定の値に置き換えてください)。

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Python の [Pandas ライブラリ](https://pandas.pydata.org/) には、Python プログラミングでデータを操作するためのデータ構造とデータ解析ツールの豊富なセットが用意されています。 次のコードは、SQL Server データベースから返される結果を Pandas データ フレームに読み取ります。

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

これで、「 [データ サイエンス環境で Azure BLOB データを処理する](data-blob.md)」トピックで扱うとおりに Pandas データ フレームを操作できるようになりました。

## <a name="the-team-data-science-process-in-action-example"></a>Team Data Science Process の活用例
パブリック データセットを使用した Cortana Analytics Process のエンド ツー エンドのチュートリアル例については、「 [Team Data Science Process の活用: SQL Server の使用](sql-walkthrough.md)」を参照してください。

