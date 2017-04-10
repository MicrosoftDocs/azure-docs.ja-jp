---
title: "Apache Storm と HBase を使用したセンサー データの分析 | Microsoft Docs"
description: "仮想ネットワークで Apache Storm に接続する方法について説明します。 Storm と HBase を使用して、イベント ハブが発するセンサー データを処理して D3.js で視覚化します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 138c90a1a9cbf3b85856f372beeb4472edb9e2e8
ms.lasthandoff: 03/25/2017


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Apache Storm、Event Hub、HBase を HDInsight (Hadoop) で使用してセンサー データを分析する

HDInsight で Apache Storm を使用して、Azure Event Hub からのセンサー データを処理する方法を説明します。 その後、データを HDInsight で Apache HBase に格納し、D3.js を使用して表示します。

このドキュメントで使用されている Azure Resource Manager テンプレートでは、リソース グループに複数の Azure リソースを作成する方法を示します。 このテンプレートは、Azure Virtual Network、2 つの HDInsight クラスター (Storm と HBase)、Azure Web アプリを作成します。 リアルタイム Web ダッシュボードの node.js 実装は、自動的に Web アプリにデプロイされます。

> [!NOTE]
> このドキュメントの情報とこのドキュメントで示されている例には、HDInsight バージョン 3.5 が必要です。
>
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
  
  > [!IMPORTANT]
  > 既存の HDInsight クラスターは必要ありません。 このドキュメントの手順では、以下のリソースを作成します。
  > 
  > * Azure Virtual Network
  > * HDInsight クラスターの Storm (Linux ベース、2 ワーカー ノード)
  > * HDInsight クラスターの HBase (Linux ベース、2 ワーカー ノード)
  > * Web ダッシュボードをホストする Azure Web アプリ

