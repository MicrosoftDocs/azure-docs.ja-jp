---
title: "HDInsight での Hadoop Pig とリモート デスクトップの使用 | Microsoft Docs"
description: "Pig コマンドを使用して、HDInsight の Windows ベースの Hadoop のクラスターへのリモート デスクトップ接続から Pig Latin ステートメントを実行する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 3cf91abf83359f2157d0f8cd53e0b450bfd58d80
ms.lasthandoff: 02/17/2017


---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>リモート デスクトップ接続から Pig ジョブを実行する
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Pig コマンドを使用して、Windows ベースの HDInsight クラスターへのリモート デスクトップ接続から Pig Latin ステートメントを実行するチュートリアルを提供します。 Pig Latin では、map 関数や reduce 関数ではなく、データ変換を記述することで MapReduce アプリケーションを作成できます。

> [!IMPORTANT]
> リモート デスクトップは、オペレーティング システムとして Windows を使用する HDInsight クラスターでのみ使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。
>
> HDInsight 3.4 以上でのクラスターでのコマンド ラインからの直接的な Pig ジョブの実行に関する情報については、[HDInsight と SSH での Pig の使用](hdinsight-hadoop-use-pig-ssh.md)に関するページを参照してください。

## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター
* Windows 10、Windows 8、Windows 7 を実行するクライアント コンピューター

## <a id="connect"></a>リモート デスクトップへの接続
「 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

## <a id="pig"></a>Pig コマンドの使用
1. リモート デスクトップに接続したら、デスクトップ上のアイコンを使用して **Hadoop コマンド ライン** を開始します。
2. Pig コマンドを開始するには次のコマンドを使用します。

        %pig_home%\bin\pig

    `grunt>` プロンプトが表示されます。
3. 次のステートメントを入力します。

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    このコマンドは、sample.log ファイルの内容をログ ファイルに読み込みます。 ファイルの内容を表示するには、次のコマンドを使用します。

        DUMP LOGS;
4. 正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    変換後のデータを表示するには、 **DUMP** を使用します。 例では、 `DUMP LEVELS;`が使用されます。
5. 次のステートメントを使用して、変換を適用します。 各手順の後に `DUMP` を使用して、変換の結果を表示します。

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
6. 変換の結果は `STORE` ステートメントで保存することもできます。 たとえば、次のコマンドでは `RESULT` がクラスターの既定のストレージ コンテナーの **/example/data/pigout** ディレクトリに保存されます。

        STORE RESULT into 'wasbs:///example/data/pigout'

   > [!NOTE]
   > データは、 **part-nnnnn**という名前のファイルの指定したディレクトリに保存されます。 ディレクトリが既に存在する場合は、エラー メッセージが表示します。
   >
   >
7. エラーを解決するには、次のステートメントを入力します。

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin バッチ ファイル
Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

1. エラーを解決したら、**メモ帳**を開き、**%PIG_HOME%** ディレクトリに **pigbatch.pig** という名前の新しいファイルを作成します。
2. 次の行を **pigbatch.pig** ファイルに入力するか貼り付けて、保存します。

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. 次の pig コマンドを使用して、 **pigbatch.pig** ファイルを実行します。

        pig %PIG_HOME%\pigbatch.pig

    バッチ ジョブが完了すると、次の出力が表示されます (前の手順の `DUMP RESULT;` での出力と同じ内容です)。

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>概要
このように、Pig コマンドでは、MapReduce 操作を対話的に実行できるだけでなく、バッチ ファイルに格納された Pig Latin ジョブも実行できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での Pig に関する全般的な情報:

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

