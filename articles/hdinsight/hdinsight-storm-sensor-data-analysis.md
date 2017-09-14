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
ms.date: 08/09/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 0d1cc959c87bd64ed728f8b56c9b9156fa492a8b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Apache Storm、Event Hub、HBase を HDInsight (Hadoop) で使用してセンサー データを分析する

HDInsight で Apache Storm を使用して、Azure Event Hub からのセンサー データを処理する方法を説明します。 その後、データを HDInsight で Apache HBase に格納し、D3.js を使用して表示します。

このドキュメントで使用されている Azure Resource Manager テンプレートでは、リソース グループに複数の Azure リソースを作成する方法を示します。 このテンプレートでは、Azure Virtual Network、2 つの HDInsight クラスター (Storm と HBase)、Azure Web アプリを作成します。 リアルタイム Web ダッシュボードの node.js 実装は、自動的に Web アプリにデプロイされます。

> [!NOTE]
> このドキュメントの情報とこのドキュメントで示されている例には、HDInsight バージョン 3.6 が必要です。
>
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。
* [Node.js](http://nodejs.org/): 開発環境のローカルで Web ダッシュボードをプレビューするために使用されます。
* [Java と JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): Storm トポロジの開発に使用されます。
* [Maven](http://maven.apache.org/what-is-maven.html): プロジェクトのビルドとコンパイルに使用されます。
* [Git](http://git-scm.com/): GitHub からプロジェクトをダウンロードするために使用されます。
* **SSH** クライアント: Linux ベースの HDInsight クラスターに接続するために使用されます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。


> [!IMPORTANT]
> 既存の HDInsight クラスターは必要ありません。 このドキュメントの手順では、以下のリソースを作成します。
> 
> * Azure 仮想ネットワーク
> * HDInsight クラスターの Storm (Linux ベース、2 ワーカー ノード)
> * HDInsight クラスターの HBase (Linux ベース、2 ワーカー ノード)
> * Web ダッシュボードをホストする Azure Web アプリ

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
  
  * Web サイトは Node.js で実装されます。
  * [Socket.io](http://socket.io/) は Storm トポロジと Web サイト間のリアルタイムの通信に使用します。
    
    > [!NOTE]
    > Socket.io を通信に使用することは実装の詳細になります。 未加工の Websocket や SignalR など、すべての通信フレームワークを使用できます。

  * [D3.js](http://d3js.org/) は、Web サイトに送信されるデータのグラフ化に使用します。

> [!IMPORTANT]
> Storm と HBase の両方に対して 1 つの HDInsight クラスターを作成するメソッドはサポートされていないため、2 つのクラスターが必要です。

トポロジは、[org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) クラスを使ってイベント ハブからデータを読み取り、[org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html) クラスを使って HBase にデータを書き込みます。 Web サイトとの通信は、 [socket.io client.java](https://github.com/nkzawa/socket.io-client.java)を使用して行います。

次の図は、トポロジのレイアウトを説明しています。

![トポロジ ダイアグラム](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> この図は、トポロジの簡略化されたビューです。 イベント ハブの各パーティションに、各コンポーネントのインスタンスが作成されます。 これらのインスタンスはクラスター内のノード間に配布され、次のようにデータはルーティングされます。
> 
> * スパウトからパーサーへのデータは負荷分散されます。
> * パーサーからダッシュボードおよび HBase へのデータはデバイス ID によってグループ化されるため、同じデバイスからのメッセージは常に同じコンポーネントにフローされます。

### <a name="topology-components"></a>トポロジ コンポーネント

* **イベント ハブ スパウト**: このスパウトは、Apache Storm バージョン 0.10.0 以降の一部として提供されます。
  
  > [!NOTE]
  > この例で使用されるイベント ハブ スパウトには、HDInsight クラスター バージョン 3.5 または 3.6 の Storm が必要です。

* **ParserBolt.java**: スパウトによって出力されるデータは未加工の JSON で、一度に複数のイベントが生成されることがあります。 このボルトは、スパウトから出力されたデータを読み込み、JSON メッセージを解析します。 次に、複数のフィールドを含むタプルとしてデータを出力します。
* **DashboardBolt.java**: このコンポーネントは、Java の Socket.io クライアント ライブラリを使用して Web ダッシュボードにリアルタイムでデータを送信する方法を示します。
* **no-hbase.yaml**: ローカル モードで実行しているときに使用されるトポロジ定義。 HBase コンポーネントは使用されません。
* **with-hbase.yaml**: クラスター上でトポロジを実行しているときに使用されるトポロジ定義。 HBase コンポーネントが使用されます。
* **dev.properties**: イベント ハブ スパウト、HBase ボルト、およびダッシュボード コンポーネントの構成情報。

## <a name="prepare-your-environment"></a>環境を準備する

この例を使用する前に、Storm トポロジが読み取る Azure イベント ハブを作成する必要があります。

### <a name="configure-event-hub"></a>Event Hub の構成

Event Hub は、この例のデータ ソースです。 Event Hub を作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[+ 新規]** -> **[モノのインターネット]** -> **[Event Hubs]** の順に選びます。
2. **[名前空間の作成]** セクションで、次のタスクを実行します。
   
   1. 名前空間の **名前** を入力します。
   2. 価格レベルを選択します。 **[[Basic]]** で十分です。
   3. 使用する Azure **サブスクリプション** を選択します。
   4. 既存のリソース グループを選択するか、新しいリソース グループを作成します。
   5. Event Hub の **場所** を選択します。
   6. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。

3. 作成プロセスが完了すると、名前空間のイベント ハブの情報が表示されます。 ここで、 **[+ イベント ハブの追加]**を選択します。 **[イベント ハブの作成]** セクションで「**sensordata**」という名前を入力し、**[作成]** を選択します。 他のフィールドは既定値のままにします。
4. 名前空間の [Event Hubs] ビューで、**[Event Hubs]**を選択します。 [ **sensordata** ] エントリを選択します。
5. sensordata イベント ハブで、**[共有アクセス ポリシー]**を選択します。 **[+ 追加]** リンクを使用して、次のポリシーを追加します。

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
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> このドキュメントでは、この例に含まれるコードの詳細については説明しません。 しかし、コードは完全にコメント化されています。

イベント ハブから読み取るプロジェクトを構成するには、`hdinsight-eventhub-example/TemperatureMonitor/dev.properties` ファイルを開き、次の行にイベント ハブ情報を追加します。

```bash
eventhub.read.policy.name: your_read_policy_name
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>コンパイルしてローカルでテストする

> [!IMPORTANT]
> トポロジをローカルで使用するには、動作している Storm 開発環境が必要です。 詳細については、Apache.org の「[Setting up a Storm development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html)」(Storm 開発環境のセットアップ) を参照してください。

> [!WARNING]
> Windows 開発環境を使用している場合は、ローカルでトポロジを実行するときに `java.io.IOException` を受け取る可能性があります。 その場合は、HDInsight 上でのトポロジ実行に進んでください。

テストする前に、ダッシュボードを開始し、トポロジの出力を表示し、Event Hub に格納するデータを生成する必要があります。

> [!IMPORTANT]
> このトポロジの HBase コンポーネントは、ローカルでテストするときにはアクティブではありません。 HBase クラスターの Java API には、クラスターが含まれる Azure Virtual Network の外部からアクセスできません。

### <a name="start-the-web-application"></a>Web アプリケーションの開始

1. コマンド プロンプトを開き、ディレクトリを `hdinsight-eventhub-example/dashboard` に変更します。 次のコマンドを使用して、Web アプリケーションで必要な依存関係をインストールします。
   
    ```bash
    npm install
    ```

2. 次のコマンドを使用して、Web アプリケーションを開始します。
   
    ```bash
    node server.js
    ```
   
    次のテキストのようなメッセージが表示されます。
   
        Server listening at port 3000

3. Web ブラウザーを開き､アドレスに `http://localhost:3000/` を入力します｡ 次の図ようなページが表示されます。
   
    ![web dashboard](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    このコマンド プロンプトは開いたままにしておきます。 テストした後で、Ctrl + C を使用して、Web サーバーを停止します。

### <a name="generate-data"></a>データを生成する

> [!NOTE]
> このセクションの手順では、任意のプラットフォームで使用できるように、Node.js を使用します。 その他の言語の例については、`SendEvents` ディレクトリを参照してください。

1. 新しいプロンプト、シェル、またはターミナルを開き、ディレクトリを `hdinsight-eventhub-example/SendEvents/nodejs` に変更します。 アプリケーションで必要な依存関係をインストールするには、次のコマンドを使用します。

    ```bash
    npm install
    ```

2. テキスト エディターで、`app.js` ファイルを開き、先に取得したイベント ハブの情報を追加します。
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'YourNamespace';
    // Event Hub Name
    var hubname ='sensordata';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'devices';
    var my_key = 'YourKey';
    ```
   
   > [!NOTE]
   > この例では、イベント ハブ名に `sensordata`、 `Send` 要求を含むポリシーの名前に `devices` を使用していることを前提としています。

3. 次のコマンドを使用して、Event Hub に新しいエントリを挿入します。
   
    ```bash
    node app.js
    ```
   
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

### <a name="build-and-start-the-topology"></a>トポロジのビルドと開始

1. 新しいコマンド プロンプトを開き、ディレクトリを `hdinsight-eventhub-example/TemperatureMonitor` に変更します。 トポロジを構築およびパッケージ化するには、次のコマンドを使用します。 

    ```bash
    mvn clean package
    ```

2. ローカル モードでトポロジを開始するには、次のコマンドを使用します。

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local` を使用すると、ローカル モードでトポロジが開始されます。
    * `--filter` は、`dev.properties` ファイルを使用して、トポロジ定義にパラメーターを設定します。
    * `resources/no-hbase.yaml` は `no-hbase.yaml` トポロジ定義を使用します。
 
   開始されると、トポロジがイベント ハブのエントリを読み取り、ローカル コンピューターで実行されているダッシュボードに送信します。 Web ダッシュボードに、次の図のような線が表示されます。
   
    ![dashboard with data](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. ダッシュボードの実行中に、前の手順の `node app.js` コマンドを使用して Event Hubs に新しいデータを送信します。 温度の値がランダムに生成されるため、グラフを更新して温度の大きな変化を表示する必要があります。
   
   > [!NOTE]
   > `node app.js` コマンドを使用するときは、**hdinsight-eventhub-example/SendEvents/Nodejs** ディレクトリに移動している必要があります。

3. ダッシュボードの更新を確認したら、Ctrl + C キーを押してトポロジを停止します。 Ctrl + C を使用して、ローカル Web サーバーも停止できます。

## <a name="create-a-storm-and-hbase-cluster"></a>Storm クラスターと HBase クラスターの作成

このセクションの手順では、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md)を使用して Azure Virtual Network を作成し、その仮想ネットワーク上に Storm クラスターと HBase クラスターを作成します。 また、このテンプレートは Azure Web アプリを作成し、それにダッシュボードのコピーをデプロイします。

> [!NOTE]
> 仮想ネットワークは、Storm クラスターで実行されているトポロジが HBase Java API を使用して HBase クラスターと直接通信できるように使用されます。

このドキュメントで使う Resource Manager テンプレートは、パブリック BLOB コンテナー (**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**) 内にあります。

1. 次のボタンをクリックして Azure にサインインし、Azure ポータルで Resource Manager テンプレートを開きます。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. **[カスタム デプロイ]** セクションで以下の値を入力します。
   
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

リソースが作成されると、リソース グループに関する情報が表示されます。

![VNET とクラスターのリソース グループ](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> HDInsight クラスターの名前が **storm-BASENAME** と **hbase-BASENAME** であることに注目してください。この場合、BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。 また、ダッシュボード サイトの名前が **basename-dashboard** であることにも注目してください。 この値は、このドキュメントで後ほど使用されます。

## <a name="configure-the-dashboard-bolt"></a>ダッシュボード ボルトの構成

Web アプリとしてデプロイされたダッシュボードにデータを送信するには、`dev.properties` ファイル内の次の行を変更する必要があります。

```yaml
dashboard.uri: http://localhost:3000
```

`http://localhost:3000` を `http://BASENAME-dashboard.azurewebsites.net` に変更し、ファイルを保存します。 **BASENAME** は、前の手順で指定したベース名に置き換えます。 また、前の手順で作成したリソース グループを使用して、ダッシュボードを選択し、URL を表示することもできます。

## <a name="create-the-hbase-table"></a>HBase テーブルの作成

HBase にデータを格納するには、まず、テーブルを作成する必要があります。 Storm の書き込み先として必要なリソースを事前に作成しておきます。Storm トポロジの内部からリソースを作成しようとすると、複数のインスタンスが同じリソースを作成しようとする場合があるためです。 トポロジの外部でリソースを作成し、読み取り/書き込みと分析に Storm を使用します。

1. SSH を使用して HBase クラスターに接続します。その際、クラスターの作成時にテンプレートに指定した SSH ユーザーとパスワードを使います。 たとえば、`ssh` コマンドを使用して接続する場合は、次の構文を使用します。
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    `sshuser` を、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 `clustername` を HBase クラスター名に置き換えます。

2. SSH セッションから HBase シェルを開始します。
   
    ```bash
    hbase shell
    ```
   
    シェルが読み込まれると、`hbase(main):001:0>` プロンプトが表示されます。

3. HBase シェルから、次のコマンドを入力して、センサー データを格納するテーブルを作成します。
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. 次のコマンドを使用して、テーブルが作成されたことを確認します。
   
    ```hbase
    scan 'SensorData'
    ```
   
    これにより、次の例のように、テーブル内の行数が 0 であることを示す情報が返されます。
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. `exit` を入力して HBase シェルを終了します。

## <a name="configure-the-hbase-bolt"></a>HBase ボルトの構成

Storm クラスターから HBase に書き込むには、HBase クラスターの構成の詳細と共に HBase ボルトを指定する必要があります。

1. 次のいずれかの例を使用すると、HBase クラスターの Zookeeper クォーラムを取得できます。

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > `your_HDInsight_cluster_name` を、使用する HDInsight クラスターの名前に置き換えます。 `jq` ユーティリティのインストール方法の詳細については、[https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) を参照してください。
    >
    > メッセージが表示されたら、HDInsight 管理者ログインのパスワードを入力します。

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name`
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > 'your_HDInsight_cluster_name' を、使用する HDInsight クラスターの名前で置き換えます。 メッセージが表示されたら、HDInsight 管理者ログインのパスワードを入力します。
    >
    > この例では、Azure PowerShell が必要です。 Azure PowerShell の使用方法の詳細については、「[Get started with Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)」(Azure PowerShell の使用) を参照してください。

    以下の例では、次のテキストのような情報が返されます。

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    この情報は、Storm が HBase クラスターと通信するために使用されます。

2. `dev.properties` ファイルを変更し、Zookeeper クォーラム情報を次の行に追加します。

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>ソリューションのビルド、パッケージ化、HDInsight へのデプロイ

開発環境で、以下の手順に従って Storm トポロジを storm クラスターにデプロイします。

1. `TemperatureMonitor` ディレクトリから次のコマンドを使用して、新しいビルドを実行し、プロジェクトから JAR パッケージを作成します。
   
        mvn clean package
   
    このコマンドで、プロジェクトの 'target' ディレクトリに `TemperatureMonitor-1.0-SNAPSHOT.jar in the ` というファイルが作成されます。

2. scp を使用して `TemperatureMonitor-1.0-SNAPSHOT.jar` ファイルと `dev.properties` ファイルを Storm クラスターにアップロードします。 次の例では、`sshuser` をクラスターの作成時に指定した SSH ユーザーで置き換えます。また、`clustername` を Storm クラスターの名前で置き換えます。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > ファイルのアップロードには数分かかる場合があります。

    `scp` および `ssh` コマンドの使用方法の詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](./hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

3. ファイルのアップロードが完了したら、SSH を使用して Storm クラスターに接続します。
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    `sshuser` を SSH ユーザー名で置き換えます。 `clustername` を Storm クラスター名で置き換えます。

4. トポロジを開始するには、SSH セッションから次のコマンドを使用します。
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote` からトポロジが Nimbus サービスに送信され、Nimbus サービスからクラスター内のスーパーバイザー ノードに配信されます。
    * `--filter` は、`dev.properties` ファイルを使用して、トポロジ定義にパラメーターを設定します。
    * `-R /with-hbase.yaml` は、パッケージに含まれている `with-hbase.yaml` トポロジを使用します。

5. トポロジが開始されたら、Azure に発行した Web サイトにブラウザーを開いて、 `node app.js` コマンドを使用して Event Hub にデータを送信します。 情報を表示する Web ダッシュボードの更新が表示されます。
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>HBase データの表示

次の手順で HBase に接続して、データがテーブルに書き込まれたことを確認します。

1. SSH を使用して HBase クラスターに接続します。
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    `sshuser` を SSH ユーザー名で置き換えます。 `clustername` を HBase クラスター名に置き換えます。

2. SSH セッションから HBase シェルを開始します。
   
    ```bash
    hbase shell
    ```
   
    シェルが読み込まれると、`hbase(main):001:0>` プロンプトが表示されます。

3. テーブルの行を表示します。
   
    ```hbase
    scan 'SensorData'
    ```
   
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

