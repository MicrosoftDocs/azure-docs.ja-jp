---
title: HDInsight (Apache Hadoop) 上で Hive と連携する Apache Ambari ビューを使用する - Azure
description: Web ブラウザーから Hive ビューを使用して Hive クエリを送信する方法について説明します。 Hive ビューは、Linux ベースの HDInsight クラスターに付属する Ambari Web UI の要素です。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: da4d1ed7dec8b3b0bc61dd2959a868d03875039c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077008"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>HDInsight 上の Apache Hadoop で Apache Ambari Hive ビューを使用する

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ambari Hive ビューを使用して Hive クエリを実行する方法について説明します。 Hive ビューを使用すると、Web ブラウザーからHive クエリを作成、最適化、および実行できます。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Hadoop クラスター。 詳細については、[Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* Web ブラウザー

## <a name="run-a-hive-query"></a>Hive クエリを実行する

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。  手順については、「[クラスターの一覧と表示](../hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。 クラスターは新しいポータル ブレードで開きます。

2. **クラスター ダッシュボード**で **[Ambari ビュー]** を選択します。 認証情報の入力を求められたら、クラスターの作成時に使用したクラスター ログイン (既定値は `admin`) アカウント名とパスワードを入力します。

3. ビューの一覧で、__Hive ビュー__ を選択します。

    ![Apache Ambari の Apache Hive ビューの選択](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Hive ビュー ページは次の図のようになります。

    ![Hive ビューのクエリ ワークシートの画像](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. __[Query]\(クエリ\)__ タブから、次の HiveQL ステートメントをワークシートに貼り付けます。

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    これらのステートメントは次のアクションを実行します。

   * `DROP TABLE`:テーブルが既に存在する場合は、テーブルとデータ ファイルを削除します。

   * `CREATE EXTERNAL TABLE`:新しい "外部" テーブルを Hive に作成します。
     外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

   * `ROW FORMAT`:データがどのように書式設定されているかを示します。 ここでは、各ログのフィールドは、スペースで区切られています。

   * `STORED AS TEXTFILE LOCATION`:データが保存されている場所、およびデータがテキストとして保存されていることを示します。

   * `SELECT`:t4 列の値が [ERROR] であるすべての行の数を選択します。

   > [!IMPORTANT]  
   > __[Database]\(データベース\)__ では、 __[default]\(既定\)__ が選択されたままにしておきます。 このドキュメントの例では、HDInsight に含まれている既定のデータベースを使用します。

5. クエリを開始するには、ワークシートの下にある **[実行]** を選択します。 ボタンがオレンジ色になり、テキストが **[Stop]\(停止\)** に変わります。

6. クエリが完了すると、 **[Results]\(結果\)** タブに操作の結果が表示されます。 次のテキストは、クエリの結果を示します。

        loglevel       count
        [ERROR]        3

    **[ログ]** タブを使用すると、ジョブによって作成されたログ情報を表示できます。

   > [!TIP]  
   > **[結果]** タブの **[アクション]** ドロップダウン ダイアログ ボックスから結果をダウンロードするか保存します。

### <a name="visual-explain"></a>ビジュアルの説明

クエリ プランの視覚化を表示するために、ワークシートの下にある **[Visual Explain]\(ビジュアルの説明\)** タブを選択します。

クエリの **[Visual Explain]** ビューは、複雑なクエリのフローを理解する際に役立ちます。

### <a name="tez-ui"></a>Tez UI

クエリの Tez UI を表示するには、ワークシートの下にある **[Tez UI]** タブを選択します。

> [!IMPORTANT]  
> Tez を使用してもすべてのクエリが解決するとは限りません。 多くのクエリは、Tez を使用することなく解決できます。

## <a name="view-job-history"></a>ジョブ履歴の表示

__[Jobs]\(ジョブ\)__ タブには、Hive クエリの履歴が表示されます。

![Apache Hive の [ジョブ] タブの履歴の表示](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>データベース テーブル

__[Tables]\(テーブル\)__ タブを使用して、Hive データベース内のテーブルを操作できます。

![Apache Hive の [テーブル] タブの画像](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>保存済みのクエリ

**[Query]\(クエリ\)** タブでは、必要に応じてクエリを保存できます。 クエリを保存すると、 __[Saved Queries]\(保存済みクエリ\)__ タブでそのクエリを再利用できます。

![Apache Hive の [保存済みクエリ] タブの表示](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> 保存済みのクエリは、既定のクラスター記憶域に格納されます。 保存済みのクエリは、パス `/user/<username>/hive/scripts` の下にあります。 これらはプレーンテキストの `.hql` ファイルとして格納されます。
>
> クラスターを削除して、記憶域は保持した場合、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) や Data Lake Storage Explorer などのユーティリティを ([Azure Portal](https://portal.azure.com) から) 使用してクエリを取得することができます。

## <a name="user-defined-functions"></a>ユーザー定義関数

ユーザー定義関数 (UDF) を使用して、Hive を拡張できます。 UDF を使用すると、HiveQL では簡単にモデル化できない機能またはロジックを実装できます。

Hive ビューの上部にある **[UDF]** タブを使用して、UDF のセットを宣言および保存します。 これらの UDF は**クエリ エディター**で使用できます。

![Apache Hive の [UDF] タブの表示](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Hive ビューに UDF を追加すると、 **[Insert udfs]\(UDF の挿入\)** ボタンが**クエリ エディター**の下部に表示されます。 このエントリを選択すると、Hive ビューで定義した UDF のドロップダウン リストが表示されます。 UDF を選択すると、HiveQL ステートメントがクエリに追加され、UDF が有効になります。

たとえば、次のプロパティを持つ UDF を定義したとします。

* リソース名: myudfs

* リソースのパス: /myudfs.jar

* UDF 名: myawesomeudf

* UDF のクラス名: com.myudfs.Awesome

**[Insert udfs]\(UDF の挿入\)** ボタンを使用すると、**myudfs** という名前のエントリと、そのリソースに定義されている UDF ごとにドロップダウン リストが表示されます。 この例では **myawesomeudf** です。 このエントリを選択すると、クエリの先頭に次の内容が追加されます。

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

これにより、クエリでこの UDF を使用できます。 たとえば、「 `SELECT myawesomeudf(name) FROM people;` 」のように入力します。

HDInsight において Hive で UDF を使用する方法の詳細については、以下の記事を参照してください。

* [HDInsight 上の Apache Hive と Apache Pig で Python を使用する](python-udf-hdinsight.md)
* [HDInsight にカスタムの Apache Hive UDF を追加する方法](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive の設定

Hive の実行エンジンを Tez (既定値) から MapReduce に変更するなど、さまざまな Hive 設定を変更できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
