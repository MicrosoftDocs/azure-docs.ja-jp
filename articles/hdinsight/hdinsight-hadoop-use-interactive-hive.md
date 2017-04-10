---
title: "HDInsight での対話型 Hive の使用 | Microsoft Docs"
description: "HDInsight での対話型 Hive (LLAP 上の Hive) の使用方法について説明します。"
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
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6971e123b388eab689e02a5c67809509c2ad29c6
ms.lasthandoff: 11/17/2016


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>HDInsight での対話型 Hive の使用 (プレビュー)
対話型 Hive (別名:  [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) は新しい HDInsight [クラスター タイプ](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)です。  対話型 Hive ではインメモリ キャッシュが可能になるため、Hive クエリの対話性が向上し、高速化します。 この新しい機能のおかげで、HDInsight は、高いパフォーマンスと柔軟性を合わせ持つ、クラウド上での世界有数のオープン ビッグ データ ソリューションになりました。インメモリ キャッシュ (Hive および Spark を使用) とR Services との密接な統合による高度な分析機能も備わっています。 

対話型 Hive クラスターは、Hadoop クラスターとは異なります。 Hive サービスのみが含まれます。 

> [!NOTE]
> MapReduce、Pig、Sqoop、Oozie、およびその他のサービスは、間もなくこのクラスター タイプから削除されます。
> 対話型 Hive クラスターの Hive サービスには、Ambari Hive ビュー、Beeline、および Hive ODBC のみでアクセスできます。 Hive コンソール、Templeton、Azure CLI、および Azure PowerShell からはアクセスできません。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>対話型 Hive クラスターの作成
対話型の Hive クラスターは、Linux ベースのクラスターでのみサポートされます。 HDInsight クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="execute-hive-from-interactive-hive"></a>対話型 Hive から Hive を実行
Hive クエリは次のいくつかの方法で実行できます。

* Ambari Hive ビュー を使用して Hive を実行
  
    Hive ビューの使用方法の詳細については、「[HDInsight での Hive View と Hadoop の使用](hdinsight-hadoop-use-hive-ambari-view.md)」を参照してください。
* Beeline を使用して Hive を実行
  
    HDInsight での Beeline の使用方法の詳細については、「[Beeline による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-beeline.md)」を参照してください。
  
    Beeline はヘッド ノードまたは空のエッジ ノードで使用します。  空のエッジ ノードから Beeline を使用することをお勧めします。  空のエッジ ノードから HDInsight クラスターを作成する方法の詳細については、「[HDInsight での空のエッジ ノードの使用](hdinsight-apps-use-edge-node.md)」を参照してください。
* Hive ODBC を使用して Hive を実行
  
    Hive ODBC の使い方の詳細については、「[Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-odbc-driver.md)」を参照してください。

**JDBC 接続文字列を見つけるには:**

1. https://<ClusterName>.AzureHDInsight.net という URL を使用して Ambari にサインインします。
2. 左側のメニューで **[Hive]** をクリックします。
3. 強調表示されているアイコンをクリックして、URL をコピーします。
   
   ![HDInsight Hadoop 対話型 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>関連項目
* [HDInsight での Hadoop Linux クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md): HDInsight で対話型 Hive クラスターを作成する方法を説明します。
* [Beeline による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-beeline.md): Beeline を使用して Hive クエリを発行する方法を説明します。
* [Microsoft Hive ODBC ドライバーを使用した Excel から Hadoop への接続](hdinsight-connect-excel-hive-odbc-driver.md): Excel から Hive に接続する方法を説明します。


