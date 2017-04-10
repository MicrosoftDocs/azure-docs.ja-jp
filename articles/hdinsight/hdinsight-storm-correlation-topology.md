---
title: "HDInsight での一定期間に発生したイベントの Storm および HBase との関連付け"
description: "HDInsight で Storm と HBase を使用して、別々の時間に到着するイベントを関連付ける方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: a16b3eee9ed52a197b5407dc7ebe71c0710d6fa1
ms.lasthandoff: 03/07/2017

---
# <a name="correlate-events-that-arrive-at-different-times-using-storm-and-hbase"></a>Storm と HBase を使用して別々の時刻に到着するイベントを関連付ける

Apache Storm の永続的なデータ ストアを使用して、別々の時刻に到着するデータのエントリを関連付けることができます。 たとえば、ユーザー セッションのログイン イベントとログアウト イベントをリンクして、セッションの継続期間を計算します。

このドキュメントでは、ユーザー セッションのログイン イベントとログアウト イベントを追跡し、セッションの期間を計算する基本的な C# Storm トポロジを作成する方法を学習します。 このトポロジは、永続的なデータ ストアとして HBase を使用します。 HBase では、履歴データに対してバッチのクエリを実行して、追加の情報を生成することもできます。 たとえば、特定の期間に開始または終了したユーザー セッションの数などです。

## <a name="prerequisites"></a>前提条件

