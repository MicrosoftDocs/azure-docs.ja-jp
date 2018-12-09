---
title: HDInsight で Apache Ambari Tez ビューを使用する - Azure
description: HDInsight で Apache Ambari Tez ビューを使用して Tez ジョブをデバッグする方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c85ad59acc8e307de05f41365855f3a9669ac2b5
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034678"
---
# <a name="use-apache-ambari-views-to-debug-apache-tez-jobs-on-hdinsight"></a>HDInsight で Apache Ambari ビューを使用して Apache Tez ジョブをデバッグする

HDInsight の Apache Ambari Web UI には Tez ビューが含まれています。Apache Tez ビューは、Tez を使用するジョブの確認とデバッグに使用できます。 Tez ビューを使用すると、関連付けられた項目のグラフとしてジョブを可視化し、各項目をドリルダウンして、統計情報やログ情報を取得することができます。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md#hdinsight-windows-retirement)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 クラスターの作成手順については、「[Hadoop チュートリアル: Linux 上の HDInsight で Hive と Hadoop を使用する](hadoop/apache-hadoop-linux-tutorial-get-started.md)」を参照してください。
* HTML5 をサポートする最新の Web ブラウザー

## <a name="understanding-apache-tez"></a>Apache Tez について

Tez は、Apache Hadoop でデータを処理するための拡張可能なフレームワークで、処理速度が従来の MapReduce よりも向上します。 Linux ベースの HDInsight クラスターでは、これが Hive の既定のエンジンになります。

Tez は、ジョブで必要なアクションの順序を記述する有向非巡回グラフ (DAG) を作成します。 個々のアクションは頂点と呼ばれ、ジョブ全体の一部分を実行します。 頂点によって表される処理を実際に実行することはタスクと呼ばれ、クラスター内の複数のノードに分散される場合があります。

### <a name="understanding-the-tez-view"></a>Tez ビューについて

Tez ビューには、履歴情報と、実行中のプロセスに関する情報の両方が表示されます。 この情報は、ジョブがクラスター間でどのように分散されるかを示しています。 タスク、頂点、およびジョブに関連するエラー情報によって使用されるカウンターも表示されます。 次のシナリオで役立つ情報が提供される場合があります。

* Map タスクと Reduce タスクの進行状況を表示して、実行時間の長いプロセスの監視する。
* 成功または失敗したプロセスの履歴データを分析して、処理の改善方法や失敗した理由を確認する。

## <a name="generate-a-dag"></a>DAG の生成

Tez ビューには、Tez エンジンを使用するジョブが現在実行中か以前に実行されていた場合にのみデータが表示されます。 単純な Hive クエリは、Tez を使用しなくても解決できます。 フィルター処理、グループ化、並べ替え、結合などを行うより複雑なクエリは、 Tez エンジンを使用します。

Tez を使用する Hive クエリを実行するには、次の手順に従います。

1. Web ブラウザーで https://CLUSTERNAME.azurehdinsight.net に移動します。**CLUSTERNAME** は実際の HDInsight クラスターの名前です。

2. ページの上部にあるメニューの **[Views]** アイコンを選択します。 このアイコンは、四角形が並んだ形をしています。 表示されたドロップダウン リストで **[Hive View]** を選択します。

    ![Selecting Hive View](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Hive ビューが読み込まれたら、クエリ エディターに次のクエリを貼り付けて、**[Execute]** をクリックします。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    ジョブが完了すると、**[Query Process Results]** セクションに出力が表示されることがわかります。 結果は次のテキストのようになります。

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. **[Log]** タブを選択します。次のテキストのような情報が表示されます。

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    **App id** 値を保存します。この値は次のセクションで使用します。

## <a name="use-the-tez-view"></a>Tez ビューの使用

1. ページの上部にあるメニューの **[Views]** アイコンを選択します。 表示されたドロップダウン リストで **[Tez View]** を選択します。

    ![Selecting Tez view](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Tez ビューが読み込まれると、クラスターで現在実行されている Hive クエリや実行されていた Hive クエリの一覧が表示されます。

    ![All DAGS](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. エントリが 1 つしかない場合、それは、前のセクションで実行したクエリのエントリです。 複数のエントリがある場合は、ページの上部にあるフィールドを使用して検索できます。

4. Hive クエリの**クエリ ID** を選択します。 クエリの情報が表示されます。

    ![DAG Details](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. このページのタブでは、次の情報を確認できます。

    * **[クエリの詳細]**: Hive クエリに関する詳細情報。
    * **[タイムライン]**: 処理の各ステージに要した時間に関する情報。
    * **[構成]**: このクエリに使用される構成。

    __[クエリの詳細]__ では、リンクを使用してこのクエリの __アプリケーション__ や __DAG__ に関する情報を確認できます。
    
    * __[アプリケーション]__ リンクには、このクエリの YARN アプリケーションに関する情報が表示されます。 ここから YARN アプリケーションのログにアクセスすることができます。
    * __[DAG]__ リンクには、このクエリの有向非巡回グラフ (DAG) に関する情報が表示されます。 ここから DAG のグラフィカル表示を見ることができます。 また、DAG 内の頂点の情報も確認できます。

## <a name="next-steps"></a>次の手順

ここでは、Tez ビューの使用方法を説明しました。詳細については、[HDInsight での Hive の使用](hadoop/hdinsight-use-hive.md)に関する記事を参照してください。

Tez に関する技術的な情報の詳細については、 [Hortonworks の Tez に関するページ](http://hortonworks.com/hadoop/tez/)を参照してください。

HDInsight での Ambari の使用の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)
