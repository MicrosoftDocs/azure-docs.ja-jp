---
title: Synapse Studio ノートブック
description: この記事では、データの準備と視覚化を行うために、Azure Synapse Studio ノートブックを作成および開発する方法について学習します。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d5ff3fb988a7e907308ccccc8d0900d45a0601c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671606"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Synapse Studio ノートブックを作成、開発、管理する

Synapse Studio ノートブックは、ライブ コード、視覚化、および説明テキストを含むファイルを作成するための Web インターフェイスです。 ノートブックは、アイデアを確認し、簡単な実験を使用してデータから分析情報を得るのに最適な場所です。 また、ノートブックは、データの準備、データの視覚化、機械学習、およびその他のビッグ データのシナリオで広く使用されています。

Azure Synapse Studio ノートブックでは、次のことができます。

* 設定作業をせずに使用を開始する。
* 組み込みのエンタープライズ セキュリティ機能を使用してデータを安全に保つ。
* 未加工の形式 (CSV、txt、JSON など) 全体のデータ、処理されたファイル形式 (parquet、Delta Lake、ORC など)、および Spark と SQL に対する SQL の表形式データ ファイルを分析する。
* 高度な作成機能と組み込みのデータの視覚化で生産性を高める。

この記事では、Azure Synapse Studio でノートブックを使用する方法について説明します。

## <a name="preview-of-the-new-notebook-experience"></a>新しいノートブック エクスペリエンスのプレビュー
Synapse チームは、Microsoft のお客様に一貫したノートブック エクスペリエンスを提供し、検出可能性、生産性、共有、コラボレーションを最大化するための新しいノートブック コンポーネントを Synapse Studio に導入しました。 この新しいノートブック エクスペリエンスのプレビュー版が利用可能になりました。 ノートブック ツールバーの **[プレビュー機能]** ボタンをオンにしてください。 次の表では、既存のノートブック ("従来のノートブック") と新しいプレビュー版の機能を比較します。  

|機能|従来のノートブック|プレビュー版ノートブック|
|--|--|--|
|%run| サポートされていません | &#9745;|
|%history| サポートされていません |&#9745;
|%load| サポートされていません |&#9745;|
|%%html| サポートされていません |&#9745;|
|ドラッグ アンド ドロップでセルを移動する| サポートされていません |&#9745;|
|Display() の出力を永続化する|&#9745;| 使用できません |
|すべてを取り消す| &#9745;| 使用できません|
|上のすべてのセルを実行する|&#9745;| 使用できません |
|下のすべてのセルを実行する|&#9745;| 使用できません |
|ツールバー ボタンを使用してテキスト セルを書式設定する|&#9745;| 使用できません |
|セルの操作を元に戻す| &#9745;| 使用できません |


## <a name="create-a-notebook"></a>ノートブックを作成する

ノートブックを作成するには、2 つの方法があります。 新しいノートブックを作成することも、既存のノートブックを **オブジェクト エクスプローラー** から Azure Synapse ワークスペースにインポートすることもできます。 Azure Synapse Studio ノートブックでは、標準の Jupyter Notebook IPYNB ファイルを認識することができます。

