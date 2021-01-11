---
title: Synapse Studio ノートブック
description: この記事では、データの準備と視覚化を行うために、Azure Synapse Studio (プレビュー) ノートブックを作成および開発する方法について学習します。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6e0062450889a2bbbdfcd47137ffbe36b83cae57
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849100"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Synapse Studio (プレビュー) ノートブックを作成、開発、管理する

Synapse Studio (プレビュー) ノートブックは、ライブ コード、視覚化、および説明テキストを含むファイルを作成するための Web インターフェイスです。 ノートブックは、アイデアを確認し、簡単な実験を使用してデータから分析情報を得るのに最適な場所です。 また、ノートブックは、データの準備、データの視覚化、機械学習、およびその他のビッグ データのシナリオで広く使用されています。

Azure Synapse Studio ノートブックでは、次のことができます。

* 設定作業をせずに使用を開始する。
* 組み込みのエンタープライズ セキュリティ機能を使用してデータを安全に保つ。
* 未加工の形式 (CSV、txt、JSON など) 全体のデータ、処理されたファイル形式 (parquet、Delta Lake、ORC など)、および Spark と SQL に対する SQL の表形式データ ファイルを分析する。
* 高度な作成機能と組み込みのデータの視覚化で生産性を高める。

この記事では、Azure Synapse Studio でノートブックを使用する方法について説明します。

## <a name="create-a-notebook"></a>ノートブックを作成する

ノートブックを作成するには、2 つの方法があります。 新しいノートブックを作成することも、既存のノートブックを**オブジェクト エクスプローラー**から Azure Synapse ワークスペースにインポートすることもできます。 Azure Synapse Studio ノートブックでは、標準の Jupyter Notebook IPYNB ファイルを認識することができます。

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>ノートブックを開発する

ノートブックは、個別またはグループとして実行できるコードまたはテキストの個々のブロックである、セルで構成されます。

### <a name="add-a-cell"></a>セルを追加する

ノートブックに新しいセルを追加するには、複数の方法があります。

