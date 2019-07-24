---
title: HDInsight 上の Apache Hadoop で使用する MapReduce と SSH 接続 - Azure
description: SSH を使用して、HDInsight 上の Apache Hadoop を使用する MapReduce ジョブを実行する方法を説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: bce79aed49b94071b4e83524de2d599fca182256
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438573"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>HDInsight 上の Apache Hadoop で MapReduce と SSH を使用する

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Secure Shell (SSH) 接続から HDInsight に MapReduce ジョブを送信する方法について説明します。

> [!NOTE]
> Linux ベースの Apache Hadoop サーバーは使い慣れているが HDInsight は初めてという場合は、「[Linux での HDInsight の使用方法](../hdinsight-hadoop-linux-information.md)」をご覧ください。

## <a id="prereq"></a>前提条件

* Linux ベースの HDInsight (HDInsight で Hadoop を使用) クラスター

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* SSH クライアント 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a id="ssh"></a>SSH を使用した接続

SSH を使用してクラスターに接続します。 たとえば、次のコマンドは **myhdinsight** という名前のクラスターに **sshuser** アカウントとして接続します。

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

**SSH 認証に証明書キーを使用する場合**、クライアント システムの秘密キーの場所を指定する必要があることがあります。たとえば、以下のようにします。

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.net
```

**SSH 認証にパスワードを使用する場合**、メッセージが表示されたら、パスワードを入力する必要があります。

SSH の使用方法の詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="hadoop"></a>Hadoop コマンドの使用

1. HDInsight クラスターに接続されたら、以下のコマンドを使用して MapReduce ジョブを起動します。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    このコマンドは、`hadoop-mapreduce-examples.jar` ファイルに含まれている `wordcount` クラスを起動します。 入力として `/example/data/gutenberg/davinci.txt` ドキュメントを使用し、出力を `/example/data/WordCountOutput` に格納します。

    > [!NOTE]
    > この MapReduce ジョブとサンプル データの詳細については、「[HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)」をご覧ください。

2. 処理中に詳細が生成され、ジョブが完了すると、次のテキストのような情報が返されます。

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. ジョブが完了したら、次のコマンドを使用して出力ファイルを表示します。

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    このコマンドは、`_SUCCESS` と `part-r-00000` の 2 つのファイルを表示します。 `part-r-00000` ファイルには、このジョブの出力が含まれています。

    > [!NOTE]  
    > 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。 このとき、ファイルの順番を特定するには ##### サフィックスを使用します。

4. 出力を表示するには、次のコマンドを使用します。

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    このコマンドでは、**wasb://example/data/gutenberg/davinci.txt** ファイルに含まれる文字の一覧と各文字の出現回数が表示されます。 ファイルに含まれるデータの例を次のテキストに示します。

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>概要

このように、Hadoop コマンドを使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
