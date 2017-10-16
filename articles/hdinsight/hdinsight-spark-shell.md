---
title: "Azure HDInsight で対話型の Spark シェルを使用する | Microsoft Docs"
description: "対話型の Spark シェルは、一度に 1 つずつ Spark コマンドを実行し、結果を表示するための read-execute-print プロセスを提供します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: maxluk
ms.openlocfilehash: 48c6a24f6c820eaf09da6fa3a0a496ddbef1e7a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="run-spark-from-the-spark-shell"></a>Spark Shell から Spark を実行する

対話型の Spark シェルは、一度に 1 つずつ Spark コマンドを実行し、結果を表示する REPL (read-execute-print loop) 環境を提供します。 このプロセスは、開発およびデバッグに役立ちます。 Spark は、そのサポートされている言語 (Scala、Python、R) ごとに 1 つのシェルを提供します。

## <a name="get-to-a-spark-shell-with-ssh"></a>SSH を使用した Spark シェルへのアクセス

SSH を使ってクラスターのプライマリ ヘッド ノードに接続して、HDInsight の Spark シェルにアクセスします。

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

お使いのクラスター用の完全な SSH コマンドは Azure Portal から取得できます。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. HDInsight Spark クラスターのウィンドウに移動します。
3. [Secure Shell (SSH)] を選択します。

    ![Azure Portal の [HDInsight] ウィンドウ](./media/hdinsight-spark-shell/hdinsight-spark-blade.png)

4. 表示された SSH コマンドをコピーして、ご使用のターミナルで実行します。

    ![Azure Portal の [HDInsight SSH] ウィンドウ](./media/hdinsight-spark-shell/hdinsight-spark-ssh-blade.png)

SSH を使って HDInsight に接続する方法については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a name="run-a-spark-shell"></a>Spark シェルを実行する

Spark には、Scala (spark-shell)、Python (pyspark)、および R (sparkR) 向けのシェルが用意されています。 HDInsight クラスターのヘッド ノードの SSH セッションで、次のいずれかのコマンドを入力します。

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

これで適切な言語で Spark コマンドを入力できるようになります。

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession インスタンスと SparkContext インスタンス

既定では、Spark シェルを実行すると、SparkSession および SparkContext のインスタンスが自動的にインスタンス化されます。

SparkSession インスタンスにアクセスするには、「`spark`」と入力します。 SparkContext インスタンスにアクセスするには、「`sc`」と入力します。

## <a name="important-shell-parameters"></a>重要なシェル パラメーター

Spark シェル コマンド (`spark-shell`、`pyspark`、または `sparkR`) は、数多くのコマンドライン パラメーターに対応しています。 パラメーターの完全な一覧を表示するには、スイッチ `--help` を使用して Spark シェルを起動します。 これらのパラメーターの一部は `spark-submit` のみに適用される場合があります (これは、Spark シェルによってラップされます)。

| スイッチ | description | 例 |
| --- | --- | --- |
| --master MASTER_URL | マスター URL を指定します。 HDInsight では、この値は常に `yarn` です。 | `--master yarn`|
| --jars JAR_LIST | ドライバーと Executor のクラスパスに含めるローカル jar のコンマ区切りリスト。 HDInsight では、この一覧は Azure Storage または Data Lake Store の既定のファイルシステムへのパスで構成されます。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | ドライバーと Executor のクラスパスに含める jar の maven コーディネートのコンマ区切りリスト。 ローカルの maven リポジトリ、maven セントラル、`--repositories` で指定された追加のリモート リポジトリの順に検索します。 コーディネートの形式は、*groupId*:*artifactId*:*version* です。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Python のみについて、PYTHONPATH に配置する .zip、.egg、.py ファイルのコンマ区切りリストです。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>次のステップ

- 「[Azure HDInsight での Spark の概要](hdinsight-apache-spark-overview.md)」で概要を確認します。
- Spark クラスターや SparkSQL の操作方法について、「[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)」を確認します。
- Spark を使用してストリーミング データを処理するアプリケーションの作成方法について、[Spark 構造化ストリーミング](hdinsight-spark-streaming-overview.md)に関する記事を参照します。

