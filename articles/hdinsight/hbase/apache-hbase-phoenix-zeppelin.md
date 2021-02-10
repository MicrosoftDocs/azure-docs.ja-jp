---
title: Apache Phoenix を使用して Azure HDInsight で Apache Base クエリを実行する
description: Apache Zeppelin を使用して Phoenix で Apache Base クエリを実行する方法について説明します。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944768"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin を使用して Azure HDInsight で Apache HBase に対して Apache Phoenix クエリを実行する

Apache Phoenix は、HBase 上に構築されるオープンソースの超並列リレーショナル データベース レイヤーです。 Phoenix では、HBase に対して SQL のようなクエリを使うことができます。 Phoenix は基盤の JDBC ドライバーを使って、SQL テーブル、インデックス、ビュー、およびシーケンスを作成、削除、変更できるようにします。  Phoenix を使用して、行を個別に、また一括で更新することもできます。 Phoenix は、MapReduce ではなく NOSQL ネイティブ コンパイルを使ってクエリをコンパイルして、HBase に基づく待機時間の短いアプリケーションを作成できるようにします。

オープンソースで Web ベースのノートブックである Apache Zeppelin では、対話型のデータ分析と SQL や Scala などの言語を使用して、データドリブンの共同作業ドキュメントを作成できます。 データ開発者およびデータ サイエンティストが、データ操作用のコードを開発、整理、実行、および共有するために役立ちます。 コマンド ラインを参照しなくても、またはクラスターの詳細を必要とせずに、結果を視覚化できます。

HDInsight ユーザーは Apache Zeppelin を使用して Phoenix テーブルのクエリを実行できます。 Apache Zeppelin は HDInsight クラスターと統合されており、それを使用するための追加の手順はありません。 JDBC インタープリターを使用して Zeppelin Notebook を作成し、Phoenix SQL クエリの作成を開始するだけです。

## <a name="prerequisites"></a>前提条件

HDInsight 内の Apache HBase クラスター 「[Apache HBase の使用](./apache-hbase-tutorial-get-started-linux.md)」を参照してください。

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin Note を作成する

1. URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` の `CLUSTERNAME` をお使いのクラスターの名前に置き換えます。 Web ブラウザーに URL を入力します。 クラスターのログイン ユーザー名とパスワードを入力します。

1. Zeppelin ページから、 **[Create new note]\(新しいノートの作成\)** をクリックします。

    ![HDInsight 対話型クエリ Zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. **[Create new note]\(新しいノートの作成\)** ダイアログで、次の値を入力または選択します。

    - [Note Name]\(ノート名\):ノートの名前を入力します。
    - [Default interpreter]\(既定のインタープリター\):ドロップダウン リストから **[jdbc]** を選択します。

    次に **[Create Note]\(ノートの作成\)** を選択します。

1. Notebook のヘッダーに [接続] というステータスが表示されることを確認します。 これは、右上隅の緑色の点で示されます。

    ![Zeppelin ノートブック のステータス](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin Notebook のステータス")

1. HBase テーブルを作成します。 次のコマンドを入力して、**Shift + Enter** キーを押します。

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    最初の行の **%jdbc(phoenix)** ステートメントは、Phoenix JDBC インタープリターを使用するようにノートブックに指示します。

1. 作成されたテーブルを表示します。

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. テーブルに値を挿入します。

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. テーブルにクエリを実行します。

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. レコードを削除します。

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. テーブルを削除します。

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>次のステップ

- [Apache Phoenix now supports Zeppelin in Azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight) (Azure HDInsight で Apache Phoenix が Zeppelin を新しくサポート)
- [Apache Phoenix grammar](https://phoenix.apache.org/language/index.html) (Apache Phoenix の文法)