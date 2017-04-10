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
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fcca957dc365d8c38b5a08991939860c5af96813
ms.lasthandoff: 03/25/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (Java)

HDInsight で Storm を使用して Azure Event Hubs を使用する方法について説明します。 この例では、Java ベースのコンポーネントを使用して、Azure Event Hubs のデータの読み取りと書き込みを行います。

Azure Event Hubs では、Web サイト、アプリ、デバイスで発生する大量のデータを処理できます。 イベント ハブのスパウトにより、HDInsight で Apache Storm を使用してこのデータをリアルタイムで分析することが簡単になります。 また、Event Hubs のボルトを使用して Storm から Event Hub にデータを書き込むこともできます。

## <a name="prerequisites"></a>前提条件

* HDInsight クラスター バージョン 3.5 での Apache Storm。 詳細については、[HDInsight クラスターでの Storm の使用](hdinsight-apache-storm-tutorial-get-started-linux.md)に関する記事を参照してください。
    
    > [!IMPORTANT]
    > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* [Azure Event Hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) バージョン 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) または同等のバージョン ([OpenJDK](http://openjdk.java.net/) など)。

* [Maven](https://maven.apache.org/download.cgi): Maven は Java プロジェクトのプロジェクト ビルド システムです

* テキスト エディターまたは統合開発環境 (IDE)。
  
  > [!NOTE]
  > お使いのエディターまたは IDE には、Maven との操作用の特定の機能が搭載されている場合があります (本ドキュメントではカバーしていません)。 お使いの編集環境の機能に関する詳細は、製品のマニュアルをご覧ください。
  
  * SSH クライアント。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* SCP クライアント。 `scp` コマンドは、Linux、Unix、OS X システム (Bash on Windows 10 を含む) すべてに付属しています。`scp` コマンドがない Windows システムでは、PSCP をお勧めします。 PSCP は、[PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)から入手できます。

## <a name="understanding-the-example"></a>例の説明

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) の例には、2 つのトポロジが含まれています。

**com.microsoft.example.EventHubWriter** は、Azure Event Hub にランダムなデータを書き込みます。 データはスパウトで生成され、ランダムなデバイス ID とデバイス値です。 この例では、文字列 ID と数値を出力する何らかのハードウェアをシミュレートしています。

**com.microsoft.example.EventHubReader** は、イベント ハブからデータを読み取り、そのデータをクラスターの /devicedata ディレクトリにある既定のストレージに格納します。

データは JSON ドキュメント形式に変更されてから、Event Hub に出力されます。リーダーが読み取るときに、JSON からタプルに解析されます。 JSON 形式は次のとおりです。

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>プロジェクトの構成

**POM.xml** ファイルには、この Maven プロジェクトの構成情報が含まれています。 興味深いのは次の点です。

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm スパウトの依存関係

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

この xml では、eventhubs パッケージの依存関係を定義します。これには、Event Hubs からの読み取りに使うスパウトと Event Hubs への書き込みに使うボルトの両方が含まれます。

> [!NOTE]
> このパッケージは、このドキュメントの後半でインストールします。

#### <a name="the-hdfsbolt-and-wasb-components"></a>HdfsBolt および WASB コンポーネント

通常、HdfsBolt は、Hadoop Distributed File System HDFS にデータを格納するために使用されます。 一方、HDInsight クラスターは Azure Storage (WASB) を既定のデータ ストアとして使用するので、HdfsBolt が WASB ファイル システムを理解できるようにするコンポーネントをいくつか読み込む必要があります。

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> 以前のバージョンの HDInsight (バージョン 3.2 など) を使用する場合は、これらのコンポーネントを手動で登録する必要があります。 例については、この例のリポジトリの [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) ブランチを参照してください。

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

これにより、プロジェクトは、HDInsight 3.5 で使用される、Java 8 の出力を生成するように構成されます。

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
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
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

これにより、ソリューションは、出力を uber jar にパッケージ化するように構成されます。 Jar には、プロジェクト コードと必要な依存関係の両方が含まれます。 また、次の用途にも使用されます。

* 依存関係のライセンス ファイル名を変更する。
* セキュリティ/署名を除外する。
* 同じインターフェイスの複数の実装は、1 つのエントリにマージする必要があります。

このような構成設定により、実行時のエラーを防ぎます。

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

この構成により、デプロイ環境のローカルでトポロジを実行することが簡単になります。 次の構文を使用すると、トポロジをローカルで実行できます。

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

たとえば、「`mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`」のように入力します。

#### <a name="the-resources-section"></a>resources セクション

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

この構成では、プロジェクトに含まれている Java 以外のリソースを定義します。 たとえば、**EventHubs.properties** ファイルには、Azure イベント ハブへの接続に使用する情報が含まれています。

## <a name="configure-environment-variables"></a>環境変数を構成する

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、 `/usr/lib/jvm/java-7-oracle`のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 次のパスを含む必要があります。
  
  * **JAVA_HOME** または同等のパス
  * **JAVA_HOME\bin** または同等のパス
  * Maven がインストールされているディレクトリ

## <a name="download-and-register-the-eventhub-components"></a>EventHub コンポーネントのダウンロードと登録

1. [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar) から、`storm-eventhubs-1.0.2-jar-with-dependencies.jar` をダウンロードします。 このファイルには、EventHubs から読み取りと書き込みを行うためのスパウト コンポーネントとボルト コンポーネントが含まれています。

2. 次のコマンドを使用して、コンポーネントをローカルの maven リポジトリに登録します。
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    ダウンロードしたファイルの場所を指すように `-Dfile=` パラメーターを変更します。

    このコマンドにより、ファイルがローカルの Maven リポジトリにインストールされます。この場合、ファイルは、コンパイル時に Maven によって見つかります。

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

2. 次のコマンドを使用して、プロジェクトをビルドしてパッケージ化します。
   
        mvn package
   
    このコマンドにより、必要な依存関係がダウンロードされ、プロジェクトがビルドされてパッケージ化されます。 出力は **EventHubExample-1.0-SNAPSHOT.jar** というファイル名で **/target** ディレクトリに格納されます。

## <a name="deploy-the-topologies"></a>トポロジのデプロイ

このプロジェクトで作成される jar には、**com.microsoft.example.EventHubWriter** と **com.microsoft.example.EventHubReader** という 2 つのトポロジが含まれます。 EventHubWriter トポロジを最初に開始する必要があります。これは、Event Hub にイベントが出力され、その出力が EventHubReader から読み取られるためです。

1. SCP を使用して、HDInsight クラスターに jar パッケージをコピーします。 USERNAME には、使用するクラスターの SSH ユーザーを指定します。 CLUSTERNAME には、使用する HDInsight クラスター名を指定します。
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 たとえば、「 `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`」のように入力します。
   
   > [!NOTE]
   > クライアントが Windows ワークステーションの場合、SCP コマンドがインストールされていない可能性があります。 PSCP をお勧めします。PSCP は [PuTTY のダウンロード ページ](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)からダウンロードできます。
   
    このコマンドにより、ファイルがクラスター上の SSH ユーザーのホーム ディレクトリにコピーされます。

2. ファイルのアップロードが完了したら、SSH を使用して HDInsight クラスターに接続します。 **USERNAME** は、SSH ログイン名に置き換えます。 **CLUSTERNAME** には、HDInsight クラスター名を指定します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合は、 `-i` パラメーターを使用してキー ファイルのパスを指定することが必要な場合があります。 次の例は、`~/.ssh/id_rsa` から秘密キーを読み込みます。
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    PuTTY を使用している場合は、**[ホスト名 (または IP アドレス)]** フィールドに `CLUSTERNAME-ssh.azurehdinsight.net` を入力し、**[開く]** をクリックして接続します。 SSH アカウント名を入力するように求められます。
   
   > [!NOTE]
   > SSH アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。 アカウントで SSH キーを使用した場合、必要に応じて次の手順でキーを選択します。
   > 
   > 1. **[カテゴリ]** で **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。
   > 2. **[参照]** をクリックし、プライベート キーが含まれた .ppk ファイルを選択します。
   > 3. **[開く]** をクリックして接続します。

3. 次のコマンドを使用して、トポロジを開始します。
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    これらのコマンドにより、"reader" および "writer" というフレンドリ名を使用してトポロジが開始されます。

4. トポロジによってデータが生成されるまで少し待ちます。 次のコマンドを使用して、データが HDInsight ストレージに書き込まれていることを確認します。
   
        hadoop fs -ls /devicedata
   
    このコマンドでは、次のテキストのようなファイルの一覧が返されます。
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > 一部のファイルはサイズ 0 です。これは、EventHubReader でファイルが作成されても、まだデータが格納されていないためです。
   
    ファイルの内容を表示するには、次のコマンドを使用します。
   
        hadoop fs -text /devicedata/*.txt
   
    これにより、次のテキストのようなデータが返されます。
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    最初の列にはデバイスの ID 値、2 列目にはデバイス値が含まれます。

5. 次のコマンドを使用してトポロジを停止します。
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>トラブルシューティング

/devicedata ディレクトリ内のファイルが表示されない場合は、Storm UI を使用して、トポロジから返されたエラーを確認します。

Storm UI の詳細な使用方法については、次のトピックを参照してください。

* HDInsight クラスターで **Linux ベース** の Storm を使用している場合は、「 [Deploy and manage Apache Storm topologies on Linux ベース HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

* HDInsight クラスターで **Windows ベース** の Storm を使用している場合は、「 [Deploy and manage Apache Storm topologies on Windows ベース HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>次のステップ

* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)