1. 左上の **[+ セル]** ボタンを展開し、 **[コード セルの追加]** または **[テキスト セルの追加]** を選択します。

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 2 つのセル間のスペースをポイントし、 **[コードの追加]** または **[テキストの追加]** を選択します。

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [コマンド モードのショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルの上にセルを挿入するには、**A** キーを押します。 現在のセルの下にセルを挿入するには、**B** キーを押します。

### <a name="set-a-primary-language"></a>第一言語を設定する

Azure Synapse Studio ノートブックでは、次の 4 つの Apache Spark 言語がサポートされます。

* PySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET for Apache Spark (C#)

上部のコマンド バーにあるドロップダウン リストから、新しく追加されたセルの第一言語を設定できます。

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>複数の言語を使用する

1 つのノートブックで、セルの先頭に正しい言語マジック コマンドを指定することで、複数の言語を使用できます。 次の表に、セルの言語を切り替えるマジック コマンドを一覧表示します。

|マジック コマンド |Language | 説明 |  
|---|------|-----|
|%%pyspark| Python | Spark コンテキストに対して、**Python** クエリを実行します。  |
|%%spark| Scala | Spark コンテキストに対して、**Scala** クエリを実行します。  |  
|%%sql| SparkSQL | Spark コンテキストに対して、**SparkSQL** クエリを実行します。  |
|%%csharp | .NET for Spark C# | Spark コンテキストに対して、 **.NET for Spark C#** クエリを実行します。 |

次の図は、**Spark(Scala)** ノートブックで、 **%%pyspark** マジック コマンドを使用する PySpark クエリ、または **%%sql** マジック コマンドでの SparkSQL クエリの記述方法の例です。 ノートブックの第一言語が PySpark に設定されていることに注目してください。

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>一時テーブルを使用して言語間でデータを参照する

Synapse Studio ノートブックでは、異なる言語間でデータや変数を直接参照することはできません。 Spark では、複数の言語間で一時テーブルを参照することができます。 以下は、回避策として Spark の一時テーブルを使用して、`PySpark` および `SparkSQL` で `Scala` DataFrame を読み取る方法の例です。

1. セル 1 で、Scala を使用して SQL プール コネクタから DataFrame を読み取り、一時テーブルを作成します。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. セル 2 で、Spark SQL を使用してデータに対してクエリを実行します。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. セル 3 では、PySpark のデータを使用します。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE スタイルの IntelliSense

Azure Synapse Studio ノートブックは、IDE スタイルの IntelliSense をセル エディターに取り込むために、Monaco エディターと統合されています。 構文の強調表示、エラー マーカー、および自動コード補完は、コードを記述したり、問題をより迅速に特定したりするために役立ちます。

IntelliSense の機能は、言語によって異なる成熟度レベルにあります。 次の表を使用して、サポートされているものを確認してください。

|Languages| 構文の強調表示 | 構文のエラー マーカー  | 構文のコード補完 | 変数のコード補完| システム関数のコード補完| ユーザー関数のコード補完| スマート インデント | コードの折りたたみ|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|はい|はい|はい|はい|はい|はい|はい|はい|
|Spark (Scala)|はい|はい|はい|はい|-|-|-|はい|
|SparkSQL|はい|はい|-|-|-|-|-|-|
|.NET for Spark (C#)|はい|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>ツールバー ボタンを使用してテキスト セルを書式設定する

テキスト セル ツールバーの書式ボタンを使用して、一般的なマークダウン アクションを実行することができます。 これには、テキストを太字にする、テキストを斜体にする、コード スニペットを挿入する、順序指定されていないリストを挿入する、順序指定されているリストを挿入する、URL からイメージを挿入するなどが含まれます。

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>セルの操作を元に戻す
**[元に戻す]** ボタンをクリックするか、**Ctrl + Z** キーを押して、最新のセルの操作を取り消します。 これで、最新の 20 個の過去のセル アクションを元に戻すことができます。 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>セルを移動する

省略記号 (...) を選択して、右端にある追加のセル アクション メニューにアクセスします。 その後、 **[Move cell up]\(セルを上に移動\)** または **[Move cell down]\(セルを下に移動\)** を選択して、現在のセルを移動します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを上に移動するには、**Ctrl + Alt + ↑** キーを押します。 現在のセルを下に移動するには、**Ctrl + Alt + ↓** キーを押します。

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>セルを削除する

セルを削除するには、省略記号 (...) を選択して、右端にある追加のセル アクション メニューにアクセスし、 **[セルの削除]** を選択します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを削除するには、**D、D** キーを押します。
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>セル入力を折りたたむ
現在のセルの下部にある矢印ボタンをクリックして、折りたたみます。 これを展開するには、セルが折りたたまれている状態で矢印ボタンをクリックします。

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>セル出力を折りたたむ

現在のセル出力の左上にある**出力の折りたたみ** ボタンをクリックして、折りたたみます。 これを展開するには、セル出力が折りたたまれている状態で **[Show cell output]\(セル出力の表示\)** をクリックします。

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>ノートブックを実行する

ノートブックのコード セルは、個別に、または一度にすべて実行できます。 ノートブックには各セルの状態と進行状況が示されます。

### <a name="run-a-cell"></a>セルを実行する

セルでコードを実行するには、いくつかの方法があります。

1. 実行するセルをポイントし、 **[セルの実行]** ボタンを選択するか、**Ctrl + Enter** キーを押します。

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 右端にある追加のセル アクション メニューにアクセスするには、省略記号 ( **...** ) を選択します。その後、 **[セルの実行]** を選択します。

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. [コマンド モードのショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルを実行し、下のセルを選択するには、**Shift + Enter** キーを押します。 現在のセルを実行し、新しいセルを下に挿入するには、**Alt + Enter** キーを押します。


### <a name="run-all-cells"></a>すべてのセルを実行する
現在のノートブック内のすべてのセルを順に実行するには、 **[すべて実行]** ボタンをクリックします。

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>上または下のすべてのセルを実行する

右端にある追加のセル アクション メニューにアクセスするには、省略記号 ( **...** ) を選択します。その後、 **[Run cells above]\(上のセルの実行\)** を選択して、現在のセルの上にあるものをすべて順に実行します。 現在のセルの下にあるものをすべて実行するには、 **[Run cells below]\(下のセルの実行\)** を選択します。

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>実行中のすべてのセルを取り消す
実行中のセルまたはキューで待機しているセルを取り消すには、 **[すべて取り消し]** ボタンをクリックします。 
   ![cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>セルの状態インジケーター

セルの下に詳細なセルの実行の状態が表示されます。これは、現在の進行状況を確認するのに役立ちます。 セルの実行が完了すると、合計期間と終了時間を含む実行の概要が表示され、今後の参照用に保持されます。

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 進行状況インジケーター

Azure Synapse Studio ノートブックは、純粋に Spark ベースです。 コード セルは、Spark プールに対してリモートで実行されます。 Spark ジョブの進行状況インジケーターは、リアルタイムの進行状況バーで示されます。これは、ジョブの実行状態を把握するのに役立ちます。
ジョブごとまたはステージごとのタスク数は、Spark ジョブの並列レベルを識別するのに役立ちます。 また、ジョブ (またはステージ) の名前のリンクをクリックすることで、特定のジョブ (またはステージ) の Spark UI をさらに掘り下げて調べることもできます。


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark セッションの構成

**[Configure session]\(セッションの構成\)** では、現在の Spark セッションに設定するタイムアウト期間、Executor の数とサイズを指定できます。 Spark セッションを再起動すると、構成の変更が有効になります。 キャッシュされたノートブック変数はすべて消去されます。

[![session-management](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Spark セッションの構成パネルで Spark セッション レコメンダーを使用できるようになりました。 セッション構成パネルから Spark プールを直接選択して、使用しているノードの数と、使用可能な残りの Executor の数を確認できます。 これらの情報は、セッションのサイズを何度も変更する代わりに、適切に設定するのに役立ちます。

![session-recommend](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>データをノートブックに取り込む

以下のコード サンプルに示すように、Azure Blob Storage、Azure Data Lake Store Gen 2、および SQL プールからデータを読み込むことができます。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2 から CSV を Spark DataFrame として読み取る

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage から CSV を Spark DataFrame として読み取る

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>プライマリ ストレージ アカウントからデータを読み取る

プライマリ ストレージ アカウントのデータに直接アクセスすることができます。 秘密鍵を指定する必要はありません。 データ エクスプローラーで、ファイルを右クリックし、 **[新しいノートブック]** を選択して、データ エクストラクターが自動生成された新しいノートブックを表示します。

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>ノートブックでデータを視覚化する

### <a name="produce-rendered-table-view"></a>レンダリングされたテーブル ビューを生成する

表形式の結果ビューには、横棒グラフ、折れ線グラフ、円グラフ、散布図、および面グラフを作成するためのオプションが表示されます。 コードを記述しなくても、データを視覚化できます。 グラフは **[グラフのオプション]** でカスタマイズできます。 

既定では、 **%%sql** マジック コマンドの出力が、レンダリングされたテーブル ビューに表示されます。 Spark DataFrame または Resilient Distributed Dataset (RDD) 関数で <code>display(df)</code> を呼び出して、レンダリングされたテーブル ビューを生成することができます。

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>大規模なデータセットから組み込みグラフを視覚化する 

既定では、<code>display(df)</code> 関数は、グラフを表示するためにデータの最初の 1000 行のみを取得します。 **[Aggregation over all results]\(すべての結果の集計\)** をオンにして **[適用]** ボタンをクリックすると、データセット全体からグラフが生成されます。 グラフの設定が変更されると Spark ジョブがトリガーされます。計算を完了してグラフを表示するのにしばらく時間がかかります。 
    [![builtin-charts-aggregation-all](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>データの統計情報を視覚化する
<code>display(df, summary = true)</code> を使用すると、各列の列名、列の種類、一意の値、欠損値など、特定の Spark DataFrame の統計の概要を確認できます。 また、特定の列を選択して、最小値、最大値、平均値、標準偏差を表示することもできます。
    [ ![builtin-charts-summary](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png) ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>HTML または対話型のライブラリをレンダリングする

**displayHTML()** を使用して、HTML または対話型のライブラリ (**ボケ**など) をレンダリングすることができます。

次の図は、**ボケ**を使用して、マップ上にグリフをプロットする例です。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

上の図を描画するには、次のサンプル コードを実行します。

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>ノートブックを保存する

ワークスペースには、単一のノートブックまたはすべてのノートブックを保存できます。

1. 単一のノートブックに加えた変更を保存するには、ノートブックのコマンド バーにある **[公開]** ボタンを選択します。

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. ワークスペース内のすべてのノートブックを保存するには、ワークスペースのコマンド バーにある **[すべて公開]** ボタンを選択します。 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

ノートブックのプロパティでは、保存時にセル出力を含めるかどうかを構成できます。

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>マジック コマンド
Azure Synapse Studio ノートブックでは、使い慣れた Jupyter マジック コマンドを使用できます。 以下の一覧で、現在使用可能なマジック コマンドを確認してください。 ご自分のニーズに合わせて、引き続きマジック コマンドをさらに作成できるように、GitHub でユース ケースをお知らせください。

使用可能なライン マジック: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%time it](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

使用可能なセル マジック: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>ノートブックを調整する

### <a name="add-a-notebook-to-a-pipeline"></a>ノートブックをパイプラインに追加する

右上隅にある **[パイプラインへの追加]** ボタンをクリックして、ノートブックを既存のパイプラインに追加するか、新しいパイプラインを作成します。

![add-to-pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>パラメーター セルを指定する

ノートブックをパラメーター化するには、右端にある省略記号 (...) を選択して、追加のセル アクション メニューにアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

パラメーター セルが検索され、このセルは実行時に渡されるパラメーターの既定値として扱われます。 実行エンジンは、既定値を上書きするために、パラメーター セルの下に入力パラメーターを持つ新しいセルを追加します。 パラメーター セルが指定されていない場合、挿入されたセルがノートブックの上部に挿入されます。

### <a name="assign-parameters-values-from-a-pipeline"></a>パイプラインからパラメーター値を割り当てる

パラメーターを使ってノートブックを作成すると、Azure Synapse Notebook アクティビティを使用して、パイプラインからそれを実行できます。 アクティビティをパイプライン キャンバスに追加した後、 **[設定]** タブの **[基本パラメーター]** セクションで、パラメーターの値を設定できます。 

![assign-parameter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

パラメーター値を割り当てるときは、[パイプライン式言語](../../data-factory/control-flow-expression-language-functions.md) または [システム変数](../../data-factory/control-flow-system-variables.md)を使用できます。



## <a name="shortcut-keys"></a>ショートカット キー

Jupyter Notebook と同様に、Azure Synapse Studio ノートブックにはモーダル ユーザー インターフェイスがあります。 キーボードの動作は、ノートブック セルのモードによって異なります。 Synapse Studio ノートブックでは、特定のコード セルに対して、コマンド モードと編集モードという 2 つのモードがサポートされます。

1. 入力を求めるテキスト カーソルがない場合、セルはコマンド モードになります。 セルがコマンド モードの場合、ノートブックを全体として編集できますが、個々のセルに入力することはできません。 `ESC` キーを押すか、マウスを使用してセルのエディター領域の外側をクリックし、コマンド モードに入ります。

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 編集モードは、エディター領域への入力を求めるテキスト カーソルによって示されます。 セルが編集モードの場合、セルに入力することができます。 `Enter` キーを押すか、マウスを使用してセルのエディター領域をクリックし、編集モードに入ります。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>コマンド モードのショートカット キー

次のキーストローク ショートカットを使用すると、Azure Synapse ノートブックのコードをより簡単に移動して実行できます。

| アクション |Synapse Studio ノートブックのショートカット  |
|--|--|
|現在のセルを実行して下のものを選択する | Shift + Enter |
|現在のセルを実行して下に挿入する | Alt + Enter |
|上のセルを選択する| 上へ |
|下のセルを選択する| [下へ] |
|上にセルを挿入する| A |
|下にセルを挿入する| B |
|選択されたセルを上に拡張する| Shift + Up |
|選択されたセルを下に拡張する| Shift + Down|
|セルを上に移動する| Ctrl + Alt + ↑ |
|セルを下に移動する| Ctrl + Alt + ↓ |
|選択されたセルを削除する| D、D |
|編集モードに切り替える| 次に、 |

### <a name="shortcut-keys-under-edit-mode"></a>編集モードのショートカット キー

次のキーストローク ショートカットを使用すると、編集モードのときに Azure Synapse ノートブックのコードをより簡単に移動して実行できます。

| アクション |Synapse Studio ノートブックのショートカット  |
|--|--|
|カーソルを上に移動する | 上へ |
|カーソルを下に移動する|[下へ]|
|元に戻す|Ctrl + Z|
|やり直し|Ctrl + Y|
|コメント化する/コメントを解除する|Ctrl + /|
|前の単語を削除する|Ctrl + Backspace|
|後の単語を削除する|Ctrl + Delete|
|セルの先頭に移動する|Ctrl + Home|
|セルの末尾に移動する |Ctrl + End|
|1 単語左に移動する|Ctrl + Left|
|1 単語右に移動する|Ctrl + Right|
|すべて選択する|Ctrl + A|
|インデントする| Ctrl + ]|
|インデントを解除する|Ctrl + [|
|コマンド モードに切り替える| Esc |

## <a name="next-steps"></a>次のステップ
- [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [クイック スタート: Web ツールを使用して Azure Synapse Analytics で Apache Spark プール (プレビュー) を作成する](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics の Apache Spark とは](apache-spark-overview.md)
- [Azure Synapse Analytics で .NET for Apache Spark を使用する](spark-dotnet.md)
- [.NET for Apache Spark のドキュメント](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