![インポートによるノートブックの作成](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>ノートブックを開発する

ノートブックは、個別またはグループとして実行できるコードまたはテキストの個々のブロックである、セルで構成されます。

### <a name="add-a-cell"></a>セルを追加する

ノートブックに新しいセルを追加するには、複数の方法があります。

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

1. 左上の **[+ セル]** ボタンを展開し、 **[コード セルの追加]** または **[テキスト セルの追加]** を選択します。

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 2 つのセル間のスペースをポイントし、 **[コードの追加]** または **[テキストの追加]** を選択します。

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [コマンド モードのショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルの上にセルを挿入するには、**A** キーを押します。 現在のセルの下にセルを挿入するには、**B** キーを押します。


# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

1. 左上の **[+ セル]** ボタンを展開し、 **[code cell]\(コード セル\)** または **[Markdown cell]\(マークダウン セル\)** を選択します。
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. セルの先頭にあるプラス記号を選択し、 **[code cell]\(コード セル\)** または **[Markdown cell]\(マークダウン セル\)** を選択します。

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. [コマンド モードの aznb ショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルの上にセルを挿入するには、**A** キーを押します。 現在のセルの下にセルを挿入するには、**B** キーを押します。

---

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

   ![Synapse spark マジック コマンド](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>一時テーブルを使用して言語間でデータを参照する

Synapse Studio ノートブックでは、異なる言語間でデータや変数を直接参照することはできません。 Spark では、複数の言語間で一時テーブルを参照することができます。 以下は、回避策として Spark の一時テーブルを使用して、`PySpark` および `SparkSQL` で `Scala` DataFrame を読み取る方法の例です。

1. セル 1 で、Scala を使用して SQL プール コネクタから DataFrame を読み取り、一時テーブルを作成します。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
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

IntelliSense の機能は、言語によって異なる成熟度レベルにあります。 次の表を使用して、サポートされている内容を確認してください。

|Languages| 構文の強調表示 | 構文のエラー マーカー  | 構文のコード補完 | 変数のコード補完| システム関数のコード補完| ユーザー関数のコード補完| スマート インデント | コードの折りたたみ|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|はい|はい|はい|はい|はい|はい|はい|はい|
|Spark (Scala)|はい|はい|はい|はい|-|-|-|はい|
|SparkSQL|はい|はい|-|-|-|-|-|-|
|.NET for Spark (C#)|はい|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>ツールバー ボタンを使用してテキスト セルを書式設定する

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

テキスト セル ツールバーの書式ボタンを使用して、一般的なマークダウン アクションを実行することができます。 これには、テキストを太字にする、テキストを斜体にする、コード スニペットを挿入する、順序指定されていないリストを挿入する、順序指定されているリストを挿入する、URL からイメージを挿入するなどが含まれます。

  ![Synapse テキスト セル ツールバー](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

プレビュー版ノートブック エクスペリエンスでは、まだ書式ボタン ツールバーを使用できません。 

---

### <a name="undo-cell-operations"></a>セルの操作を元に戻す

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

**[元に戻す]** ボタンを選択するか、**Ctrl + Z** キーを押して、最新のセルの操作を取り消します。 これで、最新の 20 個の過去のセル アクションを元に戻すことができます。 

   ![Synapse セルを元に戻す](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

プレビュー版ノートブック エクスペリエンスでは、まだセルの操作を取り消すことができません。 

---

### <a name="move-a-cell"></a>セルを移動する

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

省略記号 (...) を選択して、右端にある追加のセル アクション メニューにアクセスします。 その後、 **[Move cell up]\(セルを上に移動\)** または **[Move cell down]\(セルを下に移動\)** を選択して、現在のセルを移動します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを上に移動するには、**Ctrl + Alt + ↑** キーを押します。 現在のセルを下に移動するには、**Ctrl + Alt + ↓** キーを押します。

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

セルの左側をクリックし、目的の位置までドラッグします。 
    ![Synapse でのセルの移動](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>セルを削除する

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

セルを削除するには、省略記号 (...) を選択して、右端にある追加のセル アクション メニューにアクセスし、 **[セルの削除]** を選択します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを削除するには、**D、D** キーを押します。
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

セルを削除するには、セルの右側にある削除ボタンを選択します。 

[コマンド モードのショートカット キーを使用](#shortcut-keys-under-command-mode)することもできます。 現在のセルを削除するには、**Shift + D** キーを押します。 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>セル入力を折りたたむ

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

現在のセルの下部にある矢印ボタンを選択して、折りたたみます。 これを展開するには、セルが折りたたまれている状態で矢印ボタンを選択します。

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

セル ツールバーで **[More commands]\(その他のコマンド\)** 省略記号 (...) を選択し、 **[input]\(入力\)** を選択して、現在のセルの入力を折りたたみます。 これを展開するには、セルが折りたたまれている状態で **[input hidden]\(非表示の入力\)** を選択します。

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>セル出力を折りたたむ

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

現在のセル出力の左上にある **出力の折りたたみ** ボタンを選択して、折りたたみます。 これを展開するには、セル出力が折りたたまれている状態で **[Show cell output]\(セル出力の表示\)** を選択します。

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

セル ツールバーで **[More commands]\(その他のコマンド\)** 省略記号 (...) を選択し、 **[output]\(出力\)** を選択して、現在のセルの出力を折りたたみます。 これを展開するには、セルの出力が非表示になっている状態で同じボタンを選択します。

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>ノートブックを実行する

ノートブックのコード セルは、個別に、または一度にすべて実行できます。 ノートブックには各セルの状態と進行状況が示されます。

### <a name="run-a-cell"></a>セルを実行する

セルでコードを実行するには、いくつかの方法があります。

1. 実行するセルをポイントし、 **[セルの実行]** ボタンを選択するか、**Ctrl + Enter** キーを押します。

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [コマンド モードのショートカット キー](#shortcut-keys-under-command-mode)を使用します。 現在のセルを実行し、下のセルを選択するには、**Shift + Enter** キーを押します。 現在のセルを実行し、新しいセルを下に挿入するには、**Alt + Enter** キーを押します。

---

### <a name="run-all-cells"></a>すべてのセルを実行する
現在のノートブック内のすべてのセルを順に実行するには、 **[すべて実行]** ボタンを選択します。

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>上または下のすべてのセルを実行する

右端にある追加のセル アクション メニューにアクセスするには、省略記号 ( **...** ) を選択します。その後、 **[Run cells above]\(上のセルの実行\)** を選択して、現在のセルの上にあるものをすべて順に実行します。 現在のセルの下にあるものをすべて実行するには、 **[Run cells below]\(下のセルの実行\)** を選択します。

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>実行中のすべてのセルを取り消す
実行中のセルまたはキューで待機しているセルを取り消すには、 **[すべて取り消し]** ボタンを選択します。 
   ![cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

プレビュー版ノートブック エクスペリエンスでは、実行中のすべてのセルを取り消すことがまだできません。 

---



### <a name="reference-notebook"></a>参照ノートブック

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

サポートされていません。

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

```%run <notebook path>``` マジック コマンドを使用して、現在のノートブックのコンテキスト内で別のノートブックを参照することができます。 参照ノートブックで定義されているすべての変数を、現在のノートブックで使用できます。 ```%run``` マジック コマンドでは、入れ子になった呼び出しはサポートされますが、再帰呼び出しはサポートされません。 ステートメントの深さが 5 を超えると、例外が発生します。 現在 ```%run``` コマンドでは、ノートブック パスをパラメーターとして渡すことのみが可能です。 

例: ``` %run /path/notebookA ```.

---


### <a name="cell-status-indicator"></a>セルの状態インジケーター

セルの下に詳細なセルの実行の状態が表示されます。これは、現在の進行状況を確認するのに役立ちます。 セルの実行が完了すると、合計期間と終了時間を含む実行の概要が表示され、今後の参照用に保持されます。

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 進行状況インジケーター

Azure Synapse Studio ノートブックは、純粋に Spark ベースです。 コード セルは、サーバーレス Apache Spark プールに対してリモートで実行されます。 Spark ジョブの進行状況インジケーターは、リアルタイムの進行状況バーで示されます。これは、ジョブの実行状態を把握するのに役立ちます。
ジョブごとまたはステージごとのタスク数は、Spark ジョブの並列レベルを識別するのに役立ちます。 また、ジョブ (またはステージ) の名前のリンクを選択することで、特定のジョブ (またはステージ) の Spark UI をさらに掘り下げて調べることもできます。


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark セッションの構成

**[Configure session]\(セッションの構成\)** では、現在の Spark セッションに設定するタイムアウト期間、Executor の数とサイズを指定できます。 Spark セッションを再起動すると、構成の変更が有効になります。 キャッシュされたノートブック変数はすべて消去されます。

[![session-management](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark セッションを構成するためのマジック コマンド
マジック コマンド **%%configure** を使用して Spark セッションの設定を指定することもできます。 設定を有効にするには、Spark セッションを再起動する必要があります。 **%%configure** はノートブックの先頭で実行することをお勧めします。 次に例を示します。有効なパラメーターの一覧については、 https://github.com/cloudera/livy#request-body を参照してください。 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```


## <a name="bring-data-to-a-notebook"></a>データをノートブックに取り込む

以下のコード サンプルに示すように、Azure Blob Storage、Azure Data Lake Store Gen 2、および SQL プールからデータを読み込むことができます。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2 から CSV を Spark DataFrame として読み取る

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage から CSV を Spark DataFrame として読み取る

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>プライマリ ストレージ アカウントからデータを読み取る

プライマリ ストレージ アカウントのデータに直接アクセスすることができます。 秘密鍵を指定する必要はありません。 データ エクスプローラーで、ファイルを右クリックし、 **[新しいノートブック]** を選択して、データ エクストラクターが自動生成された新しいノートブックを表示します。

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>ノートブックを保存する

ワークスペースには、単一のノートブックまたはすべてのノートブックを保存できます。

1. 単一のノートブックに加えた変更を保存するには、ノートブックのコマンド バーにある **[公開]** ボタンを選択します。

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. ワークスペース内のすべてのノートブックを保存するには、ワークスペースのコマンド バーにある **[すべて公開]** ボタンを選択します。 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

ノートブックのプロパティでは、保存時にセル出力を含めるかどうかを構成できます。

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>マジック コマンド
Azure Synapse Studio ノートブックでは、使い慣れた Jupyter マジック コマンドを使用できます。 以下の一覧で、現在使用可能なマジック コマンドを確認してください。 ユーザーのニーズに合ったマジック コマンドを引き続き作成できるよう、[GitHub でユース ケース](https://github.com/MicrosoftDocs/azure-docs/issues/new)についてお知らせください。

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

使用可能なライン マジック: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

使用可能なセル マジック: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)、[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

使用可能なライン マジック: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)、[%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history)、[%run](#reference-notebook)、[%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

使用可能なセル マジック: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)、[%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html)、[%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>ノートブックを統合する

### <a name="add-a-notebook-to-a-pipeline"></a>ノートブックをパイプラインに追加する

右上隅にある **[パイプラインへの追加]** ボタンを選択して、ノートブックを既存のパイプラインに追加するか、新しいパイプラインを作成します。

![ノートブックをパイプラインに追加する](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>パラメーター セルを指定する

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

ノートブックをパラメーター化するには、省略記号 (...) を選択して、右端にある追加のセル アクション メニューにアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

ノートブックをパラメーター化するには、省略記号 (...) を選択して、セル ツールバーの **[more commands]\(その他のコマンド\)** にアクセスします。 次に、 **[パラメーター セルを切り替えます]** を選択して、セルをパラメーター セルとして指定します。

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

パラメーター セルが検索され、このセルは実行時に渡されるパラメーターの既定値として扱われます。 実行エンジンは、既定値を上書きするために、入力パラメーターを含んだ新しいセルをパラメーター セルの下に追加します。 パラメーター セルが指定されていない場合、挿入されたセルがノートブックの上部に挿入されます。


### <a name="assign-parameters-values-from-a-pipeline"></a>パイプラインからパラメーター値を割り当てる

パラメーターを使ってノートブックを作成すると、Azure Synapse Notebook アクティビティを使用して、パイプラインからそれを実行できます。 アクティビティをパイプライン キャンバスに追加した後、 **[設定]** タブの **[基本パラメーター]** セクションで、パラメーターの値を設定できます。 

![パラメーターを割り当てる](./media/apache-spark-development-using-notebooks/assign-parameter.png)

パラメーター値を割り当てるときは、[パイプライン式言語](../../data-factory/control-flow-expression-language-functions.md) または [システム変数](../../data-factory/control-flow-system-variables.md)を使用できます。



## <a name="shortcut-keys"></a>ショートカット キー

Jupyter Notebook と同様に、Azure Synapse Studio ノートブックにはモーダル ユーザー インターフェイスがあります。 キーボードの動作は、ノートブック セルのモードによって異なります。 Synapse Studio ノートブックでは、特定のコード セルに対して、コマンド モードと編集モードという 2 つのモードがサポートされます。

1. 入力を求めるテキスト カーソルがない場合、セルはコマンド モードになります。 セルがコマンド モードの場合、ノートブックを全体として編集できますが、個々のセルに入力することはできません。 `ESC` キーを押すか、マウスを使用してセルのエディター領域の外側を選択し、コマンド モードに入ります。

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. 編集モードは、エディター領域への入力を求めるテキスト カーソルによって示されます。 セルが編集モードの場合、セルに入力することができます。 `Enter` キーを押すか、マウスを使用してセルのエディター領域を選択し、編集モードに入ります。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>コマンド モードのショートカット キー

# <a name="classical-notebook"></a>[従来のノートブック](#tab/classical)

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

# <a name="preview-notebook"></a>[プレビュー版ノートブック](#tab/preview)

| アクション |Synapse Studio ノートブックのショートカット  |
|--|--|
|現在のセルを実行して下のものを選択する | Shift + Enter |
|現在のセルを実行して下に挿入する | Alt + Enter |
|現在のセルを実行する| Ctrl + Enter |
|上のセルを選択する| 上へ |
|下のセルを選択する| [下へ] |
|前のセルを選択する| K |
|次のセルを選択する| J |
|上にセルを挿入する| A |
|下にセルを挿入する| B |
|選択されたセルを削除する| Shift + D |
|編集モードに切り替える| 次に、 |

---

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

---

## <a name="next-steps"></a>次のステップ
- [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [クイック スタート:Web ツールを使用して Azure Synapse Analytics で Apache Spark プールを作成する](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics の Apache Spark とは](apache-spark-overview.md)
- [Azure Synapse Analytics で .NET for Apache Spark を使用する](spark-dotnet.md)
- [.NET for Apache Spark のドキュメント](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)