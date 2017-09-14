---
title: "Java を使用した HDInsight の Storm での Event Hubs のイベントの処理 | Microsoft Docs"
description: "Maven で作成した Java Storm トポロジを使用して Event Hubs を処理する方法について説明します。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java)

HDInsight で Storm を使用して Azure Event Hubs を使用する方法について説明します。 この例では、Java ベースのコンポーネントを使用して、Azure Event Hubs のデータの読み取りと書き込みを行います。

Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。 イベント ハブのスパウトにより、HDInsight で Apache Storm を使用してこのデータをリアルタイムで分析することが簡単になります。 また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

## <a name="prerequisites"></a>前提条件

* HDInsight クラスター バージョン 3.6 での Apache Storm。 詳細については、[HDInsight クラスターでの Storm の使用](hdinsight-apache-storm-tutorial-get-started-linux.md)に関する記事を参照してください。

    > [!IMPORTANT]
    > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) バージョン 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) または同等のバージョン ([OpenJDK](http://openjdk.java.net/) など)。

* [Maven](https://maven.apache.org/download.cgi): Maven は Java プロジェクトのプロジェクト ビルド システムです

* テキスト エディターまたは統合開発環境 (IDE)。

    > [!NOTE]
    > お使いのエディターまたは IDE には、Maven との操作用の特定の機能が搭載されている場合があります (本ドキュメントではカバーしていません)。 お使いの編集環境の機能に関する詳細は、製品のマニュアルをご覧ください。

    * SSH クライアント。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* `ssh` コマンドと `scp` コマンド。 これらは、ファイルを HDInsight クラスターにコピーするために使います。 Windows では、これらは Windows 10 の Bash で利用できます。

## <a name="understanding-the-example"></a>例の説明

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) の例には、2 つのトポロジが含まれています。

`resources/writer.yaml` トポロジは、Azure Event Hub にランダムなデータを書き込みます。 データは `DeviceSpout` コンポーネントによって生成され、ランダムなデバイス ID とデバイス値です。 この例では、文字列 ID と数値を出力する何らかのハードウェアをシミュレートしています。

`resources/reader.yaml` トポロジは、Event Hub からデータを読み取り (EventHubWriter によって書き込まれたデータ)、JSON データを解析してから、`deviceId` データと `deviceValue` データを記録します。

データは JSON ドキュメント形式に変更されてから、Event Hub に出力されます。リーダーが読み取るときに、JSON からタプルに解析されます。 JSON 形式は次のとおりです。

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>プロジェクトの構成

`POM.xml` ファイルには、この Maven プロジェクトの構成情報が含まれています。 興味深いのは次の点です。

#### <a name="event-hub-components"></a>Event Hub のコンポーネント

Azure Event Hubs に対する読み書きを行うコンポーネントは、[HDInsight リポジトリ](https://github.com/hdinsight/mvn-rep)にあります。 `POM.xml` ファイルの次のセクションでは、このリポジトリからコンポーネントを読み込みます。

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm スパウトの依存関係

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

この xml では、eventhubs パッケージの依存関係を定義します。これには、Event Hubs からの読み取りに使うスパウトと Event Hubs への書き込みに使うボルトの両方が含まれます。

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

この xml により、プロジェクトは、HDInsight 3.5 以降で使われる Java 8 用の出力を生成するように構成されます。

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

この xml により、ソリューションは、出力を uber jar にパッケージ化するように構成されます。 Jar には、プロジェクト コードと必要な依存関係の両方が含まれます。 また、次の用途にも使用されます。

* 依存関係のライセンス ファイル名を変更する。
* セキュリティ/署名を除外する。
* 同じインターフェイスの複数の実装は、1 つのエントリにマージする必要があります。

このような構成設定により、実行時のエラーを防ぎます。

#### <a name="topology-definitions"></a>トポロジの定義

この例では、[Flux](https://storm.apache.org/releases/1.1.0/flux.html) フレームワークを使います。 このフレームワークでは、YAML を使ってトポロジを定義します。 主な利点は、Java コードでトポロジをハード コーディングする必要がないことです。 定義は YAML であるため、すべてを再コンパイルしなくても、トポロジを送信する前に定義を変更できます。

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Event Hub についてトポロジに通知する

実行時には、`dev.properties` ファイルを使ってトポロジに Event Hub の構成を渡します。 このファイルの既定の内容は、次の例のようになります。

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>環境変数を構成する

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、 `/usr/lib/jvm/java-7-oracle`のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 次のパスを含む必要があります。

  * **JAVA_HOME** または同等のパス
  * **JAVA_HOME\bin** または同等のパス
  * Maven がインストールされているディレクトリ

## <a name="configure-event-hub"></a>Event Hub の構成

Event Hubs は、この例のデータ ソースです。 イベント ハブを作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)から、**[新規作成]** > **[Service Bus]** > **[Event Hub]** > **[カスタム作成]** の順に選択します。

2. **[新しいイベント ハブの追加]** 画面で、**イベント ハブ名**を入力します。 そのハブが作成される **[リージョン]** を選択し、名前空間を作成するか、既存の名前空間を選択します。 最後に、**矢印**をクリックして続行します。

    ![ウィザード ページ 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > 待機時間とコストを削減するために、HDInsight サーバーで使用する Storm と同じ **[場所]** を選択してください。

3. **[Event Hub の構成]** 画面で、**パーティション カウント**と**メッセージ保持**の値を入力します。 この例では、パーティション カウントに 10 を、メッセージ保持に 1 を使用します。 パーティション数を書き留めておきます。この値は後で必要になります。

    ![ウィザード ページ 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. Event Hub が作成されたら、名前空間を選択し、 **[Event Hubs]**を選択して、先ほど作成した Event Hub を選択します。
5. **[構成]** を選択し、次の情報を使用して新しいアクセス ポリシーを 2 つ作成します。

    <table>
    <tr><th>名前</th><th>アクセス許可</th></tr>
    <tr><td>ライター</td><td>送信</td></tr>
    <tr><td>閲覧者</td><td>リッスン</td></tr>
    </table>

    アクセス許可の作成後、ページの下部にある **[保存]** アイコンをクリックします。 これらの共有アクセス ポリシーは、イベント ハブに対する読み取りと書き込みに使用されます。

    ![ポリシー](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. ポリシーの保存後、ページの下部にある **[共有アクセス キー生成コンポーネント]** を使用して、**ライター**と**リーダー**のポリシーのキーを取得します。 これらのキーを保存します。

## <a name="download-and-build-the-project"></a>プロジェクトのダウンロードとビルド

1. GitHub からプロジェクトをダウンロードします ( [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub))。 パッケージを zip アーカイブとしてダウンロードするか、 [git](https://git-scm.com/) を使用してローカルにプロジェクトの複製を作成します。

2. 実際の Event Hub の構成で、`dev.properties` ファイルを変更します。

3. 次のコマンドを使用して、プロジェクトをビルドしてパッケージ化します。

        mvn package

    このコマンドにより、必要な依存関係がダウンロードされ、プロジェクトがビルドされてパッケージ化されます。 出力は **EventHubExample-1.0-SNAPSHOT.jar** というファイル名で **/target** ディレクトリに格納されます。

## <a name="test-locally"></a>ローカルでテストする

これらのトポロジは Event Hubs の読み書きを行うだけなので、[Storm 開発環境](http://storm.apache.org/releases/current/Setting-up-development-environment.html)がある場合は、ローカルにテストできます。 次の手順を使って、開発環境でローカルに実行します。

1. ライターを実行します。

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. リーダーを実行します。

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: トポロジをローカル モード (非分散) で実行します。
> * `-R /writer.yaml`: jar にパッケージ化されている `resources` から、トポロジの定義を読み込みます。 トポロジがローカル ファイル システム上のファイルである場合は、代わりに、最後のパラメーターとしてファイルへのパスを指定します。
> * `--filter dev.properties`: `dev.properties` の内容を使って、トポロジ定義の値を設定します。 たとえば、「 `${eventhub.read.policy.name}`」のように入力します。

ローカルで実行すると、出力はコンソールに対して行われます。 トポロジを停止するには、__Ctrl + C__ キーを押します。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ

1. SCP を使用して、HDInsight クラスターに jar パッケージをコピーします。 USERNAME には、使用するクラスターの SSH ユーザーを指定します。 CLUSTERNAME には、使用する HDInsight クラスター名を指定します。

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 たとえば、 `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    このコマンドにより、ファイルがクラスター上の SSH ユーザーのホーム ディレクトリにコピーされます。

2. ファイルのアップロードが完了したら、SSH を使用して HDInsight クラスターに接続します。 **USERNAME** は、SSH ログイン名に置き換えます。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 次のコマンドを使用して、トポロジを開始します。

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: トポロジを Nimbus サービスに送信します。Nimbus サービスは、クラスター内の worker ノードでトポロジを開始します。

4. 記録されたデータを表示するには、https://CLUSTERNAME.azurehdinsight.net/stormui に移動します。__CLUSTERNAME__ は実際の HDInsight クラスターの名前です。 トポロジを選び、コンポーネントにドリルダウンします。 ログに記録された情報を表示するには、コンポーネントのインスタンスの __port__ エントリを選びます。

5. 次のコマンドを使用してトポロジを停止します。

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

