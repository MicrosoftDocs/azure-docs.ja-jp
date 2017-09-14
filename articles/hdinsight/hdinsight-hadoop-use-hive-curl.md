---
title: "HDInsight での Hadoop Hive と Curl の使用 - Azure | Microsoft Docs"
description: "Curl を使用して Pig ジョブを HDInsight にリモートで送信する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/12/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: afd0bc8c19456fd123f53de7d1704619405bed67
ms.contentlocale: ja-jp
ms.lasthandoff: 06/10/2017

---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>REST を使用した HDInsight における Hadoop での Hive クエリの実行

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API を使用して Azure HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。

[Curl](http://curl.haxx.se/) は、未加工の HTTP 要求を使用して HDInsight とやり取りする方法を示すために用いられます。 [Jq](http://stedolan.github.io/jq/)ユーティリティを使用して REST 要求から返された JSON データを処理します。

> [!NOTE]
> Linux ベースの Hadoop サーバーは使い慣れているが HDInsight の使用は初めてという場合は、「[Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」を参照してください。

## <a id="curl"></a>Hive クエリの実行

> [!NOTE]
> cURL、または WebHCat を用いたその他 REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。
>
> このセクションのコマンドでは、 **USERNAME** をクラスターに対して認証するユーザーの名前に置き換え、 **PASSWORD** をユーザー アカウントのパスワードに置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
>
> REST API のセキュリティは、 [基本認証](http://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-u** : 要求の認証に使用するユーザー名とパスワード
   * **-G**: GET 操作であることを示しています。

     URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、すべての要求において共通です。 パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 次のコマンドを使用して、Hive のバージョンを要求することもできます。

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

     この要求では、次のような応答が返されます。

       {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 次のコマンドを使用して、 **log4jLogs** という名前のテーブルを作成します。

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    この要求では次のパラメーターが用いられます。

   * **-d** - `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

     * **user.name** : コマンドを実行するユーザー
     * **execute** : 実行する HiveQL ステートメント
     * **statusdir**: ジョブのステータスが書き込まれるディレクトリ。

     これらのステートメントは次のアクションを実行します。
   * **DROP TABLE**: テーブルが既に存在する場合は削除されます。
   * **CREATE EXTERNAL TABLE** : Hive に新しく '外部' テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

     > [!NOTE]
     > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や別の MapReduce 操作の場合です。
     >
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

   * **ROW FORMAT**: データ形式 各ログのフィールドは、スペースで区切られています。
   * **STORED AS TEXTFILE LOCATION**: データの格納先 (example/data ディレクトリ) と、データがテキストとして格納されていることを示します。
   * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 この値を含む行が 3 行あるため、このステートメントでは値 **3** が返されます。

     > [!NOTE]
     > Curl を使用したとき、HiveQL ステートメントのスペースが `+` に置き換わることに注意してください。 スペースを含む引用符で囲まれた値 (区切り記号など) は `+`に置き換わりません。

   * **INPUT__FILE__NAME LIKE '%25.log'**: このステートメントでは、.log で終わるファイルのみの検索に限定されます。

     > [!NOTE]
     > `%25` は % の URL エンコード形式であるため、実際の条件は`like '%.log'` です。 URL で特殊文字と見なされるため、% は URL エンコードである必要があります。

     このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

       {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、**JOBID** は `job_1415651640909_0026` になります。

    ジョブが完了している場合、ステータスは **SUCCEEDED** です。

   > [!NOTE]
   > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。 Jq は、状態値のみを取得するために使用されます。

4. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには、出力ファイルの場所が含まれます。この場合は、**/example/curl** です。 このアドレスは、クラスターの既定ストレージに **example/curl** ディレクトリの出力を格納します。

    これらのファイルを一覧表示およびダウンロードするには [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)を使用します。 Azure Storage で Azure CLI を使用する際の詳細については、[Azure Storage で Azure CLI 2.0 を使用する](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli#create-and-manage-blobs)を参照してください。

5. 次のステートメントを使用して、 **errorLogs**という名前の新しい "内部" テーブルを作成します。

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="/example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive
    ```

    これらのステートメントは次のアクションを実行します。

   * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 このステートメントが作成する内部テーブルは、Hive データ ウェアハウスに格納され、Hive により完全管理されます。

     > [!NOTE]
     > 外部テーブルとは異なり、内部テーブルを削除すると基盤となるデータも削除されます。

   * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC は、Hive データを格納するための高度に最適化された効率的な形式です。
   * **INSERT OVERWRITE ...SELECT** - **[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。
   * **SELECT**: 新しい **errorLogs** テーブルからすべての行を選択します。

6. 返されたジョブ ID を使用して、ジョブのステータスを確認します。 確認できたら、前述のように Azure CLI を使用して、結果をダウンロードして表示します。 出力には、それぞれに **[ERROR]**が含まれた 3 つの行が返されます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合、デバッグ情報については、次のドキュメントを参照してください。

* [HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)

このドキュメントで使用されている REST API の詳細については、「[WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するドキュメントをご覧ください。

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx



