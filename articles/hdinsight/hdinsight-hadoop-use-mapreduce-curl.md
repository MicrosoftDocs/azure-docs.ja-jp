---
title: "HDInsight での MapReduce と Hadoop の Curl の使用 - Azure | Microsoft Docs"
description: "Curl を使用して HDInsight の Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/12/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 8238bb829df95dcb8c99c0b7fff53c627a56f47c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>REST を使用して HDInsight の Hadoop で MapReduce ジョブを実行

WebHCat REST API を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。 Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、MapReduce ジョブを実行する方法を示すために使用します。

> [!NOTE]
> Linux ベースの Hadoop サーバーは使い慣れているが、HDInsight は初めてという場合は、「[Linux ベースの HDInsight の Hadoop について知っておくべきこと](hdinsight-hadoop-linux-information.md)」というドキュメントをご覧ください。


## <a id="prereq"></a>前提条件

* HDInsight クラスター上の Hadoop
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Curl を使用した MapReduce ジョブの実行

> [!NOTE]
> Curl、または WebHCat を使用したその他の REST 通信が使用できる場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 サーバーへの要求の送信に使用する URI にクラスター名を含める必要があります。
>
> このセクションのコマンドでは、**USERNAME** をクラスターを認証するユーザーの名前に、**PASSWORD** をユーザー アカウントのパスワードに置き換えてください。 **CLUSTERNAME** をクラスターの名前に置き換えます。
>
> REST API のセキュリティは、 [基本アクセス認証](http://en.wikipedia.org/wiki/Basic_access_authentication)の使用によって保護されています。 資格情報を安全にサーバーに送信するには、必ず HTTPS を使用して要求を行う必要があります。


1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    次の JSON のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-u**: 要求の認証に使用するユーザー名とパスワード
   * **-G**: この操作が GET 要求であることを示します

     URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。

2. MapReduce ジョブを送信するには、次のコマンドを使用します。

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    URI の末尾 (/mapreduce/jar) により、この要求では jar ファイルのクラスから MapReduce ジョブが起動されることが WebHCat に通知されます。 このコマンドで使用されるパラメーターの意味は次のとおりです。

   * **-d**: `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。
    * **user.name**: コマンドを実行するユーザー
    * **jar**: 実行されるクラスを含む jar ファイルの場所
    * **class**: MapReduce ロジックを含むクラス
    * **arg**: MapReduce ジョブに渡される引数。 この場合、入力テキスト ファイルと出力に使用するディレクトリです

     このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。

       {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、JOBID は `job_1415651640909_0026` になります。

    ジョブが完了している場合、返される状態は `SUCCEEDED` です。

   > [!NOTE]
   > この Curl 要求では、ジョブに関する情報が記載された JSON ドキュメントが返されます。 Jq は、状態値のみを取得するために使用されます。

4. ジョブの状態が `SUCCEEDED` に変化したら、Azure Blob ストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには出力ファイルの場所を含めます。 この例では、場所は `/example/curl` です。 このアドレスは、`/example/curl` にあるクラスターの既定ストレージにジョブの出力を格納します。

これらのファイルを一覧表示およびダウンロードするには [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用します。 Azure CLI から Blob を操作することの詳細については、「[Azure Storage での Azure CLI 2.0 の使用](../storage/common/storage-azure-cli.md#create-and-manage-blobs)」というドキュメントを参照してください。

## <a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

この記事で使用されている REST インターフェイスの詳細については、「 [WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するページをご覧ください。
