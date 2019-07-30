---
title: クイック スタート:Azure HDInsight で Apache Hive クエリを実行する - Apache Zeppelin
description: このクイックスタートでは、Apache Zeppelin を使用して Apache Hive クエリを実行する方法について説明します。
keywords: hdinsight,hadoop,hive,対話型クエリ,LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 1642c64b0b14c2e290aad689399b59d896660a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056685"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>クイック スタート:Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する

このクイックスタートでは、Azure HDInsight で Apache Zeppelin を使用して [Apache Hive](https://hive.apache.org/) クエリを実行する方法について説明します。 HDInsight 対話型クエリ クラスターには、対話型 Hive クエリを実行するために使用できる [Apache Zeppelin](https://zeppelin.apache.org/) ノートブックが含まれています。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

HDInsight 対話型クエリ クラスター。 HDInsight クラスターの作成については、[クラスターの作成](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)に関するセクションを参照してください。  クラスターの種類では、 **[インタラクティブ クエリ]** を必ず選択してください。

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin Note を作成する

1. URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` の `CLUSTERNAME` をお使いのクラスターの名前に置き換えます。 Web ブラウザーに URL を入力します。

2. クラスターのログイン ユーザー名とパスワードを入力します。 Zeppelin ページで、新しいノートを作成するか、既存のノートを開きます。 **HiveSample** には、サンプル Hive クエリが含まれています。  

    ![HDInsight 対話型クエリ Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. **[Create new note]\(新しいノートの作成\)** を選択します。

4. **[Create new note]\(新しいノートの作成\)** ダイアログで、次の値を入力または選択します。

    - [Note Name]\(ノート名\):ノートの名前を入力します。
    - [Default interpreter]\(既定のインタープリター\):ドロップダウン リストから **[jdbc]** を選択します。

5. **[Create Note]\(ノートの作成\)** を選択します。

6. 次の Hive クエリをコード セクションに入力し、**Shift + Enter** キーを押します。

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight 対話型クエリ Zeppelin でクエリを実行する](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    最初の行の **%jdbc(hive)** ステートメントは、Hive JDBC インタープリターを使用するようにノートブックに指示します。

    クエリは **hivesampletable** という名前の Hive テーブルを返します。

    **hivesampletable** に対して実行できるその他の Hive クエリを 2 つ次に示します。

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    従来の Hive と比較すると、クエリ結果が返されるまでの時間が短くなりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する方法について学習しました。 Hive クエリの詳細について、次の記事で Visual Studio を使用してクエリを実行する方法を紹介します。

> [!div class="nextstepaction"]
> [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
