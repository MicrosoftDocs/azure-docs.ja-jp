---
title: HDInsight 上で Curl を使用して Apache Hadoop Hive を使用する - Azure
description: Curl を使用して Apache Pig ジョブを HDInsight にリモートで送信する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 3e7ee293c2c33f169b3a5e4a7bded3190a9dc209
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443226"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>HDInsight 上の Apache Hadoop で REST を使用して Apache Hive クエリを実行する

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API を使用して Azure HDInsight クラスター上の Apache Hadoop で Apache Hive クエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* バージョン 3.4 以上の HDInsight クラスターでの Linux ベースの Hadoop。

  > [!IMPORTANT]  
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* REST クライアント。 このドキュメントでは、Windows PowerShell と [Curl](https://curl.haxx.se/) の例を使用します。

    > [!NOTE]  
    > Azure PowerShell は、HDInsight 上の Hive を操作するための専用のコマンドレットを提供します。 詳細については、[Azure PowerShell を使用した Apache Hive の実行](apache-hadoop-use-hive-powershell.md)に関するドキュメントをご覧ください。

また、このドキュメントでは、Windows PowerShell と [Jq](https://stedolan.github.io/jq/) を使用して、REST 要求から返された JSON データを処理します。

## <a id="curl"></a>Hive クエリを実行する

> [!NOTE]  
> cURL、または WebHCat を用いたその他 REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。
>
> REST API のセキュリティは、 [基本認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. このドキュメントのスクリプトで使用されるクラスター ログインを設定するには、次のいずれかのコマンドを使用します。

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. クラスター名を設定するには、次のいずれかのコマンドを使用します。

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. HDInsight クラスターに接続できることを確認するには、次のいずれかのコマンドを使用します。

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    次のような応答を受け取ります。

    ```json
    {"status":"ok","version":"v1"}
    ```

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * `-u` - 要求の認証に使用するユーザー名とパスワード。
    * `-G` - この要求が GET 操作であることを示します。

   URL の最初の部分 `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` は、すべての要求で同じです。 パス `/status` は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 次のコマンドを使用して、Hive のバージョンを要求することもできます。

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    この要求では、次のような応答が返されます。

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. 次のコマンドを使用して、 **log4jLogs** という名前のテーブルを作成します。

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    この要求は、REST API に要求の一部としてデータを送信する、POST メソッドを使用します。 要求では次のデータ値が送信されます。

     * `user.name` - コマンドを実行するユーザー。
     * `execute` - 実行する HiveQL ステートメント。
     * `statusdir` - このジョブの状態が書き込まれるディレクトリ。

   これらのステートメントは次のアクションを実行します。
   
   * `DROP TABLE` - テーブルが既に存在する場合は削除されます。
   * `CREATE EXTERNAL TABLE` - 新しい "外部" テーブルを Hive に作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

     > [!NOTE]  
     > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や別の MapReduce 操作の場合です。
     >
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

   * `ROW FORMAT` - データがどのように書式設定されるか。 各ログのフィールドは、スペースで区切られています。
   * `STORED AS TEXTFILE LOCATION` - データの格納先 (example/data ディレクトリ) と、データがテキストとして格納されていることを Hive に伝えます。
   * `SELECT` - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 この値を含む行が 3 行あるため、このステートメントでは値 **3** が返されます。

     > [!NOTE]  
     > Curl を使用したとき、HiveQL ステートメントのスペースが `+` に置き換わることに注意してください。 スペースを含む引用符で囲まれた値 (区切り記号など) は `+`に置き換わりません。

      このコマンドは、ジョブのステータスの確認に使用できるジョブ ID を返します。

5. ジョブのステータスを確認するには、次のコマンドを使用します。

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    ジョブが完了している場合、ステータスは **SUCCEEDED** です。

6. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには、出力ファイルの場所が含まれます。この場合は、`/example/rest` です。 このアドレスは、クラスターの既定ストレージに `example/curl` ディレクトリの出力を格納します。

    これらのファイルを一覧表示およびダウンロードするには [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)を使用します。 Azure Storage での Azure CLI の使用の詳細については、[Azure Storage での Azure CLI の使用](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs)に関するページを参照してください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

このドキュメントで使用されている REST API の詳細については、「[WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するドキュメントをご覧ください。

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


