---
title: "HDInsight クラスターでの Hadoop Pig と SSH の使用 - Azure | Microsoft Docs"
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
ms.date: 07/12/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e4c893ef4bfa573dd9fbc9c9b0ae296720769842
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Pig コマンド (SSH) を使用して Linux ベースのクラスターで Pig ジョブを実行する

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

HDInsight クラスターへの SSH 接続を使用して、Pig ジョブを対話的に実行する方法について説明します。 Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

> [!IMPORTANT]
> このドキュメントの手順では、Linux ベースの HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a id="ssh"></a>SSH を使用した接続

SSH を使用して、HDInsight クラスターに接続します。 次の例では、**myhdinsight** という名前のクラスターに **sshuser** という名前のアカウントとして接続します。

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は** 、クライアント システムの秘密キーの場所を指定する必要があることがあります。

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は**、パスワードの入力を求められます。

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="pig"></a>Pig コマンドの使用

1. 接続したら、次のコマンドを使用して Pig コマンド ライン インターフェイス (CLI) を起動します。

        pig

    少し経ってから `grunt>` プロンプトが表示されます。

2. 次のステートメントを入力します。

        LOGS = LOAD '/example/data/sample.log';

    このコマンドは、sample.log ファイルの内容をログに読み込みます。 ファイルの内容を表示するには、次のステートメントを使用します。

        DUMP LOGS;

3. 次のステートメントを使用して、正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    変換後のデータを表示するには、 **DUMP** を使用します。 例では `DUMP LEVELS;`が使用されます。

4. 次の表のステートメントを使用して、変換を適用します。

    | Pig Latin ステートメント | ステートメントの実行内容 |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | ログ レベルに null 値を含む行を削除し、結果を `FILTEREDLEVELS` に格納します。 |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | ログ レベルで列をグループ化し、結果を `GROUPEDLEVELS` に格納します。 |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | 一意のログ レベル値とそれが出現した回数を含む データ セットを作成します。 データ セットは `FREQUENCIES` に格納されます。 |
    | `RESULT = order FREQUENCIES by COUNT desc;` | 数が多い順にログ レベルを並べ替えて、`RESULT` に格納します。 |

    > [!TIP]
    > 各手順の後に `DUMP` を使用して、変換の結果を表示します。

5. 変換の結果は `STORE` ステートメントで保存することもできます。 たとえば、以下のステートメントでは `RESULT` がクラスターの既定のストレージの `/example/data/pigout` ディレクトリに保存されます。

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > データは、`part-nnnnn` という名前のファイルの指定したディレクトリに保存されます。 ディレクトリが既に存在する場合は、エラーが発生します。

6. エラーを解決するには、次のステートメントを入力します。

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin バッチ ファイル

Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

1. エラーを解決したら、次のコマンドを使用して、STDIN を `pigbatch.pig` という名前のファイルにパイプします。 このファイルは、SSH ユーザー アカウントのホーム ディレクトリに作成されます。

        cat > ~/pigbatch.pig

2. 次の行を入力または貼り付けて、Ctrl + D キーを押します。

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. 次の Pig コマンドを使用して、`pigbatch.pig` ファイルを実行します。

        pig ~/pigbatch.pig

    バッチ ジョブが完了すると、次の出力が表示されます。

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報について詳しくは、次のドキュメントを参照してください。

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop の操作について詳しくは、次のドキュメントを参照してください。

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

