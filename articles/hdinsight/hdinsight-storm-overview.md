---
title: "HDInsight での Apache Storm の概要 | Microsoft Docs"
description: "HDInsight の Apache Storm について紹介しています。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 0aa2a7075f64b353f6b052ab6b973a06622a9339
ms.lasthandoff: 03/24/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>HDInsight での Apache Storm の概要: Hadoop のリアルタイム分析

Azure には、HDInsight の Apache Storm を使って分散型のリアルタイム分析ソリューションを作成できます。

Apache Storm は、Hadoop を使用して、リアルタイムでデータを処理できる、フォールト トレランスに優れたオープン ソースの分散型計算システムです。 また、Storm のソリューションは、最初に正常に処理されなかったデータを再生する機能を備え、保証されたデータ処理を実現します。

## <a name="why-use-storm-on-hdinsight"></a>HDInsight で Storm を使用する理由

HDInsight の Apache Storm  は、管理されるクラスターとして、Azure 環境に統合されて提供されます。 クラスターのオペレーティング システムは Ubuntu (Linux ディストリビューション) ですが、HDInsight における Storm やその他の Hadoop コンポーネントは Hortonworks Data Platform (HDP) に基づいています。 この構成により、Hadoop エコシステムで人気のあるツールやサービスと互換性のあるプラットフォームが提供されます。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

HDInsight における Apache Storm の主なメリットは次のとおりです。

* 稼働時間 99.9% の SLA を提供する、管理されたサービスとして実行されます。