* Visual Studio および HDInsight tools for Visual Studio。 詳細については、「[HDInsight Tools for Visual Studio を使用して Hive クエリを実行する](hdinsight-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

* HDInsight クラスターの Apache Storm (Windows ベース)。
  
  > [!IMPORTANT]
  > SCP.NET トポロジは、2016 年 10 月 28 日以降に作成された Linux ベース Storm クラスタでサポートされます。2016 年 10 月 28 日以降に入手可能な HBase SDK for .NET パッケージは Linux では正常に作動しません。

* HDInsight クラスター (Linux または Windows ベース) の Apache HBase。

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* 開発環境での [Java](https://java.com) 1.7 以降 Java を使用して、HDInsight クラスターへの送信時にトポロジをパッケージ化します。

  * **JAVA_HOME** 環境変数は、Java があるディレクトリを指している必要があります。
  * **%JAVA_HOME%/bin** ディレクトリはパス内にある必要があります。

## <a name="architecture"></a>アーキテクチャ

![トポロジ経由のデータ フロー図](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

イベントを関連付けるには、イベント ソースの共通の識別子が必要です。 たとえば、ユーザー ID、セッション ID、または a) 一意で、b) Storm に送信されたすべてのデータに含まれている他のデータが必要です。 この例では、GUID 値を使用してセッション ID を表します。

この例は、次の&2; つの HDInsight クラスターで構成されます。

* HBase: 履歴データの永続的なデータ ストア
* Storm: 受信データの取り込みに使用

データは、Storm トポロジによってランダムに生成され、次の項目で構成されます。

* セッション ID: 各セッションを一意に識別する GUID
* イベント: START または END イベント。 この例では、START は必ず END の前に発生します。
* 時刻: イベントの時刻。

このデータは、処理されて HBase に格納されます。

### <a name="storm-topology"></a>Storm トポロジ

セッションの開始時に、 **START** イベントはトポロジによって受信され、HBase に記録されます。 **END** イベントが受信されると、トポロジによって **START** イベントが取得され、2 つのイベントの間の時間が計算されます。 この、**期間**の値が、**END** イベントの情報と共に HBase に格納されます。

> [!IMPORTANT]
> このトポロジでは、基本的なパターンを説明しましたが、運用環境のソリューションでは、次のシナリオに対応した設計を使用する必要があります。
> 
> * 順序に関係なく到着するイベント
> * 重複イベント
> * 削除されたイベント

サンプルのトポロジは、次のコンポーネントで構成されています。

* Session.cs: ランダムなセッション ID、開始時刻、セッションの継続時間を作成することで、ユーザー セッションをシミュレートします。

* Spout.cs: 100 個のセッションを作成し、START イベントを出力します。セッションごとにランダムなタイムアウトを発生させて待機し、END イベントを出力します。 その後、終了したセッションをリサイクルして、新しいセッションを生成します。

* HBaseLookupBolt.cs: セッション ID を使用して HBase からセッション情報を検索します。 END イベントが処理されると、対応する START イベントを検索して、セッションの期間を計算します。

* HBaseBolt.cs: HBase に情報を格納します。

* TypeHelper.cs: HBase からの読み取り/HBase への書き込み時の型の変換で役立ちます。

### <a name="hbase-schema"></a>HBase のスキーマ

HBase では、データは、次のスキーマと設定を使用してテーブルに格納されます。

* Row key: セッション ID が、このテーブルの行キーとして使用されます。

* Column family: ファミリ名は 'cf' です。 このファミリに格納される列は、次のとおりです。
  
  * event: START または END

  * time: イベントが発生した時刻 (ミリ秒)

  * duration: START と END イベントの間の時間

* VERSIONS: 'cf' ファミリが各行の 5 つのバージョンを保持するよう設定されます。
  
  > [!NOTE]
  > バージョンとは、以前格納された、特定の行キーの値のログです。 既定では、HBase により、行の最新バージョンの値のみが返されます。 この場合は、同じ行がすべてのイベント (START、END) に使用されます。行の各バージョンは、タイムスタンプの値によって識別されます。 バージョンにより、ログに記録された、特定の ID のイベント履歴を表示できます。

## <a name="download-the-project"></a>プロジェクトのダウンロード

サンプル プロジェクトは [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation)からダウンロードできます。

このダウンロードには、次の C# プロジェクトが含まれています。

* CorrelationTopology: ユーザー セッションの開始イベントと終了イベントをランダムに生成する C# Storm トポロジ。 各セッションは、1 ～ 5 分間継続します。

* SessionInfo: HBase テーブルを作成し、格納されているセッション データに関する情報を返すクエリの例を提供する C# コンソール アプリケーション。

## <a name="create-the-table"></a>テーブルを作成する

1. Visual Studio で **[SessionInfo]** プロジェクトを開きます。

2. **[ソリューション エクスプローラー]** で、**[SessionInfo]** プロジェクトを右クリックし、**[プロパティ]** を選択します。
   
    ![メニューとプロパティが選択されたスクリーンショット](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. **[設定]**を選択し、次の値を設定します。
   
   * HBaseClusterURL: HBase クラスターの URL。 https://myhbasecluster.azurehdinsight.net など

   * HBaseClusterUserName: クラスターの管理者/HTTP ユーザー アカウント

   * HBaseClusterPassword: 管理者/HTTP ユーザー アカウントのパスワード

   * HBaseTableName: この例で使用するテーブルの名前

   * HBaseTableColumnFamily: 列ファミリ名
     
     ![[設定] ダイアログの画像](./media/hdinsight-storm-correlation-topology/settings.png)

4. ソリューションを実行する メッセージが表示されたら、'c' キーを選択し、HBase クラスター上でテーブルを作成します。

## <a name="build-and-deploy-the-storm-topology"></a>Storm トポロジをビルドおよびデプロイする

1. Visual Studio で **[CorrelationTopology]** ソリューションを開きます。

2. **[ソリューション エクスプローラー]** で、**[CorrelationTopology]** プロジェクトを右クリックし、[プロパティ] を選択します。

3. [プロパティ] ウィンドウで、**[設定]** を選択し、このプロジェクトの構成値を入力します。 最初の 5 つの項目には、**SessionInfo** プロジェクトで使用するのと同じ値を指定します。
   
   * HBaseClusterURL: HBase クラスターの URL。 https://myhbasecluster.azurehdinsight.net など。

   * HBaseClusterUserName: クラスターの管理者/HTTP ユーザー アカウント。

   * HBaseClusterPassword: 管理者/HTTP ユーザー アカウントのパスワード。

   * HBaseTableName: この例で使用するテーブルの名前。 この値は、SessionInfo プロジェクトで使用するのと同じテーブル名です。

   * HBaseTableColumnFamily: 列ファミリ名。 この値は、SessionInfo プロジェクトで使用するのと同じ列ファミリ名です。
   
   > [!IMPORTANT]
   > HBaseTableColumnNames は変更しないでください。これらの既定値は、データを取得するために **SessionInfo** が使用する名前であるためです。

4. プロパティを保存し、プロジェクトをビルドします。

5. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[HDInsight の Storm に送信]** を選択します。 メッセージが表示されたら、Azure サブスクリプションの資格情報を入力します。
   
   ![[Storm に送信] メニュー項目の画像](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. **[トポロジの送信]** ダイアログ ボックスで、このトポロジのデプロイ先の Storm クラスターを選択します。
   
   > [!NOTE]
   > 最初にトポロジを送信するときは、HDInsight クラスターの名前を取得するのに数秒かかる場合があります。

7. トポロジがアップロードされてクラスターに送信されると、**[Storm トポロジ ビュー]** が開き、現在実行中のトポロジが表示されます。 データを更新するには、**[CorrelationTopology]** を選択し、ページ右上にある [更新] ボタンを使用します。
   
   ![トポロジ ビューの画像](./media/hdinsight-storm-correlation-topology/topologyview.png)
   
   トポロジがデータの生成を開始すると、**[Emitted]** 列の値がインクリメントされます。
   
   > [!NOTE]
   > **[Storm トポロジ ビュー]** が自動的に開かない場合は、次の手順を使用して開きます。
   > 
   > 1. **[ソリューション エクスプローラー]** で、**[Azure]**、**[HDInsight]** の順に展開します。
   > 2. トポロジを実行している Storm クラスターを右クリックし、**[Storm トポロジの表示]** を選択します。

## <a name="query-the-data"></a>データを照会する

データが出力されたら、次の手順を使用してデータを照会します。

1. **[SessionInfo]** プロジェクトに戻ります。 実行されていない場合は、トポロジの新しいインスタンスを開始します。

2. メッセージが表示されたら、**[s]** を選択して、START イベントを検索します。 時間の範囲を定義するために、開始時刻および終了時刻を入力するように求められます。これら&2; つの時刻の間のイベントのみが返されます。
   
    開始時刻と終了時刻を入力するときには、HH:MM と 'am' または 'pm' の形式を使用します。 たとえば、11:20pm です。
   
    ログに記録されたイベントを取得するには、開始時刻には Storm トポロジがデプロイされるよりも前の時刻、終了時刻には現在の時刻を使用します。 返されるデータには、次のテキストのようなエントリが含まれます。
   
        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

START イベントと同じ方法で END イベントを検索できます。 ただし、END イベントは、START イベントの後に、1 ～ 5 分の間隔でランダムに生成されます。 END イベントを検索するために、いくつかの時間範囲を試す必要がある場合があります。 END イベントには、セッションの期間も含まれます。これは、START イベントの時刻と END イベントの時刻の差です。 次に、END イベントのデータの例を示します。

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> 現地時間で時間の値を入力すると、クエリから返される時刻は UTC になります。

## <a name="stop-the-topology"></a>トポロジを停止する

トポロジを停止する準備ができたら、Visual Studio で **[CorrelationTopology]** プロジェクトに戻ります。 **[Storm トポロジ ビュー]** で、トポロジを選択して、トポロジ ビューの上部にある **[終了]** ボタンを使用します。

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

Storm の例の詳細については、「 [HDInsight での Storm のサンプル トポロジ](hdinsight-storm-example-topology.md)」を参照してください。


