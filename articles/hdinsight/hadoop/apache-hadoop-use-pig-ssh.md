---
title: HDInsight クラスターで Apache Pig と SSH を使用する - Azure
description: SSH で Linux ベースの Apache Hadoop クラスターに接続し、Pig コマンドを使用して Pig Latin ステートメントを対話的に実行するか、バッチ ジョブとして実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a674ee52ae5e8f8f800d4584a53c808ceae70156
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435057"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Pig コマンド (SSH) を使用して Linux ベースのクラスターで Apache Pig ジョブを実行する

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

HDInsight クラスターへの SSH 接続から、Apache Pig ジョブを対話的に実行する方法について説明します。 Pig Latin プログラミング言語では、入力データに適用される変換を記述し、目的の出力を生成することができます。

> [!IMPORTANT]  
> このドキュメントの手順では、Linux ベースの HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a id="ssh"></a>SSH を使用した接続

SSH を使用して、HDInsight クラスターに接続します。 次の例では、**myhdinsight** という名前のクラスターに **sshuser** という名前のアカウントとして接続します。

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a id="pig"></a>Apache Pig コマンドの使用

1. 接続したら、次のコマンドを使用して Pig コマンド ライン インターフェイス (CLI) を起動します。

    ```bash
    pig
    ```

    しばらくすると、プロンプトが `grunt>` に変わります。

2. 次のステートメントを入力します。

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    このコマンドは、sample.log ファイルの内容をログに読み込みます。 ファイルの内容を表示するには、次のステートメントを使用します。

    ```piglatin
    DUMP LOGS;
    ```

3. 次のステートメントを使用して、正規表現を適用してデータを変換し、各レコードのログ レベルのみを抽出します。

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

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

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > データは、`part-nnnnn` という名前のファイルの指定したディレクトリに保存されます。 ディレクトリが既に存在する場合は、エラーが発生します。

6. エラーを解決するには、次のステートメントを入力します。

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin バッチ ファイル

Pig コマンドを使用して、ファイルに含まれた Pig Latin を実行することもできます。

1. エラーを解決したら、次のコマンドを使用して、`pigbatch.pig` という名前のファイルを作成します。

    ```bash
    nano ~/pigbatch.pig
    ```

2. 次の行を入力するか貼り付けます。

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    完了したら、__Ctrl__ + __X__、__Y__ キーを使用し、__Enter__ を使用してファイルを保存します。

3. 次の Pig コマンドを使用して、`pigbatch.pig` ファイルを実行します。

    ```bash
    pig ~/pigbatch.pig
    ```

    バッチ ジョブが完了すると、次の出力が表示されます。

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報について詳しくは、次のドキュメントを参照してください。

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)

HDInsight での Hadoop の操作について詳しくは、次のドキュメントを参照してください。

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
