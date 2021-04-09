---
title: SQL と Python を使用して SQL Server で特徴を作成する - Team Data Science Process
description: SQL と Python を使用して、Azure 上の SQL Server VM に格納されているデータの特徴を生成します。Team Data Science Process の一部です。
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322031"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>SQL と Python を使用して SQL Server のデータの特徴を作成する
このドキュメントでは、Azure の SQL Server VM に保存されたデータから、アルゴリズムの学習効率を高めることのできる特徴を生成する方法について説明します。 このタスクを実行するには、SQL または Python のようなプログラミング言語を使用できます。 ここでは、両方の方法を説明しています。

このタスクは、 [Team Data Science Process (TDSP)](./index.yml)の 1 ステップです。

> [!NOTE]
> 実用的な例として、[NYC タクシー データセット](https://www.andresmh.com/nyctaxitrips/)を使用し、エンドツーエンドのチュートリアルの「[IPython Notebook と SQL Server を使用した NYC データの処理](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)」というタイトルの IPNB を参照することができます。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントの作成](../../storage/common/storage-account-create.md)」をご覧ください。
* データが SQL Server に格納されている。 格納されていない場合、データの移動手順については、「 [Azure Machine Learning 用にデータを Azure SQL Database に移動する](move-sql-azure.md) 」をご覧ください。

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>SQL を使用した特徴の生成
このセクションでは SQL を使用して特徴を生成する方法について説明します。  

* [カウント ベースの特徴の生成](#sql-countfeature)
* [ビン分割特徴の生成](#sql-binningfeature)
* [1 つの列からの特徴の展開](#sql-featurerollout)

> [!NOTE]
> 追加の特徴を生成すると、既存のテーブルに列として追加するか、追加の特徴と主キーを持つ新しいテーブルを作成して元のテーブルと結合することができます。
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>カウント ベースの特徴の生成
このドキュメントでは、カウント特徴を生成する 2 つの方法を示します。 最初の方法は、条件付きの合計を使用します。2 番目の方法は、Where 句を使用します。 これらの新しい機能を (主キーの列を使用することで) 元のテーブルと結合して、カウント特徴と元のデータを一緒にすることができます。

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>ビン分割特徴の生成
次の例は、数値型の列をビン分割 (5 つの箱を使用) して、特徴として代わりに使用できる、ビン分割特徴を生成する方法を示しています。

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>1 つの列からの特徴の展開
このセクションでは、テーブル内の 1 つの列を展開して追加の特徴を生成する方法を示します。 この例は、特徴を生成しようとするテーブルに、緯度や経度の列があることを前提としています。

緯度と経度の位置データ (リソースは `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`の stackoverflow) の簡単な概要を次に示します。 フィールドから特徴を作成する前に、位置データを理解するために役立つ情報をいくつか示します。

* 符号は、地球の北半球か南半球か、および東半球か西半球かを示します。
* 100 の位が 0 でなければ、それは経度を使用していることを示します。緯度ではありません。
* 10 の位は、最大で約 1,000 キロメートル単位で位置を示します。 これは、どの大陸または海洋にいるかに関する有益な情報です。
* 1 の位 (1 つの 10 進数の角度) は、最大 111 キロメートル (60 海里、約 69 マイル) 単位で位置を示します。 これは、大まかにどの大きい州または国や地域にいるかを示します。
* 小数第 1 位は最大 11.1 km: になります。この位で、大都市と隣接する大都市の位置を識別できます。
* 小数第 2 位は、最大 1.1 km に値します。ある村と隣接する村を識別できます。
* 小数第 3 位は、最大 110 m に値します。大規模な農地または施設の構内を識別できます。
* 小数第 4 位は、最大 11 m に値します。土地の一区画を識別できます。 これは、未修正の GPS ユニットの一般的な支障のない精度と比較できます。
* 小数第 5 位は、1.1 m に値します。木々を互いに識別することができます。 商用の GPS ユニットにおいて、このレベルの精度は微分補正によってのみ実現できます。
* 小数第 6 位は、最大 0.11 m に値します。このレベルは、造園設計、道路建設において構造を詳細に配置するために使用できます。 氷河と川の動きを追跡するには十分すぎるはずです。 この目標は、GPS の微分補正など、GPS を使用した精密な測定で実現できます。

位置情報の特徴付けは、地域、位置、および都市の情報に分けて、次のように実行します。 1 回に、Bing Maps API などの REST エンドポイントを呼び出すこともできます (リージョン/地区情報を取得するには `https://msdn.microsoft.com/library/ff701710.aspx` を参照してください)。

```sql
select
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

これらの位置ベースの特徴をさらに使用すると、前述した追加のカウント特徴を生成できます。

> [!TIP]
> お好みのプログラム言語でレコードを挿入できます。 書き込みの効率を向上させるために、データをチャンクで挿入する必要がある場合があります。 pyodbc を使用してそのようにする方法の例については、[こちら](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)を参照してください。
> [BCP ユーティリティ](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Azure Machine Learning への接続
新しく生成された特徴は、既存のテーブルに列として追加するか、新しいテーブルに格納して機械学習の元のテーブルと結合することができます。 特徴を生成できます。作成済みであれば、次に示すように、Azure ML の[データのインポート](/azure/machine-learning/studio-module-reference/import-data) モジュールを使用してアクセスすることができます。

![Azure ML リーダー](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Python などのプログラミング言語の使用
データが SQL Server に格納されている場合に Python を使用して特徴を生成する手順は、Python を使用して Azure BLOB のデータを処理する手順と似ています。 比較については、[データ サイエンス環境での Azure BLOB データの処理](data-blob.md)に関するページを参照してください。 データをさらに処理するには、データをデータベースから pandas データ フレームに読み込みます。 データベースに接続してデータ フレームにデータを読み込むプロセスについては、このセクションで説明します。

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

[Pandas を使用して Azure BLOB ストレージ データの特徴を作成する](./explore-data-blob.md)方法についてのページの説明に従って、Pandas データ フレームを使用できるようになりました。