* 作成中または作成後にクラスターに対してスクリプトを実行することで、簡単にカスタマイズできます。 詳細については、[スクリプト アクションを使った HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

* Storm コンポーネントは、言語選択の自由度が高く、**Java** や **C#**、**Python** などのさまざまな言語で作成することができます。

  * C# トポロジの開発、管理、監視を目的とした、Visual Studio と HDInsight の統合。 詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

  * **Trident** Java インターフェイスをサポートします。 このインターフェイスにより、メッセージの "厳密に 1 回" 処理、"トランザクション" のデータストア永続化、一般的なストリーム分析操作のセットをサポートする Storm トポロジの作成が可能になります。

* クラスターのスケールアップとスケールダウンが容易です。Storm トポロジの実行に影響を与えることなく、worker ノードを追加または削除することができます。

* 次の Azure サービスと連携します。

    * Event Hubs
    * 仮想ネットワーク
    * SQL Database
    * Azure Storage (Azure Storage)
    * DocumentDB に保存するものとします。

  * Azure Virtual Network を使用して、複数の HDInsight クラスターの機能を安全に組み合わせることができます。HDInsight、HBase、Hadoop クラスターを使用する分析パイプラインを作成できます。

リアルタイムの分析ソリューション用の Apache Storm を使用している企業の一覧については、「 [Companies Using Apache Storm (Apache Stormを使用している企業)](https://storm.apache.org/documentation/Powered-By.html)」をご覧ください。

Storm の使用方法については、[HDInsight での Storm の使用][gettingstarted]に関する記事を参照してください。

### <a name="ease-of-creation"></a>作成のしやすさ

HDInsight クラスター上に新しい Storm を数分でプロビジョニングできます。 クラスター名、サイズ、管理者アカウント、ストレージ アカウントを指定します。 Azure は、トポロジのサンプルや Web 管理ダッシュボードを含む、クラスターを作成します。

> [!NOTE]
> [Azure CLI](../cli-install-nodejs.md) または [Azure PowerShell](/powershell/azureps-cmdlets-docs) を使用して、Storm クラスターをプロビジョニングすることもできます。

要求を送信すると、15 分以内には、新しい Storm クラスターが実行され、最初のリアルタイム分析パイプラインの準備が完了します。

### <a name="ease-of-use"></a>使いやすさ

* __Secure Shell 接続__: SSH を使用してインターネット経由で HDInsight クラスターのヘッド ノードにアクセスすることができます。 SSH により、クラスター上で直接コマンドを実行できます。

  詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* __Web 接続__: HDInsight クラスターでは Ambari Web UI が提供されます。 Ambari Web UI を使用すれば、クラスター上のサービスの監視、構成、管理を簡単に行うことができます。 HDInsight における Storm では Storm UI も提供されます。これを使用すれば、お使いのブラウザーから Storm トポロジの実行を監視および管理できます。

  詳細については、[Ambari Web UI を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)に関する記事と、「[Storm UI を使用して監視および管理する](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui)」を参照してください。

* __Azure PowerShell および CLI__: Azure PowerShell と Azure CLI はいずれも、HDInsight やその他の Azure サービスを操作するためにクライアント システムから使用できるコマンド ライン ユーティリティです。

* __Visual Studio 統合__: Data Lake Tools for Visual Studio には、C# Storm トポロジの作成に使用できるプロジェクト テンプレートのほか、HDInsight の Storm を監視するためのツールが含まれています。 Visual Studio から、C# トポロジを作成、デプロイ、監視、管理することができます。

  詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

* __その他の Azure サービスとの統合__

  * __Java__ の開発の場合、Microsoft は既存の Storm コンポーネントを活用して、その他の Azure サービスと可能な限り統合します。 場合によっては、サービスに固有のコンポーネントまたはソリューションが必要になります。

    * __Azure Data Lake Store__: Java ベースのトポロジは、Storm-HDFS ボルトと `adl://` という URI スキームを使用することで、Data Lake Store にアクセスできます。 Storm-HDFS ボルトの使用例については、[HDInsight の Apache Storm による Azure Data Lake Store の使用](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-write-data-lake-store)に関する記事を参照してください。

    * __Azure Storage__ (HDInsight 向けストレージとして使用した場合): Java ベースのトポロジは、Storm-HDFS ボルトと `wasb://` という URI スキームを使用することで、Azure Storage にアクセスできます。

    * __Azure Event Hubs__: Microsoft から提供される EventHubSpout および EventHubBolt コンポーネントを使用してアクセスできます。 これらのコンポーネントは Java で記述され、スタンドアロンの .jar ファイルとして提供されます。

    Java ソリューションの開発の詳細については、[HDInsight の Storm 向けの Java ベースのトポロジの開発](hdinsight-storm-develop-java-topology.md)に関する記事を参照してください。

  * __C#__ の開発の場合、通常は Azure サービス向けの .NET SDK を使用できます。 場合によっては、SDK が Linux (HDInsight 3.4 以上でのホスト OS) では使用できないフレームワークに依存する可能性があります。その場合は、C# ソリューションから Java コンポーネントを使用することができます。

    * __SQL DB__、__DocumentDB__、__EventHub__、__HBase__ の使用例は、Azure Data Lake Tools for Visual Studio のテンプレートとして含まれています。 詳細については、[HDInsight の Storm 向けの C# トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

    * __Azure Event Hubs__: C# ソリューションからの Java コンポーネントの使用例については、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (＃C)](hdinsight-storm-develop-csharp-event-hub-topology.md)」を参照してください。

    C# ソリューションの開発の詳細については、[HDInsight の Storm 向けの C# トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

### <a name="reliability"></a>信頼性

Apache Storm は、データ分析が多数のノードにまたがる場合でも、各受信メッセージを完全に処理することを常に保証します。

**Nimbus ノード**は Hadoop JobTracker に同様の機能を提供し、**Zookeeper** を介して、タスクをクラスターの他のノードに割り当てます。 Zookeeper ノードは、クラスターに調整を提供し、Nimbus と ワーカー ノードの **Supervisor** 処理間の通信を容易にします。 処理中のノードの 1 つがダウンした場合、Nimbus ノードに通知され、タスクと関連付けられているデータが別のノードに割り当てられます。

Apache Storm の既定の構成では、Nimbus ノードは 1 つだけです。 HDInsight の Storm では、2 つの Nimbus ノードを実行します。 プライマリ ノードが失敗すると、プライマリ ノードが復旧中は、HDInsight クラスターはセカンダリ ノードに切り替わります。

![Nimbus、Zookeeper、スーパーバイザのダイアグラム](./media/hdinsight-storm-overview/nimbus.png)

### <a name="scale"></a>スケール

作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。 すべての HDInsight クラスターで、データの処理中でも、クラスター内のノード数を変更できます。

> [!NOTE]
> スケーリングによって追加された新しいノードを利用するには、クラスター サイズを増やす前に開始されていたトポロジを再調整する必要があります。

### <a name="support"></a>サポート

HDInsight の Storm には、完全なエンタープライズ レベルの 24 時間 365 日のサポートが付属します。 HDInsight の Storm では、99.9% の SLA も保証されています。 つまり、クラスターは、99.9% 以上の時間において外部に接続されることを保証します。

## <a name="common-use-cases-for-real-time-analytics"></a>リアルタイム分析の一般的なユース ケース

以下に、HDInsight で Apache Storm を使用する一般的なシナリオをいくつか示します。 実際のシナリオの詳細については、[企業での Storm の使用事例](https://storm.apache.org/documentation/Powered-By.html)をご覧ください。

* モノのインターネット(IoT)
* 不正行為の検出
* ソーシャル分析
* Extract Transform Load (ETL)
* ネットワーク監視
* Search
* Mobile Engagement


## <a name="how-is-data-in-hdinsight-storm-processed"></a>HDInsight Storm のデータの処理方法

Apache Storm は、HDInsight や Hadoop から使用することが一般的な MapReduce ジョブではなく、 **トポロジ** を処理します。 HDInsight クラスターの Storm には、**Nimbus** を実行するヘッド ノードと **Supervisor** を実行するワーカー ノードの 2 種類のノードが含まれます。

* **Nimbus**- Hadoop の JobTracker 同様、クラスター全体へのコードの配布、仮想マシンへのタスクの割り当て、障害の監視を処理します。 HDInsight は 2 つの Nimbus ノードを提供するため、HDInsight の Storm クラスターに単一障害点が生じません。
* **Supervisor**: 各ワーカー ノードの supervisor は、ノードの**ワーカー プロセス**の開始と停止を処理します。
* **ワーカー プロセス**: **トポロジ**のサブセットを実行します。 実行中のトポロジは、クラスター内の多くのワーカー プロセスに分散されます。
* **トポロジ**: データ **ストリーム**を処理する計算処理のグラフを定義します。 MapReduce ジョブとは異なり、トポロジは停止されるまで実行されます。
* **ストリーム**: **タプル**のバインドされていないコレクション。 ストリームは、**スパウト**と**ボルト**により生成され、**ボルト**によって利用されます。
* **タプル**: 動的に型指定される値の名前付きのリスト
* **スパウト**: データ ソースのデータを利用し、1 つ以上の**ストリーム**を発します。

  > [!NOTE]
  > 多くの場合、データは、Kafka、Azure Event Hubs などのキューから読み取られます。 システムが停止した場合、キューが確実にデータを保持します。

* **ボルト**: **ストリーム**を利用し、**タプル**に対して処理を実行します。**ストリーム**を発する場合もあります。 ボルトは、キュー、HDInsight HBase、BLOB、その他のデータ ストアなどの、外部ストレージへのデータの書き込みも処理します。
* **Apache Thrift**: 拡張性のある、多言語サービス開発のためのソフトウェア フレームワークです。 これを使用すると、C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk、その他の言語の間で動作するサービスを構築できます。

Storm コンポーネントの詳細については、apache.org の [Storm のチュートリアル][apachetutorial]を参照してください。

## <a name="what-programming-languages-can-i-use"></a>使用できるプログラミング言語

### <a name="c35"></a>C (&) #35；

Data Lake Tools for Visual Studio では、.NET 開発者は、C# のトポロジを設計し、実装できます。 Java と C# のコンポーネントを使用するハイブリッド トポロジを作成することもできます。

詳細については、「 [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

### <a name="java"></a>Java

目にすることがあるほとんどの Java の例は、プレーンな Java か Trident です。 Trident は、結合、集計、グループ化、フィルタリングなどの実行を容易にするための、高レベルの抽象概念です。 ただし、Trident は、タプルのバッチに作用し、ここでは未加工の Java ソリューションが 1 回に 1 タプルずつストリームを処理します。

Trident の詳細については、apache.org の「 [Trident のチュートリアル](https://storm.apache.org/documentation/Trident-tutorial.html) 」をご覧ください。

Java トポロジと Trident トポロジの例については、 [Storm トポロジの例の一覧](hdinsight-storm-example-topology.md) 、または HDInsight クラスターの storm-starter の例をご覧ください。

Storm-Starter のサンプルは、お使いの HDInsight クラスターの **/usr/hdp/current/storm-client/contrib/storm-starter** ディレクトリにあります。

### <a name="python"></a>Python

Python コンポーネントの使用例については、[HDInsight での Python を使用した Storm トポロジの開発](hdinsight-storm-develop-python-topology.md)に関するページを参照してください。

## <a name="what-are-some-common-development-patterns"></a>一般的な開発パターンの例

### <a name="guaranteed-message-processing"></a>メッセージの処理の保証

Storm は、さまざまなレベルでメッセージの処理を保証します。 たとえば、基本的な Storm アプリケーションは "最低 1 回" の処理を保証できますが、Trident は "厳密に 1 回" の処理を保証できます。

詳細については、apache.org の「 [Guarantees on data processing (データ処理の保証)](https://storm.apache.org/about/guarantees-data-processing.html) 」をご覧ください。

### <a name="ibasicbolt"></a>IBasicBolt

入力タプルを読み込み、0 個以上のタプルを発行し、実行メソッドの終了直後に入力タプルを確認するというパターンが一般的です。 Storm には、このパターンを自動化する [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) インターフェイスが用意されています。

### <a name="joins"></a>結合

データ ストリームの結合方法は、アプリケーションによって異なります。 たとえば、複数のストリームの各タプルを 1 つの新しいストリームに結合したり、特定のウィンドウに対してのみタプルのバッチを結合したりする場合があります。 いずれの場合も、結合は [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用して実行されます。これは、ボルトへのタプルのルーティングの定義方法の 1 つです。

次の Java の例では、fieldsGrouping は、 **MyJoiner** ボルトへの、コンポーネント "1"、"2"、"3" から発生したタプルのルーティングに使用されています。

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>バッチ処理

Storm には "tick タプル" と呼ばれる内部の時間調整メカニズムが備わっており、これを使って X 秒ごとにバッチを発することができます。

C# コンポーネントからの tick タプルの使用例については、[PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java) を参照してください。

Trident を使用している場合、これはタプルのバッチ処理を基盤とします。

### <a name="caching"></a>Caching (キャッシュ)

メモリ内キャッシュは、頻繁に使用されるアセットをメモリ内に保持することで、処理速度を上げるためのメカニズムとしてよく使用されます。 トポロジは複数のノード (また各ノード内の複数のプロセス) にまたがって分散されるため、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) の使用を検討してください。 `fieldsGrouping` によって、キャッシュの検索に使用されるフィールドを含んだタプルが常に同じプロセスにルーティングされます。 このグループ化の機能により、プロセス間でのキャッシュ エントリの重複が回避されます。

### <a name="streaming-top-n"></a>トップ N のストリーミング

トポロジが、"トップ N" の値の計算に依存する場合、トップ N の値を同時に計算し、計算の出力をグローバル値に結合する必要があります。 これには、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用して、同時に計算するフィールド別にルーティングし、次に、トップ N の値を一括で決定するボルトにルーティングします。

"トップ N" の値の計算例については、「[RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java)」の例を参照してください。

## <a name="what-type-of-logging-does-storm-use"></a>Storm で使用されるログの種類

Storm では、Apache Log4j を使用して情報をログに記録します。 既定では大量のデータがログに記録されるため、情報を調べるのが困難になる可能性があります。 Storm トポロジの一部にログの構成ファイルを含めることにより、ログ記録の動作を制御することができます。

ログ記録を構成する方法を示すトポロジの例については、HDInsight で Storm を使用した [Java ベースの WordCount](hdinsight-storm-develop-java-topology.md) の例を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight で Apache Storm を使用したリアルタイム分析ソリューションの詳細について学習します。

* [HDInsight での Storm の使用][gettingstarted]
* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

