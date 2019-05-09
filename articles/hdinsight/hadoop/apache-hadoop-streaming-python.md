---
title: HDInsight での Python ストリーミング MapReduce ジョブの開発 - Azure
description: ストリーミング MapReduce ジョブに Python を使用する方法について説明します。 Apache Hadoop には、MapReduce を Java 以外の言語で記述するためのストリーミング API が用意されています。
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 95a78fafcd83e6f56f26ef6670beaa179085de62
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359582"
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>HDInsight 用 Python ストリーミング MapReduce プログラムの開発

ストリーミング MapReduce 操作に Python を使用する方法について説明します。 Apache Hadoop には、Java 以外の言語で map 関数と reduce 関数を記述できる MapReduce 用のストリーミング API が用意されています。 このドキュメントの手順では、Python に Map および Reduce コンポーネントを実装します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* HDInsight クラスター上の Linux ベースの Apache Hadoop

  > [!IMPORTANT]
  > このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* テキスト エディター

  > [!IMPORTANT]
  > テキスト エディターでは、行の終わりとして LF を使用する必要があります。 行末に CRLF を使用すると、Linux ベースの HDInsight クラスターで MapReduce ジョブを実行するときにエラーが発生します。

* `ssh` および `scp` コマンド、または [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)

## <a name="word-count"></a>文字数

この例は、Python に実装された基本的な文字カウント、マッパーとレジューサです。 マッパーは文をいくつかの単語に分割し、レジューサは単語数や回数を合計して出力を生成します。

次のフローチャートは、map および reduce のフェーズで実際に何が実行されるかを示しています。

![mapreduce プロセスの図](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>MapReduce ストリーミング

Hadoop では、ジョブで使用される map および reduce のロジックを含むファイルを指定できます。 map および reduce ロジックに固有の要件は次のとおりです。

* **入力**:map および reduce コンポーネントは STDIN から入力データを読み取る必要があります。
* **出力**:map および reduce コンポーネントは STDOUT に出力データを書き込む必要があります。
* **Data format**:使用および生成されるデータは、タブ文字で区切られたキーと値のペアである必要があります。

Python では、STDIN からの読み取りに `sys` モジュールを、STDOUT への印刷に `print` を使用して、これらの要件を簡単に処理できます。 その他、キーと値の間にタブ (`\t`) 文字を使用してデータを簡単に書式設定できます。

## <a name="create-the-mapper-and-reducer"></a>マッパーとリデューサーの作成

1. `mapper.py` という名前のファイルを作成し、内容として以下のコードを使用します。

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. **reducer.py** という名前のファイルを作成し、内容として以下のコードを使用します。

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>PowerShell を使用して実行する

ファイルが右行末を持つようにするには、次の PowerShell スクリプトを使用します。

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

次の PowerShell スクリプトを使用して、ファイルをアップロードし、ジョブを実行して、出力を表示します。

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>SSH セッションからの実行

1. 開発環境から、`mapper.py` および `reducer.py` ファイルと同じディレクトリで、次のコマンドを使用します。

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    `username` は、クラスターの SSH ユーザーに置き換えます。また、`clustername` はクラスターの名前に置き換えます。

    このコマンドにより、ファイルがローカル システムからヘッド ノードにコピーされます。

    > [!NOTE]  
    > SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、 `-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。 たとえば、「 `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:` 」のように入力します。

2. SSH を使用したクラスターへの接続:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. Mapper.py および reducer.py が正しい行末を持つようにするには、次のコマンドを使用します。

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. MapReduce ジョブを開始するには次のコマンドを使用します。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    このコマンドには次のようなものがあります。

   * **hadoop-streaming.jar**:MapReduce 操作のストリーミングを実行するときに使用します。 Hadoop と指定した外部 MapReduce コードとの橋渡しを務めます。

   * **-files**:MapReduce ジョブに指定したファイルを追加します。

   * **-mapper**:マッパーとして使用するファイルを Hadoop に伝えます。

   * **-reducer**:レジューサーとして使用するファイルを Hadoop に伝えます。

   * **-input**:文字数をカウントする入力ファイル。

   * **-output**:出力の書き込み先のディレクトリ。

     MapReduce ジョブの実行に応じて、プロセスがパーセンテージで表示されます。

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. 出力を表示するには、次のコマンドを使用します。

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    このコマンドでは、文字の一覧と、その出現回数が表示されます。

## <a name="next-steps"></a>次の手順

これで、HDInsight でストリーミング MapRedcue ジョブを使用する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)
