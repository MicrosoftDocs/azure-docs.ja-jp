---
title: UHDInsight 上で REST を使用して Apache Hadoop Pig を使用する - Azure
description: REST を使用して、Azure HDInsight 上の Apache Hadoop クラスターで Pig Latin ジョブを実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 52ad40a2521f21efee3b9f98b46c2e2e6343b656
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098306"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>REST を使用して HDInsight 上の Apache Hadoop で Apache Pig ジョブを実行する

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Azure HDInsight クラスターに REST 要求を行うことで、Apache Pig Latin ジョブを実行する方法について説明します。 Curl は、WebHCat REST API を使用して HDInsight とやり取りする方法を示すために使用されます。

> [!NOTE]  
> Linux ベースの Apache Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、[Linux ベースの HDInsight のヒント](../hdinsight-hadoop-linux-information.md)に関する記事をご覧ください。

## <a id="prereq"></a>前提条件

* Azure HDInsight (HDInsight での Hadoop) クラスター (Linux または Windows ベース)

  > [!IMPORTANT]  
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Curl を使用した Apache Pig ジョブの実行


> [!NOTE]
> REST API のセキュリティは、 [基本アクセス認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。
>
> このセクションのコマンドを使用する場合は、`USERNAME` をクラスターに対して認証するユーザーの名前に置き換え、`PASSWORD` をユーザー アカウントのパスワードに置き換えます。 `CLUSTERNAME` をクラスターの名前に置き換えます。
>


1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    次の JSON 応答が表示されます。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-u**:要求の認証に使用するユーザー名とパスワード
   * **-G**:この要求が GET 要求であることを示します

     URL の最初の部分 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** は、すべての要求で同じです。 パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) のステータスを返すことを示します。

2. 次のコードを使用して、Pig Latin ジョブをクラスターに送信します。

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-d**:`-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

   * **user.name**: コマンドを実行するユーザー
   * **execute**:実行する Pig Latin ステートメント
   * **statusdir**:このジョブの状態が書き込まれるディレクトリ

     > [!NOTE]  
     > Curl を使用したとき、Pig Latin ステートメントのスペースが `+` に置き換わることに注意してください。

     このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。たとえば、

       {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     `JOBID` を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、`JOBID` は `job_1415651640909_0026` になります。

    ジョブが完了している場合、ステータスは **SUCCEEDED** です。

    > [!NOTE]  
    > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。

## <a id="results"></a>結果の表示

ジョブのステータスが **SUCCEEDED** に変わったら、ジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには、出力ファイルの場所が含まれます。この場合は、`/example/pigcurl` です。

HDInsight は、既定のデータ ストアとして Azure Storage または Azure Data Lake Storage のいずれかを使用します。 どちらを使用するかによって、さまざまな方法でデータを取得できます。 詳細については、[Linux ベースの HDInsight について](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage)ドキュメントのストレージの項を参照してください。

## <a id="summary"></a>概要

このドキュメントを参照すると、未加工の HTTP 要求を使用して HDInsight クラスターで Pig ジョブを実行および監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「 [WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するページをご覧ください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報:

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
