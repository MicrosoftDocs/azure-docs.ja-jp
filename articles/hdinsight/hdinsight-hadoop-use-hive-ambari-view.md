---
title: "Ambari ビューによる HDInsight (Hadoop) での Hive の使用 - Azure | Microsoft Docs"
description: "Web ブラウザーから Hive ビューを使用して Hive クエリを送信する方法について説明します。 Hive ビューは、Linux ベースの HDInsight クラスターに付属する Ambari Web UI の要素です。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: b5bf9042921dfb2344c2c6e03990578fa9ce4f5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>HDInsight での Ambari Hive ビューと Hadoop の使用

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari Hive ビューを使用して Hive クエリを実行する方法について説明します。 Ambari は、Linux ベースの HDInsight クラスターに付属する管理および監視ユーティリティです。 Ambari が提供する機能の 1 つに、Hive クエリを実行するときに使用される Web UI があります。

> [!NOTE]
> Ambari には数多くの機能がありますが、このドキュメントではそれらについて説明しません。 詳細については、「 [Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 クラスターの作成については、[HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページ を参照してください。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する Azure HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以降の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="open-the-hive-view"></a>Hive ビューを開く

Ambari ビューは、Azure Portal から開くことができます。 HDInsight クラスターを選択し、**[クイック リンク]** セクションの **[Ambari ビュー]** を選択します。

![ポータルの [クイック リンク] セクション](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

ビューの一覧で、__Hive ビュー__を選択します。

![Hive ビューを選択する](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Ambari にアクセスする際には、サイトに対する認証を求められます。 クラスターの作成時に使用した管理者アカウント名 (既定値は `admin`) とパスワードを入力します。

次の図ようなページが表示されます。

![Hive ビューのクエリ ワークシートの画像](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>クエリを実行する

Hive クエリを実行するには、Hive ビューから以下の手順を使用します。

1. __[Query]\(クエリ\)__ タブから、次の HiveQL ステートメントをワークシートに貼り付けます。

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    これらのステートメントは次のアクションを実行します。

   * `DROP TABLE`: テーブルが既に存在する場合は、テーブルとデータ ファイルを削除します。

   * `CREATE EXTERNAL TABLE`: 新しい "外部" テーブルを Hive に作成します。
   外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

   * `ROW FORMAT`: データの書式設定の方法を指示します。 ここでは、各ログのフィールドは、スペースで区切られています。

   * `STORED AS TEXTFILE LOCATION`: データが保存されている場所、およびデータがテキストとして保存されていることを指示します。

   * `SELECT`: t4 列の値が [ERROR] であるすべての行の数を選択します。

     > [!NOTE]
     > 基になるデータが外部ソース (データの自動アップロード処理や別の MapReduce 操作など) によって更新する場合は、外部テーブルを使用します。 外部テーブルを削除しても、データは削除*されません*。テーブル定義のみが削除されます。

    > [!IMPORTANT]
    > __[Database]\(データベース\)__ では、__[default]\(既定\)__ が選択されたままにしておきます。 このドキュメントの例では、HDInsight に含まれている既定のデータベースを使用します。

2. クエリを開始するために、ワークシートの下にある **[Execute]\(実行\)** ボタンを使用します。 ボタンがオレンジ色になり、テキストが **[Stop]\(停止\)** に変わります。

3. クエリが完了すると、**[Results]\(結果\)** タブに操作の結果が表示されます。 次のテキストは、クエリの結果を示します。

        sev       cnt
        [ERROR]   3

    **[Logs]\(ログ\)** タブを使用すると、ジョブが作成したログ情報を表示できます。

   > [!TIP]
   > **[Query Process Results]\(クエリ処理結果\)** セクションの左上にある **[Save results]\(結果の保存\)** ドロップダウン ダイアログで、結果をダウンロードまたは保存します。

4. このクエリの最初の 4 行を選択し、次に **[Execute]\(実行\)** を選択します。 ジョブが完了しても結果が示されないことに注意してください。 クエリの一部が選択されているときに **[Execute]** ボタンを使用すると、選択したステートメントのみが実行されます。 この場合は、テーブルの行を取得する最後のステートメントが選択範囲に含まれていませんでした。 その行のみを選択して **[Execute]** を使用すると、予想どおりの結果が表示されます。

5. ワークシートを追加するために、**クエリ エディター**の下部にある **[New Worksheet]\(新規ワークシート\)** ボタンを使用します。 新しいワークシートに、次の HiveQL ステートメントを入力します。

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  これらのステートメントは次のアクションを実行します。

   * **CREATE TABLE IF NOT EXISTS**: 既存のテーブルがない場合は、テーブルを作成します。 **EXTERNAL** キーワードは使用されていないため、内部テーブルが作成されます。 内部テーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。 外部テーブルとは異なり、内部テーブルを削除すると基になるデータも削除されます。

   * **STORED AS ORC**: Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。

   * **INSERT OVERWRITE ...SELECT**: `[ERROR]` を含む **log4jLogs** テーブルから列を選択し、**errorLogs** テーブルにデータを挿入します。

**[Execute]** ボタンを使用して、このクエリを実行します。 クエリから返される行が 0 の場合、**[Results]** タブには情報が含まれません。 クエリが完了すると、状態として **SUCCEEDED** が示されます。

### <a name="visual-explain"></a>ビジュアルの説明

クエリ プランの視覚化を表示するために、ワークシートの下にある **[Visual Explain]\(ビジュアルの説明\)** タブを選択します。

クエリの **[Visual Explain]** ビューは、複雑なクエリのフローを理解する際に役立ちます。 クエリ エディターの **[Explain]\(説明\)** ボタンを使用して、このビューに対応するテキストを見ることができます。

### <a name="tez-ui"></a>Tez UI

クエリの Tez UI を表示するために、ワークシートの下にある **[Tez]** タブを選択します。

> [!IMPORTANT]
> Tez を使用してもすべてのクエリが解決するとは限りません。 多くのクエリは、Tez を使用することなく解決できます。 

クエリの解決に Tez を使用している場合、有向非巡回グラフ (DAG) が表示されます。 過去に実行したクエリの DAG を表示する場合、または Tez プロセスをデバッグする場合は、代わりに [[Tez View]\(Tez ビュー\)](hdinsight-debug-ambari-tez-view.md) を使用します。

## <a name="view-job-history"></a>ジョブ履歴の表示

__[Jobs]\(ジョブ\)__ タブには、Hive クエリの履歴が表示されます。

![ジョブ履歴の画像](./media/hdinsight-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>データベース テーブル

__[Tables]\(テーブル\)__ タブを使用して、Hive データベース内のテーブルを操作できます。

![[Tables]\(テーブル\) タブの画像](./media/hdinsight-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>保存済みのクエリ

**[Query]\(クエリ\)** タブでは、必要に応じてクエリを保存できます。 クエリを保存すると、__[Saved Queries]\(保存済みクエリ\)__ タブでそのクエリを再利用できます。

![[Saved Queries]\(保存済みクエリ\) タブの画像](./media/hdinsight-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>ユーザー定義関数

Hive はユーザー定義関数 (UDF) を使用して拡張することもできます。 UDF を使用すると、HiveQL では簡単にモデル化できない機能またはロジックを実装できます。

Hive ビューの上部にある **[UDF]** タブを使用して、UDF のセットを宣言および保存します。 これらの UDF は**クエリ エディター**で使用できます。

![[UDF] タブの画像](./media/hdinsight-hadoop-use-hive-ambari-view/user-defined-functions.png)

Hive ビューに UDF を追加すると、**[Insert udfs]\(UDF の挿入\)** ボタンが**クエリ エディター**の下部に表示されます。 このエントリを選択すると、Hive ビューで定義した UDF のドロップダウン リストが表示されます。 UDF を選択すると、HiveQL ステートメントがクエリに追加され、UDF が有効になります。

たとえば、次のプロパティを持つ UDF を定義したとします。

* リソース名: myudfs

* リソースのパス: /myudfs.jar

* UDF 名: myawesomeudf

* UDF のクラス名: com.myudfs.Awesome

**[Insert udfs]\(UDF の挿入\)** ボタンを使用すると、**myudfs** という名前のエントリと、そのリソースに定義されている UDF ごとにドロップダウン リストが表示されます。 この場合は、**myawesomeudf** が表示されます。 このエントリを選択すると、クエリの先頭に次の内容が追加されます。

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

これにより、クエリでこの UDF を使用できます。 たとえば、「 `SELECT myawesomeudf(name) FROM people;`」のように入力します。

HDInsight において Hive で UDF を使用する方法の詳細については、以下の記事を参照してください。

* [HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)
* [HDInsight にカスタムの Hive UDF を追加する方法](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive の設定

Hive の実行エンジンを Tez (既定値) から MapReduce に変更するなど、さまざまな Hive 設定を変更できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)
