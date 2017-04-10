---
title: "HDInsight での Hadoop Pig と Curl の使用 | Microsoft Docs"
description: "Curl を使用して Azure HDInsight の Hadoop クラスターで Pig Latin ジョブを実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: e7489b0485fe4779a72daeef0467a244bb55ff65
ms.lasthandoff: 03/18/2017


---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Curl を使用した HDInsight の Hadoop での Pig ジョブの実行

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Curl を使用して Azure HDInsight クラスターで Pig Latin ジョブを実行する方法を説明します。 Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

Curl は、未加工の HTTP 要求を使用して HDInsight とやり取りし、Pig ジョブを実行、監視して、その結果を取得する方法を示すために使用します。 これは、HDInsight クラスターで提供される WebHCat REST API (旧称: Templeton) を使用することで機能します。

> [!NOTE]
> Linux ベースの Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「 [Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」をご覧ください。

## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Linux または Windows ベース)

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Curl を使用した Pig ジョブの実行

> [!NOTE]
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスターの管理者ユーザー名およびパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
>
> このセクションのコマンドでは、 **USERNAME** をクラスターに対して認証するユーザーの名前に置き換え、 **PASSWORD** をユーザー アカウントのパスワードに置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
>
> REST API のセキュリティは、 [基本アクセス認証](http://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    次のような応答を受け取ります。

        {"status":"ok","version":"v1"}

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-u**: 要求の認証に使用するユーザー名とパスワード
    * **-G**: GET 要求であることを示します。

     URL の先頭は **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** で、これはすべての要求で共通です。 パス **/status** は、要求がサーバー用の WebHCat (別名: Templeton) のステータスを返すことを示します。

2. 次のコードを使用して、Pig Latin ジョブをクラスターに送信します。

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    このコマンドで使用されるパラメーターの意味は次のとおりです。

    * **-d**: `-G` が使用されていないため、要求は既定で POST メソッドになります。 `-d` は要求で送信されるデータ値を指定します。

    * **user.name**: コマンドを実行するユーザー
    * **execute**: 実行する Pig Latin ステートメント
    * **statusdir**: ジョブのステータスが書き込まれるディレクトリ

    > [!NOTE]
    > Curl を使用したとき、Pig Latin ステートメントのスペースが `+` に置き換わることに注意してください。

    このコマンドは、ジョブのステータスの確認に使用できる ジョブ ID を返します。たとえば、

        {"id":"job_1415651640909_0026"}

3. ジョブのステータスを確認するには、次のコマンドを使用します。 **JOBID** を前の手順で返された値に置き換えます。 たとえば、戻り値が `{"id":"job_1415651640909_0026"}` の場合、**JOBID** は `job_1415651640909_0026` になります。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    ジョブが完了している場合、ステータスは **SUCCEEDED**になります。

    > [!NOTE]
    > この Curl 要求では、ジョブに関する情報が記載された JavaScript Object Notation (JSON) ドキュメントが返されます。状態値のみを取得するには jq を使用します。

## <a id="results"></a>結果の表示

ジョブのステータスが **SUCCEEDED** に変わったら、クラスターが使用する既定のストレージからジョブの結果を取得できます。 クエリで渡される `statusdir` パラメーターには出力ファイルの場所が含まれます。この場合は、**/example/pigcurl** になります。

HDInsight のバッキング ストアは Azure Storage または Azure Data Lake Store のいずれかにでき、このいずれを使用するかによってデータにアクセスする方法は異なります。 Azure Storage および Azure Data Lake Store の使用方法について詳しくは、Linux ドキュメントの HDInsight の「[HDFS、Blob Storage、および Data Lake Store](hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store)」をご覧ください。

## <a id="summary"></a>概要

このドキュメントを参照すると、未加工の HTTP 要求を使用して HDInsight クラスターで Pig ジョブを実行および監視し、その結果を表示できます。

この記事で使用されている REST インターフェイスの詳細については、「 [WebHCat リファレンス](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)」に関するページをご覧ください。

## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報:

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

