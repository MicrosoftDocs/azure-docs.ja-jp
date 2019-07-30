---
title: HDInsight 上で Curl を使用して Apache Hadoop Hive を使用する - Azure
description: Curl を使用して Apache Pig ジョブを HDInsight にリモートで送信する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508128"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>HDInsight 上の Apache Hadoop で REST を使用して Apache Hive クエリを実行する

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

WebHCat REST API を使用して Azure HDInsight クラスター上の Apache Hadoop で Apache Hive クエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* REST クライアント。 このドキュメントでは、Windows PowerShell の [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) と [Bash](https://docs.microsoft.com/windows/wsl/install-win10) の [Curl](https://curl.haxx.se/) を使用します。

* Bash を使用する場合、コマンド ライン JSON プロセッサの jq も必要になります。  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) をご覧ください。

## <a name="base-uri-for-rest-api"></a>Rest API のベース URI

HDInsight の REST API のベース URI (Uniform Resource Identifier) は、`https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` です。`CLUSTERNAME` は実際のクラスターの名前です。  URI のクラスター名では、**大文字と小文字が区別**されます。  URI (`CLUSTERNAME.azurehdinsight.net`) の FQDN (完全修飾ドメイン名) 部分のクラスター名では大文字と小文字が区別されませんが、URI の他の部分で出現するときは大文字と小文字が区別されます。

## <a name="authentication"></a>Authentication

cURL、または WebHCat を用いたその他 REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 REST API のセキュリティは、 [基本認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

### <a name="setup-preserve-credentials"></a>セットアップ (資格情報の保存)
各例で再入力しなくて済むように、資格情報を保存します。  クラスター名は別の手順で保存します。

**A.Bash**  
`PASSWORD` を実際のパスワードに置き換えて、次のスクリプトを編集します。  その後、コマンドを入力します。

```bash
export password='PASSWORD'
```  

**B.PowerShell** 以下のコードを実行し、ポップアップ ウィンドウで資格情報を入力します。

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>大文字と小文字が正しく区別されたクラスター名を確認する
クラスターの作成方法によっては、クラスター名の実際の大文字小文字の区別が予想と異なる場合があります。  ここでの手順では、実際の大文字小文字の区別を示し、以降のすべての例で使用できるように、それを変数に格納します。

次のスクリプトを編集して、`CLUSTERNAME` を実際のクラスター名に置き換えます。 その後、コマンドを入力します。 (FQDN のクラスター名では、大文字と小文字は区別されません)。

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a id="curl"></a>Hive クエリを実行する

1. HDInsight クラスターに接続できることを確認するには、次のいずれかのコマンドを使用します。

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
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

1. URL の最初の部分 `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` は、すべての要求で同じです。 パス `/status` は、要求がサーバー用の WebHCat (別名: Templeton) の状態を返すことを示します。 次のコマンドを使用して、Hive のバージョンを要求することもできます。

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    この要求では、次のような応答が返されます。

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. 次のコマンドを使用して、 **log4jLogs** という名前のテーブルを作成します。

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
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

1. ジョブのステータスを確認するには、次のコマンドを使用します。

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

1. ジョブのステータスが **SUCCEEDED** に変わったら、Azure BLOB ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには、出力ファイルの場所が含まれます。この場合は、`/example/rest` です。 このアドレスは、クラスターの既定ストレージに `example/curl` ディレクトリの出力を格納します。

    これらのファイルを一覧表示およびダウンロードするには [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)を使用します。 Azure Storage での Azure CLI の使用の詳細については、[Azure Storage での Azure CLI の使用](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs)に関するページを参照してください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

このドキュメントで使用されている REST API の詳細については、「[WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するドキュメントをご覧ください。