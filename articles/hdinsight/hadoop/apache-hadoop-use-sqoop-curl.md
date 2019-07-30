---
title: Curl を使用して Azure HDInsight で Apache Sqoop のデータをエクスポートします。
description: Curl を使用して Apache Sqoop ジョブを HDInsight にリモートで送信する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: ede0538e90e9f35797546f34bfed757c2727b194
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508875"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Curl を使用して HDInsight で Apache Sqoop ジョブを実行します。
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Curl を使用して HDInsight 上の Apache Hadoop クラスターで Apache Sqoop ジョブを実行する方法について説明します。 この記事では、Azure storage からデータをエクスポートし、Curl を使用して SQL Server データベースにインポートする方法を示します。 この記事は、「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の続きです。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、Sqoop ジョブを実行、監視して、その結果を取得する方法を示すために使用します。 これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

## <a name="prerequisites"></a>前提条件

* 「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の「[テスト環境のセットアップ](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)」が完了していること。

* Azure SQL データベースのクエリを実行するクライアント。 [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) または [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md) の使用を検討してください。

* [Curl](https://curl.haxx.se/)。 Curl は、HDInsight クラスターとの間でデータを転送するツールです。

* [jq](https://stedolan.github.io/jq/)。 jq ユーティリティを使用して REST 要求から返された JSON データを処理します。

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Curl を使用して Apache Sqoop ジョブを送信する

Curl を使用して、Apache Sqoop ジョブを使用するデータを Azure storage から SQL Server にエクスポートします。

> [!NOTE]  
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。

このセクションのコマンドの場合、`USERNAME` をクラスターに対して認証するユーザーの名前に置き換え、`PASSWORD` をユーザー アカウントのパスワードに置き換えます。 `CLUSTERNAME` をクラスターの名前に置き換えます。
 
REST API のセキュリティは、 [基本認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    次のような応答を受け取ります。

    ```json
    {"status":"ok","version":"v1"}
    ```

2. `SQLDATABASESERVERNAME`、 `USERNAME@SQLDATABASESERVERNAME`、 `PASSWORD`、`SQLDATABASENAME`を前提条件の適切な値に置き換えます。 次のコマンドを使用して sqoop ジョブを送信します。

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

       * **user.name** : コマンドを実行するユーザー

       * **command** : 実行する Sqoop コマンド。

       * **statusdir** : ジョブのステータスが書き込まれるディレクトリ

     このコマンドは、ジョブのステータスの確認に使用できるジョブ ID を返します。

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. ジョブのステータスを確認するには、次のコマンドを使用します。 `JOBID` を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、`JOBID` は `job_1415651640909_0026` になります。

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    ジョブが完了している場合、ステータスは **SUCCEEDED**になります。
   
   > [!NOTE]  
   > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。

4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには、出力ファイルの場所が含まれます。この場合は、`wasb:///example/data/sqoop/curl` です。 このアドレスではジョブの出力は、HDInsight クラスターが使用する既定のストレージ コンテナーの `example/data/sqoop/curl` ディレクトリに保存されます。

    Azure Portal を使用して、stderr および stdout BLOB にアクセスできます。

5. データがエクスポートされたことを確認するには、SQL クライアントから次のクエリを使用して、エクスポートされたデータを表示します。

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>制限事項
* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。
* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入処理を実行します。

## <a name="summary"></a>まとめ
このドキュメントで示したように、未加工の HTTP 要求を使用して、HDInsight クラスターで Sqoop ジョブを実行、監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、<a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API ガイド</a>に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
[HDInsight 上の Apache Hadoop で Apache Sqoop を使用する](hdinsight-use-sqoop.md)

curl に関するその他の HDInsight の記事:
 
* [Azure REST API を使用して Apache Hadoop クラスターを作成する](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [HDInsight 上の Apache Hadoop で REST を使用して Apache Hive クエリを実行する](apache-hadoop-use-hive-curl.md)
* [HDInsight 上の Apache Hadoop で REST を使用して MapReduce ジョブを実行する](apache-hadoop-use-mapreduce-curl.md)

