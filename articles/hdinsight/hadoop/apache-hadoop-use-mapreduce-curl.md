---
title: HDInsight 上の Apache Hadoop で MapReduce と Curl を使用する - Azure
description: HDInsight 上の Apache Hadoop で Curl を使用して MapReduce ジョブをリモートで実行する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939692"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>HDInsight 上の Apache Hadoop で REST を使用して MapReduce ジョブを実行する

HDInsight クラスター上の Apache Hadoop で Apache Hive WebHCat REST API を使用して MapReduce ジョブを実行する方法を説明します。 Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、MapReduce ジョブを実行する方法を示すために使用します。

> [!NOTE]  
> Linux ベースの Hadoop サーバーは使い慣れているが、HDInsight は初めてという場合は、[Linux ベースの HDInsight の Apache Hadoop について知っておくべきこと](../hdinsight-hadoop-linux-information.md)を説明しているドキュメントをご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

次のどちらかの操作を行います。
  * Windows PowerShell
  * [Curl](https://curl.haxx.se/) と [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>MapReduce ジョブを実行する

> [!NOTE]  
> Curl、または WebHCat を使用したその他の REST 通信が使用できる場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 サーバーへの要求の送信に使用する URI にクラスター名を含める必要があります。
>
> REST API のセキュリティは、 [基本アクセス認証](https://en.wikipedia.org/wiki/Basic_access_authentication)の使用によって保護されています。 資格情報を安全にサーバーに送信するには、必ず HTTPS を使用して要求を行う必要があります。

### <a name="curl"></a>Curl

1. 使いやすくするために、以下の変数を設定します。 この例は、Windows 環境に基づいています。必要に応じて、お使いの環境に合わせて変更してください。

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-u**: 要求の認証に使用するユーザー名とパスワードを示します。
   * **-G**: この操作が GET 要求であることを示します。

   URI の最初の部分 `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` は、すべての要求で同じです。

    次の JSON のような応答を受け取ります。

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce ジョブを送信するには、次のコマンドを使用します。 必要に応じて **jq** へのパスを変更します。

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    URI の末尾 (/mapreduce/jar) により、この要求では jar ファイルのクラスから MapReduce ジョブが起動されることが WebHCat に通知されます。 このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-d**: `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。
     * **user.name**: コマンドを実行するユーザー
     * **jar**: 実行されるクラスを含む jar ファイルの場所
     * **class**: MapReduce ロジックを含むクラス
     * **arg**: MapReduce ジョブに渡される引数。 この場合、入力テキスト ファイルと出力に使用するディレクトリです

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します: `job_1415651640909_0026`。

1. ジョブのステータスを確認するには、次のコマンドを使用します。 `JOBID` の値を、前の手順で返された **実際の** 値に置き換えます。 必要に応じて **jq** の場所を変更します。

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. 使いやすくするために、以下の変数を設定します。 `CLUSTERNAME` を実際のクラスター名に置き換えます。 コマンドを実行し、入力を求められたらクラスター ログイン パスワードを入力します。

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. 次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    次の JSON のような応答を受け取ります。

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce ジョブを送信するには、次のコマンドを使用します。

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    URI の末尾 (/mapreduce/jar) により、この要求では jar ファイルのクラスから MapReduce ジョブが起動されることが WebHCat に通知されます。 このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **user.name**: コマンドを実行するユーザー
    * **jar**: 実行されるクラスを含む jar ファイルの場所
    * **class**: MapReduce ロジックを含むクラス
    * **arg**: MapReduce ジョブに渡される引数。 この場合、入力テキスト ファイルと出力に使用するディレクトリです

   このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します: `job_1415651640909_0026`。

1. ジョブのステータスを確認するには、次のコマンドを使用します。

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

### <a name="both-methods"></a>両方の方法

1. ジョブが完了している場合、返される状態は `SUCCEEDED` です。

1. ジョブの状態が `SUCCEEDED` に変化したら、Azure Blob ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには出力ファイルの場所を含めます。 この例では、場所は `/example/curl` です。 このアドレスは、`/example/curl` にあるクラスターの既定ストレージにジョブの出力を格納します。

これらのファイルを一覧表示およびダウンロードするには [Azure CLI](/cli/azure/install-azure-cli)を使用します。 Azure CLI を使用して Azure Blob Storage を操作する方法の詳細については、[クイック スタート:Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](../../storage/blobs/storage-quickstart-blobs-cli.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

この記事で使用されている REST インターフェイスの詳細については、「 [WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するページをご覧ください。
