---
title: "Azure HDInsight での対話型クエリの使用 | Microsoft Docs"
description: "HDInsight での対話型クエリ (Hive LLAP) の使用方法について説明します。"
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: jgao
ms.openlocfilehash: 822c5f2302fb32d0d5a05ba69132282c050b7b8b
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2017
---
# <a name="use-interactive-query-with-hdinsight"></a>HDInsight での対話型クエリの使用
対話型クエリ (別名 Hive LLAP または [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) は、Azure HDInsight の[クラスターの種類](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)です。 対話型クエリではインメモリ キャッシュがサポートされるため、Hive クエリの速度と対話性が向上します。 

対話型クエリ クラスターは、Hadoop クラスターとは異なり、 Hive サービスのみが含まれます。 

> [!NOTE]
> 対話型クエリ クラスターの Hive サービスには、Ambari Hive View、Beeline、および Microsoft Hive Open Database Connectivity ドライバー (Hive ODBC) からのみアクセスできます。 Hive コンソール、Templeton、Azure コマンドライン ツール (Azure CLI)、Azure PowerShell からはアクセスできません。 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>対話型クエリ クラスターの作成
HDInsight クラスターの作成について詳しくは、[HDInsight での Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。 対話型クエリのクラスターの種類を選択します。

## <a name="execute-hive-queries-from-interactive-query"></a>対話型クエリから Hive クエリを実行する
Hive クエリを実行するには、次のオプションがあります。

* Power BI を使用する

    [Azure HDInsight の Power BI を使用したビッグ データの視覚化](./hdinsight-connect-hive-power-bi.md)に関するページをご覧ください。

* Zeppelin を使用する

    「[Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)」をご覧ください。

* Visual Studio を使用する

    「[Data Lake Tools for Visual Studio を使用した Azure HDInsight への接続と Hive クエリの実行](hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query)」をご覧ください。

* Visual Studio Code を使用する

    [Hive、LLAP、pySpark に Visual Studio Code を使用する](hdinsight-for-vscode.md)方法に関するページをご覧ください。
* Ambari Hive ビューを使って Hive を実行する。
  
    [Azure HDInsight での Hive ビューと Hadoop の使用](hdinsight-hadoop-use-hive-ambari-view.md)に関するページをご覧ください。
* Beeline を使って Hive を実行する。
  
    「[Beeline による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-beeline.md)」をご覧ください。
  
    Beeline はヘッド ノードまたは空のエッジ ノードから使用できます。 空のエッジ ノードから Beeline を使用することをお勧めします。 空のエッジ ノードを使って HDInsight クラスターを作成する方法の詳細については、「[HDInsight での空のエッジ ノードの使用](hdinsight-apps-use-edge-node.md)」を参照してください。
* Hive ODBC を使って Hive を実行する。
  
    [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-odbc-driver.md)に関するページをご覧ください。

Java Database Connectivity (JDBC) 接続文字列は次の方法で調べることができます。

1. https://\<クラスター名\>.AzureHDInsight.net という URL を使用して Ambari にサインインします。
2. 左側のメニューで **[Hive]** を選択します。
3. URL をコピーするには、クリップボード アイコンを選択します。
   
   ![HDInsight Hadoop 対話型クエリ LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>次のステップ

* [HDInsight で対話型クエリ クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md)方法を学ぶ。
* [Azure HDInsight の Power BI でビッグ データを視覚化する](./hdinsight-connect-hive-power-bi.md)方法を学ぶ。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)方法を学ぶ。
* [Data Lake Tools for Visual Studio を使用して Hive クエリを実行する](./hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query)方法を学ぶ。
* [HDInsight Tools for Visual Studio Code を使用する](hdinsight-for-vscode.md)方法を学ぶ。
* [HDInsight で Hive View と Hadoop を使用する](hdinsight-hadoop-use-hive-ambari-view.md)方法を学ぶ。
* [Beeline を使用して HDInsight で Hive クエリを実行する](hdinsight-hadoop-use-hive-beeline.md)方法を学ぶ。
* [Microsoft Hive ODBC ドライバーを使用して Excel から Hadoop に接続する](hdinsight-connect-excel-hive-odbc-driver.md)方法を学ぶ。

