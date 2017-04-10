---
title: "HDInsight での Hadoop の MapReduce とリモート デスクトップの使用 | Microsoft Docs"
description: "リモート デスクトップを使用して、HDInsight で Hadoop に接続し、MapReduce ジョブを実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: dc8e9647d99b39cdee36ec11e144452326e2d968
ms.openlocfilehash: 99a3e44730737a9e1b3db8eead6dfe181382962b
ms.lasthandoff: 01/18/2017


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>リモート デスクトップによる HDInsight での MapReduce と Hadoop の使用
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、リモート デスクトップを使用して HDInsight クラスター上の Hadoop に接続し、Hive コマンドを使用して MapReduce ジョブを実行する方法について説明します。

> [!IMPORTANT]
> リモート デスクトップは、Windows ベースの HDInsight クラスターでのみ使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。
>
> HDInsight 3.4 以上での HDInsight クラスターへの接続と MapReduce ジョブの実行に関する情報については、[SSH による MapReduce の使用](hdinsight-hadoop-use-mapreduce-ssh.md)に関するページを参照してください。

## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* Windows ベースの HDInsight (HDInsight で Hadoop を使用) クラスター
* Windows 10、Windows 8、Windows 7 を実行するクライアント コンピューター

## <a id="connect"></a>リモート デスクトップへの接続
「 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

## <a id="hadoop"></a>Hadoop コマンドの使用
HDInsight クラスターのデスクトップに接続したら、次の手順に従って Hadoop コマンドを使用して MapReduce ジョブを実行します。

1. HDInsight デスクトップから、 **Hadoop コマンド ライン**を起動します。 これにより、**c:\apps\dist\hadoop-&lt;version number>** ディレクトリに新しいコマンド プロンプトが開きます。

   > [!NOTE]
   > Hadoop の更新に応じて、バージョン番号が変わります。 **HADOOP_HOME** 環境変数を使用して、パスを探します。 たとえば、 `cd %HADOOP_HOME%` では、バージョン番号がわからなくても、ディレクトリは Hadoop ディレクトリに変更されます。
   >
   >
2. **Hadoop** コマンドを使用してサンプルの MapReduce ジョブ実行するには、次のコマンドを使用します。

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    これは、現在のディレクトリの **hadoop-mapreduce-examples.jar** ファイルに含まれる **wordcount** クラスを起動します。 入力として **wasbs://example/data/gutenberg/davinci.txt** ドキュメントを使用し、出力は **wasbs:///example/data/WordCountOutput** に格納されます。

   > [!NOTE]
   > この MapReduce ジョブとサンプル データの詳細については、「<a href="hdinsight-use-mapreduce.md">HDInsight での Hadoop MapReduce の使用</a>」をご覧ください。
   >
   >
3. 処理中に詳細が生成され、ジョブが完了すると、次のような情報が返されます。

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. ジョブが完了したら、次のコマンドを使用して、**wasbs://example/data/WordCountOutput** に格納された出力ファイルを一覧表示します。

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    ここでは、**_SUCCESS** と **part-r-00000** の&2; つのファイルが表示されます。 **part-r-00000** ファイルには、このジョブの出力が含まれています。

   > [!NOTE]
   > 一部の MapReduce ジョブでは、複数の **part-r-#####** ファイルに結果が分割される場合があります。 このとき、ファイルの順番を特定するには ##### サフィックスを使用します。
   >
   >
5. 出力を表示するには、次のコマンドを使用します。

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    **wasbs://example/data/gutenberg/davinci.txt** ファイルに含まれる文字の一覧と、各文字の出現回数が表示されます。 ファイルに含まれるデータの例を次に示します。

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>概要
このように、Hadoop を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ出力を表示できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