* [Node.js](http://nodejs.org/): 開発環境のローカルで Web ダッシュボードをプレビューするために使用されます。
* [Java と JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): Storm トポロジの開発に使用されます。
* [Maven](http://maven.apache.org/what-is-maven.html): プロジェクトのビルドとコンパイルに使用されます。
* [Git](http://git-scm.com/): GitHub からプロジェクトをダウンロードするために使用されます。
* **SSH** クライアント: Linux ベースの HDInsight クラスターに接続するために使用されます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
    
    > [!NOTE]
    > `scp` コマンドへのアクセス権も必要です。このコマンドは、ローカルの開発環境と、SSH を使用する HDInsight クラスターの間でファイルをコピーする場合に使用されます。


## <a name="architecture"></a>アーキテクチャ

![アーキテクチャ ダイアグラム](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

この例は、次のコンポーネントで構成されています。

* **Azure Event Hubs**: センサーから収集されたデータを格納します。
* **HDInsight でのStorm**: Event Hub からのデータのリアルタイムの処理を提供します。
* **HDInsight の HBase**: Storm によって処理された後のデータ用に、永続的な NoSQL データ ストアを提供します。
* **Azure Virtual Network サービス**: HDInsight の Storm と HDInsight クラスターの HBase の間のセキュリティで保護された通信を可能にします。
  
  > [!NOTE]
  > Java HBase クライアント API を使用する場合は、仮想ネットワークが必要です。 この API は HBase クラスターのパブリック ゲートウェイ経由では公開されません。 HBase クラスターと Storm クラスターを同じ仮想ネットワークにインストールすると、Storm クラスター (または仮想ネットワーク上のその他のシステム) はクライアント API を使用して直接 HBase にアクセスできるようになります。

* **ダッシュボードの Web サイト**: データをリアルタイムでグラフにするサンプルのダッシュボード。
  
  * Websites は Node.js で実装されるため、すべてのクライアント オペレーティング システムでテスト用に実行できます。また Azure Websites にデプロイすることもできます。
  * [Socket.io](http://socket.io/) は Storm トポロジと Web サイト間のリアルタイムの通信に使用します。
    
    > [!NOTE]
    > Socket.io を通信に使用することは実装の詳細になります。 未加工の Websocket や SignalR など、すべての通信フレームワークを使用できます。

  * [D3.js](http://d3js.org/) は、Web サイトに送信されるデータのグラフ化に使用します。

> [!IMPORTANT]
> Storm と HBase の両方に対して 1 つの HDInsight クラスターを作成するメソッドはサポートされていないため、2 つのクラスターが必要です。

トポロジは、[org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) クラスを使ってイベント ハブからデータを読み取り、[org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) クラスを使って HBase にデータを書き込みます。 Web サイトとの通信は、 [socket.io client.java](https://github.com/nkzawa/socket.io-client.java)を使用して行います。

次の図は、トポロジのレイアウトを説明しています。

![トポロジ ダイアグラム](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> これは、トポロジの簡略化されたビューです。 実行時に、各コンポーネントのインスタンスは、読み取られている Event Hub のパーティションごとに作成されます。 これらのインスタンスはクラスター内のノード間に配布され、次のようにデータはルーティングされます。
> 
> * スパウトからパーサーへのデータは負荷分散されます。
> * パーサーからダッシュボードおよび HBase へのデータはデバイス ID によってグループ化されるため、同じデバイスからのメッセージは常に同じコンポーネントにフローされます。

### <a name="topology-components"></a>トポロジ コンポーネント

* **EventHub スパウト**: スパウトは、Apache Storm バージョン 0.10.0 以降の一部として提供されます。
  
  > [!NOTE]
  > この例で使用される Event Hub スパウトには、HDInsight クラスター バージョン 3.3 または 3.4 の Storm が必要です。 HDInsight で以前のバージョンの Storm と共に Event Hubs を使用する方法については、「[HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する](hdinsight-storm-develop-java-event-hub-topology.md)」を参照してください。

* **ParserBolt.java**: スパウトによって出力されるデータは未加工の JSON で、一度に複数のイベントが生成されることがあります。 このボルトでは、スパウトによって送出されたデータを読み込み方法とそれを複数のフィールドを含むタプルとして新しいストリームに出力する方法を示します。
* **DashboardBolt.java**: このコンポーネントは、Java の Socket.io クライアント ライブラリを使用して Web ダッシュボードにリアルタイムでデータを送信する方法を示します。
* **Temperature.java**: これは、トポロジを定義し、**Config.properties** ファイルから構成データを読み込みます。

## <a name="prepare-your-environment"></a>環境を準備する

この例を使用する前に、Storm トポロジが読み取る Azure イベント ハブを作成する必要があります。

### <a name="configure-event-hub"></a>Event Hub の構成

Event Hub は、この例のデータ ソースです。 Event Hub を作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[+ 新規]** -> **[モノのインターネット]** -> **[Event Hubs]** の順に選びます。
2. **[名前空間の作成]** ブレードで、次のタスクを実行します。
   
   1. 名前空間の **名前** を入力します。
   2. 価格レベルを選択します。 **[[Basic]]** で十分です。
   3. 使用する Azure **サブスクリプション** を選択します。
   4. 既存のリソース グループを選択するか、新しいリソース グループを作成します。
   5. Event Hub の **場所** を選択します。
   6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。

3. 作成プロセスが完了すると、名前空間のイベント ハブのブレードが表示されます。 ここで、 **[+ イベント ハブの追加]**を選択します。 **[イベント ハブの作成]** ブレードで「**sensordata**」という名前を入力し、**[作成]** を選択します。 他のフィールドは既定値のままにします。
4. 名前空間のイベント ハブのブレードで、 **[Event Hubs]**を選択します。 [ **sensordata** ] エントリを選択します。
5. sensordata イベント ハブのブレードで、 **[共有アクセス ポリシー]**を選択します。 **[+ 追加]** リンクを使用して、次のポリシーを追加します。

    | ポリシー名 | Claims |
    | ----- | ----- |
    | デバイス | Send |
    | Storm | リッスン |

1. 両方のポリシーを選択し、 **主キー** の値を書き留めます。 後の手順で両方のポリシーの値が必要になります。

## <a name="download-and-configure-the-project"></a>プロジェクトをダウンロードして構成する

次のコマンドを使用して GitHub からプロジェクトをダウンロードします。

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

コマンドが完了すると、次のディレクトリ構造が構成されます。

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> このドキュメントでは、このサンプルに含まれるコードの詳細については説明しません。 しかし、コードは完全にコメント化されています。

**hdinsight-eventhub-example/TemperatureMonitor/resources/Config.properties** ファイルを開き、イベント ハブの情報を次の行に追加します。

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

この情報を追加したら、ファイルを保存します。

## <a name="compile-and-test-locally"></a>コンパイルしてローカルでテストする

テストする前に、ダッシュボードを開始し、トポロジの出力を表示し、Event Hub に格納するデータを生成する必要があります。

> [!IMPORTANT]
> このトポロジの HBase コンポーネントは、ローカルでテストするときにはアクティブではありません。 HBase クラスターの Java API には、クラスターが含まれる Azure Virtual Network の外部からアクセスできないためです。

### <a name="start-the-web-application"></a>Web アプリケーションの開始

1. 新しいコマンド プロンプトまたはターミナルを開き、ディレクトリを **hdinsight-eventhub-example/dashboard** に変更します。 次のコマンドを使用して、Web アプリケーションで必要な依存関係をインストールします。
   
        npm install

2. 次のコマンドを使用して、Web アプリケーションを開始します。
   
        node server.js
   
    次のテキストのようなメッセージが表示されます。
   
        Server listening at port 3000

3. Web ブラウザーを開き、アドレスとして「**http://localhost:3000/**」と入力します。 次の図ようなページが表示されます。
   
    ![web dashboard](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    このコマンド プロンプトまたはターミナルは開いたままにします。 テストした後で、Ctrl + C を使用して、Web サーバーを停止します。

### <a name="start-generating-data"></a>データの生成を開始する

> [!NOTE]
> このセクションの手順では、任意のプラットフォームで使用できるように、Node.js を使用します。 その他の言語の例については、 **Sendevent** ディレクトリをご覧ください。

1. 新しいプロンプト、シェル、またはターミナルを開き、ディレクトリを **hdinsight-eventhub-example/SendEvents/nodejs** に変更します。 アプリケーションで必要な依存関係をインストールするには、次のコマンドを使用します。
   
        npm install

2. テキスト エディターで、 **app.js** ファイルを開き、先に取得した Event Hub の情報を追加します。
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > この例では、**sensordata** を Event Hub の名前として使うことと、**Send** 要求を持つポリシーの名前として **devices** を使うことを前提としています。

3. 次のコマンドを使用して、Event Hub に新しいエントリを挿入します。
   
        node app.js
   
    Event Hub に送信されるデータを含む出力の複数の行が表示されます。
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>トポロジの開始

1. 新しいコマンド プロンプト、シェル、またはターミナルを開き、ディレクトリを **hdinsight-eventhub-example/TemperatureMonitor**に変更し、次のコマンドを使用してトポロジを開始します。
   
        mvn compile exec:java
   
    このコマンドは、ローカル モードでトポロジを開始します。 **Config.properties** ファイルに含まれる値によって、イベント ハブとローカル ダッシュボード Web サイトの接続情報が提供されます。 開始されると、トポロジがイベント ハブのエントリを読み取り、ローカル コンピューターで実行されているダッシュボードに送信します。 Web ダッシュボードに、次の図のような線が表示されます。
   
    ![dashboard with data](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. ダッシュボードの実行中に、前の手順の `node app.js` コマンドを使用して Event Hubs に新しいデータを送信します。 温度の値がランダムに生成されるため、グラフを更新して温度の大きな変化を表示する必要があります。
   
   > [!NOTE]
   > `node app.js` コマンドを使用するときは、**hdinsight-eventhub-example/SendEvents/Nodejs** ディレクトリに移動している必要があります。

3. ダッシュボードの更新を確認したら、Ctrl + C キーを押してトポロジを停止します。 Ctrl + C を使用して、ローカル Web サーバーも停止できます。

## <a name="create-a-storm-and-hbase-cluster"></a>Storm クラスターと HBase クラスターの作成

このセクションの手順では、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md) を使用して Azure Virtual Network を作成し、その仮想ネットワーク上に Storm クラスターと HBase クラスターを作成します。 また、このテンプレートは Azure Web アプリを作成し、それにダッシュボードのコピーをデプロイします。

> [!NOTE]
> 仮想ネットワークは、Storm クラスターで実行されているトポロジが HBase Java API を使用して HBase クラスターと直接通信できるように使用されます。

このドキュメントで使う Resource Manager テンプレートは、パブリック BLOB コンテナー (**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**) 内にあります。

1. 次のボタンをクリックして Azure にサインインし、Azure ポータルで Resource Manager テンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **[カスタム デプロイ]** ブレードで以下の値を入力します。
   
    ![HDInsight parameters](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **[Base Cluster Name (ベース クラスター名)]**: この値は、Strom クラスターと HBase クラスターのベース名として使用されます。 たとえば、「**abc**」と入力すると、**storm-abc** という名前の Storm クラスターと、**hbase-abc** という名前の HBase クラスターが作成されます。
   * **[Cluster Login User Name (クラスター ログイン ユーザー名)]**: Storm クラスターと HBase クラスターの管理者のユーザー名。
   * **[クラスター ログイン パスワード]**: Storm クラスターと HBase クラスターの管理者のユーザー パスワード。
   * **[SSH ユーザー名]**: Storm クラスターと HBase クラスター用に作成する SSH ユーザー。
   * **[SSH パスワード]**: Storm クラスターと HBase クラスター用の SSH ユーザーのパスワード。
   * **[場所]**: クラスターが作成されるリージョン。
     
     **[OK]** をクリックしてパラメーターを保存します。

3. **[基本]** セクションを使用して、リソース グループを作成するか、既存のリソース グループを選択します。
4. **[リソース グループの場所]** ドロップダウン メニューで、**[設定]** セクションの **[場所]** パラメーターに選んだものと同じ場所を選びます。
5. 使用条件を読み、**[上記の使用条件に同意する]** をオンにします。
6. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、クラスターと Web ダッシュボードが含まれているリソース グループのブレードにリダイレクトされます。

![Resource group blade for the vnet and clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> HDInsight クラスターの名前が **storm-BASENAME** と **hbase-BASENAME** であることに注目してください。この場合、BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。 また、ダッシュボード サイトの名前が **basename-dashboard** であることにも注目してください。 この値は、このドキュメントで後ほど使用されます。

## <a name="configure-the-dashboard-bolt"></a>ダッシュボード ボルトの構成

Web アプリとしてデプロイされたダッシュボードにデータを送信するには、**Config.properties** ファイル内の次の行を変更する必要があります。

    dashboard.uri: http://localhost:3000

`http://localhost:3000` を `http://BASENAME-dashboard.azurewebsites.net` に変更し、ファイルを保存します。 **BASENAME** は、前の手順で指定したベース名に置き換えます。 また、前の手順で作成したリソース グループを使用して、ダッシュボードを選択し、URL を表示することもできます。

## <a name="create-the-hbase-table"></a>HBase テーブルの作成

HBase にデータを格納するには、まず、テーブルを作成する必要があります。 Storm の書き込み先として必要なリソースを事前に作成しておきます。Storm トポロジの内部からリソースを作成しようとすると、複数のインスタンスが同じリソースを作成しようとする場合があるためです。 トポロジの外部でリソースを作成し、読み取り/書き込みと分析に Storm を使用します。

1. SSH を使用して HBase クラスターに接続します。その際、クラスターの作成時にテンプレートに指定した SSH ユーザーとパスワードを使います。 たとえば、`ssh` コマンドを使用して接続する場合は、次の構文を使用します。
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    このコマンドでは、**USERNAME** はクラスターの作成時に指定した SSH ユーザー名に置き換え、**BASENAME** は指定したベース名に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

2. SSH セッションから HBase シェルを開始します。
   
        hbase shell
   
    シェルが読み込まれると、`hbase(main):001:0>` プロンプトが表示されます。

3. HBase シェルから、次のコマンドを入力して、センサー データを格納するテーブルを作成します。
   
        create 'SensorData', 'cf'

4. 次のコマンドを使用して、テーブルが作成されたことを確認します。
   
        scan 'SensorData'
   
    これにより、次の例のように、テーブル内の行数が 0 であることを示す情報が返されます。
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. `exit` を入力して HBase シェルを終了します。

## <a name="configure-the-hbase-bolt"></a>HBase ボルトの構成

Storm クラスターから HBase に書き込むには、HBase クラスターの構成の詳細と共に HBase ボルトを指定する必要があります。 この例では、HBase クラスターから **hbase-site.xml** ファイルを使用します。

### <a name="download-the-hbase-sitexml"></a>hbase-site.xml のダウンロード

コマンド プロンプトで SCP を使用して、クラスターから **hbase-site.xml** ファイルをダウンロードします。 次の例では、**USERNAME** はクラスターの作成時に指定した SSH ユーザーに置き換え、**BASENAME** は前に指定したベース名に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。 `/path/to/TemperatureMonitor/resources/hbase-site.xml` は、TemperatureMonitor プロジェクト内のこのファイルへのパスに置き換えます。

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

このコマンドにより、指定したパスに **hbase-site.xml** がダウンロードされます。

### <a name="enable-the-hbase-bolt"></a>HBase ボルトを有効にします。

HBase ボルト コンポーネントを有効にするには、**TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** を開き、次の行をコメント解除します。

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

これらの行をコメント解除したら、ファイルを保存します。

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>ソリューションのビルド、パッケージ化、HDInsight へのデプロイ

開発環境で、以下の手順に従って Storm トポロジを storm クラスターにデプロイします。

1. **TemperatureMonitor** ディレクトリから次のコマンドを使用して、新しいビルドを実行し、プロジェクトから JAR パッケージを作成します。
   
        mvn clean compile package
   
    このコマンドにより、プロジェクトの **target** ディレクトリに **TemperatureMonitor-1.0-SNAPSHOT.jar** という名前のファイルが作成されます。

2. scp を使用して、 **TemperatureMonitor-1.0-SNAPSHOT.jar** ファイルを Storm クラスターにアップロードします。 次の例では、**USERNAME** はクラスターの作成時に指定した SSH ユーザーに置き換え、**BASENAME** は前に指定したベース名に置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > ファイルのアップロードには数分かかる場合があります。

3. ファイルのアップロードが完了したら、SSH を使用してクラスターに接続します。
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. トポロジを開始するには、SSH セッションから次のコマンドを使用します。
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. トポロジが開始されたら、Azure に発行した Web サイトにブラウザーを開いて、 `node app.js` コマンドを使用して Event Hub にデータを送信します。 情報を表示する Web ダッシュボードの更新が表示されます。
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>HBase データの表示

次の手順で HBase に接続して、データがテーブルに書き込まれたことを確認します。

1. SSH を使用して HBase クラスターに接続します。
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. SSH セッションから HBase シェルを開始します。
   
        hbase shell
   
    シェルが読み込まれると、`hbase(main):001:0>` プロンプトが表示されます。

3. テーブルの行を表示します。
   
        scan 'SensorData'
   
    このコマンドにより、次のテキストのように、テーブルにデータが含まれることを示す情報が返されます。
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > このスキャン操作では、テーブルから最大 10 行が返されます。

## <a name="delete-your-clusters"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

クラスター、ストレージ、Web アプリを一度に削除するには、それらを含むリソース グループを削除します。

## <a name="next-steps"></a>次のステップ

HDInsight でのその他の Storm トポロジの例については、[HDInsight での Storm トポロジの例](hdinsight-storm-example-topology.md)に関するページをご覧ください。

Apache ストームの詳細については、 [Apache Storm](https://storm.incubator.apache.org/) サイトをご覧ください。

HDInsight での HBase の詳細については、「[HDInsight HBase の概要](hdinsight-hbase-overview.md)」をご覧ください。

Socket.IO の詳細については、 [socket.io](http://socket.io/) サイトをご覧ください。

D3.js の詳細については、「[D3.js - Data Driven Documents (D3.js - データ駆動ドキュメント)](http://d3js.org/)」をご覧ください。

Java でトポロジを作成する方法の詳細については、「[HDInsight での Apache Storm の Java ベース トポロジの開発](hdinsight-storm-develop-java-topology.md)」をご覧ください。

.NET でトポロジを作成する方法の詳細については、「 [Visual Studio を使用して HDInsight で Apache Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)」をご覧ください。

[azure-portal]: https://portal.azure.com

