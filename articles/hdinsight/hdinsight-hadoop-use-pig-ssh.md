---
title: "HDInsight クラスターでの Hadoop Pig と SSH の使用 | Microsoft Docs"
description: "SSH で Linux ベースの Hadoop クラスターに接続し、Pig コマンドを使用して Pig Latin ステートメントを対話的に実行するか、バッチ ジョブとして実行する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 4fe50acbbf9424275c5746b3bdabc79b08b027d3
ms.lasthandoff: 03/25/2017


---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Pig コマンド (SSH) を使用して Linux ベースのクラスターで Pig ジョブを実行する
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Secure Shell (SSH) を使用して Linux ベースの Azure HDInsight クラスターに接続し、Pig コマンドを使用して Pig Latin ステートメントを対話的にまたはバッチ ジョブとして実行する方法を順を追って説明します。

Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

> [!NOTE]
> Linux ベースの Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「 [Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)」をご覧ください。


## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* Linux ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* SSH クライアント SSH クライアントを備えた Linux、Unix、および Mac OS Windows ユーザーは [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのクライアントをダウンロードする必要があります。

## <a id="ssh"></a>SSH を使用した接続
SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。 FQDN はクラスターに指定した名前で、その後、 **.azurehdinsight.net**が続きます。 以下の例では、 **myhdinsight**という名前のクラスターに接続します。

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は** 、クライアント システムの秘密キーの場所を指定する必要があることがあります。

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は** 、パスワードの入力を求められます。

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="pig"></a>Pig コマンドの使用
1. 接続したら、次のコマンドを使用して Pig コマンド ライン インターフェイス (CLI) を起動します。
   
        pig
   
    少し経ってから `grunt>` プロンプトが表示されます。
2. 次のステートメントを入力します。
   
        LOGS = LOAD 'wasbs:///example/data/sample.log';
   
    このコマンドは、sample.log ファイルの内容をログに読み込みます。 ファイルの内容を表示するには、次のコマンドを使用します。
   
        DUMP LOGS;
3. 次のコマンドを使用して、正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。
   
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
   
    変換後のデータを表示するには、 **DUMP** を使用します。 例では `DUMP LEVELS;`が使用されます。
4. 次のステートメントを使用して、変換を適用します。 各手順の後に `DUMP` を使用して、変換の結果を表示します。
   
    <table>
    <tr>
    <th>ステートメント</th><th>実行内容</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>ログ レベルに null 値を含む行を削除し、結果を FILTEREDLEVELS に格納します。</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>ログ レベルで列をグループ化し、結果を GROUPEDLEVELS に格納します。</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>一意のログ レベル値とそれが出現した回数を含む新しい データ セットを作成します。 これは FREQUENCIES に格納されます。</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>数が多い順にログ レベルを並べ替えて、RESULT に格納します。</td>
    </tr>
    </table>
5. 変換の結果は `STORE` ステートメントで保存することもできます。 たとえば、以下では `RESULT` がクラスターの既定のストレージ コンテナーの **/example/data/pigout** ディレクトリに保存されます。
   
        STORE RESULT into 'wasbs:///example/data/pigout';
   
   > [!NOTE]
   > データは、 **part-nnnnn**という名前のファイルの指定したディレクトリに保存されます。 ディレクトリが既に存在する場合は、エラーが発生します。
   > 
   > 
6. エラーを解決するには、次のステートメントを入力します。
   
        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin バッチ ファイル
Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

1. エラーを解決したら、次のコマンドを使用して、STDIN を **pigbatch.pig**という名前のファイルにパイプします。 このファイルは、SSH セッションでログインしているアカウントのホーム ディレクトリに作成されます。
   
        cat > ~/pigbatch.pig
2. 次の行を入力または貼り付けて、Ctrl + D キーを押します。
   
        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. 次の Pig コマンドを使用して、 **pigbatch.pig** ファイルを実行します。
   
        pig ~/pigbatch.pig
   
    バッチ ジョブが完了したら、次の出力が表示されます (前の手順の `DUMP RESULT;` での出力と同じ内容です)。
   
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>概要
このように、Pig コマンドでは、Pig Latin を使用して MapReduce 操作を対話的に実行できるだけでなく、バッチ ファイルに格納されたステートメントも実行できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)


