---
title: "Zeppelin を使用して Azure HDInsight で Hive クエリを実行する | Microsoft Docs"
description: "Zeppelin を使用して Hive クエリを実行する方法について説明します。"
keywords: "hdinsight,hadoop,hive,対話型クエリ,LLAP"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: b44321619f2aa94a6d98624ab1ee35a598fb6fc8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Zeppelin を使用して Azure HDInsight で Hive クエリを実行する 

HDInsight 対話型クエリ クラスターには、Zeppelin ノートブックが含まれます。このノートブックを利用し、対話型 Hive クエリを実行できます。 この記事では、Zeppelin を使用して Azure HDInsight で Hive クエリを実行する方法について説明します。 

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight 対話型クエリ クラスター**。 HDInsight クラスターの作成については、「[クラスターの作成](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。  クエリの種類では対話型クエリを必ず選択してください。 

## <a name="create-a-zeppelin-note"></a>Zeppelin Note を作成する

1. 次の URL を参照します。

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    **CLUSTERNAME** をクラスターの名前に置き換えます。

2. Hadoop ユーザー名とパスワードを入力します。 Zeppelin ページで、新しいノートを作成するか、既存のノートを開きます。 HiveSample には、サンプル Hive クエリが含まれています。  

    ![HDInsight 対話型クエリ Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. **[Note の新規作成]**をクリックします。
4. 次の値を入力または選択します。

    - Note 名: ノートの名前を入力します。
    - 既定のインタープリター: **[JDBC]** を選択します。

5. **[Note の作成]** をクリックします。
6. 次の Hive クエリを実行します。

        %jdbc(hive)
        show tables

    ![HDInsight 対話型クエリ Zeppelin でクエリを実行する](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    最初の行の **%jdbc(hive)** ステートメントは、Hive JDBC インタープリターを使用するようにノートブックに指示します。

    クエリは *hivesampletable* という名前の Hive テーブルを返します。

    次は、hivesampletable に対して実行できるさらに 2 つの Hive クエリです。 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    従来の Hive と比較すると、クエリ結果が返されるまでの時間が短くなりました。


## <a name="next-steps"></a>次のステップ
この記事では、Power BI を使用して HDInsight からデータを視覚化する方法について学習しました。  詳細については、次の記事を参照してください。

* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](./hdinsight-connect-hive-power-bi.md)。
* [Microsoft Hive ODBC Driver を使用して Excel を HDInsight に接続する](./hdinsight-connect-excel-hive-odbc-driver.md)。
* [Power Query を使用して Excel を Hadoop に接続する](./hdinsight-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Hive クエリを実行する](./hdinsight-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./hdinsight-upload-data.md)。
