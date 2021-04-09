---
title: Apache Hadoop での MapReduce と SSH 接続 - Azure HDInsight
description: SSH を使用して、HDInsight 上の Apache Hadoop を使用する MapReduce ジョブを実行する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939632"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>HDInsight 上の Apache Hadoop で MapReduce と SSH を使用する

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Secure Shell (SSH) 接続から HDInsight に MapReduce ジョブを送信する方法について説明します。

> [!NOTE]
> Linux ベースの Apache Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「[Linux での HDInsight の使用方法](../hdinsight-hadoop-linux-information.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

HDInsight の Apache Hadoop クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

## <a name="use-hadoop-commands"></a>Hadoop コマンドの使用

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. HDInsight クラスターに接続されたら、以下のコマンドを使用して MapReduce ジョブを起動します。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    このコマンドは、`hadoop-mapreduce-examples.jar` ファイルに含まれている `wordcount` クラスを起動します。 入力として `/example/data/gutenberg/davinci.txt` ドキュメントを使用し、出力を `/example/data/WordCountOutput` に格納します。

    > [!NOTE]
    > この MapReduce ジョブとサンプル データの詳細については、「[HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)」をご覧ください。

    処理中に詳細が生成され、ジョブが完了すると、次のテキストのような情報が返されます。

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. ジョブが完了したら、次のコマンドを使用して出力ファイルを表示します。

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    このコマンドは、`_SUCCESS` と `part-r-00000` の 2 つのファイルを表示します。 `part-r-00000` ファイルには、このジョブの出力が含まれています。

    > [!NOTE]  
    > 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。 このとき、ファイルの順番を特定するには ##### サフィックスを使用します。

1. 出力を表示するには、次のコマンドを使用します。

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    このコマンドでは、**wasbs://example/data/gutenberg/davinci.txt** ファイルに含まれる文字の一覧と各文字の出現回数が表示されます。 ファイルに含まれるデータの例を次のテキストに示します。

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>次のステップ

このように、Hadoop コマンドを使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。 HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)
* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
