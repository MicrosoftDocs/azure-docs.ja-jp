---
title: "Azure Cosmos DB: Spark と Apache TinkerPop Gremlin を使用してグラフ分析を実行する | Microsoft Docs"
description: "この記事では、Azure Cosmos DB で Spark と TinkerPop SparkGraphComputer を使用して、グラフ分析と並列計算を設定して実行するための手順を示します。"
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 4f35ed6399df777edd90d1944f22dce91e76952f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/13/2017

---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: Spark と Apache TinkerPop Gremlin を使用してグラフ分析を実行する

[Azure Cosmos DB](introduction.md) は、Microsoft が提供するグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント データベース、キー/値データベース、グラフ データベースを作成したり照会したりすることができます。 Azure Cosmos DB は、[Apache TinkerPop Gremlin](graph-introduction.md) を使用するオンライン トランザクション処理 (OLTP) グラフ ワークロードをサポートします。

[Spark](http://spark.apache.org/) は、汎用目的のオンライン分析処理 (OLAP) データ処理に重点を置いている Apache Software Foundation プロジェクトです。 Spark は、Hadoop MapReduce モデルに似たインメモリ/ディスク ベースのハイブリッド分散コンピューティング モデルを備えています。 Apache Spark は、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) を使用してクラウドにデプロイすることができます。

Azure Cosmos DB と Spark を組み合わせることで、Gremlin を使用するときに OLTP と OLAP の両方を実行することができます。 このクイック スタートの記事では、Azure HDInsight Spark クラスター上の Azure Cosmos DB に対して Gremlin クエリを実行する方法を示します。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下の前提条件を満たしている必要があります。
* Azure HDInsight Spark クラスター 2.0
* JDK 1.8 以降 (JDK がない場合は `apt-get install default-jdk` を実行してください)
* Maven (Maven がない場合は `apt-get install maven` を実行してください)
* An Azure サブスクリプション ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Azure HDInsight Spark クラスターをセットアップする方法の詳細については、[HDInsight クラスターのプロビジョニング](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

## <a name="create-an-azure-cosmos-db-database-account"></a>Azure Cosmos DB データベース アカウントを作成する

まず、次の手順に従って、Graph API を持つデータベース アカウントを作成します。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>コレクションの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>Apache TinkerPop を入手する

次の手順に従って、Apache TinkerPop を取得します。

1. HDInsight クラスターのマスター ノードにリモート接続します。`ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`

2. TinkerPop3 のソース コードを複製してローカルでビルドした後、Maven キャッシュにインストールします。

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Spark-Gremlin プラグインをインストールします。 

    a. プラグインのインストールは Grape によって処理されます。 Grape がプラグインとその依存関係をダウンロードできるように、Grape のレポジトリ情報を入力します。 

      grape 構成ファイルが `~/.groovy/grapeConfig.xml` に存在しない場合は、構成ファイルを作成します。 次の設定を使用します。

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Gremlin コンソールを起動します。`bin/gremlin.sh`
        
    c. 前の手順でビルドしたバージョン 3.3.0-SNAPSHOT を含む Spark-Gremlin プラグインをインストールします。

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. `Hadoop-Gremlin` が `:plugin list` でアクティブになっているかどうかを調べます。 このプラグインは Spark Gremlin プラグインに干渉する可能性があるため、無効にします。`:plugin unuse tinkerpop.hadoop`

## <a name="prepare-tinkerpop3-dependencies"></a>TinkerPop3 の依存関係を準備する

先ほどの手順で TinkerPop3 をビルドすると、そのプロセスによって、Spark と Hadoop に必要なすべての jar の依存関係も target ディレクトリにプルされます。 HDI と共にプレインストールされる jar を使用し、他の依存関係は必要な場合のみプルします。

1. Gremlin コンソールの target ディレクトリに移動します。`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone` 

2. `ext/` にあるすべての jar を `lib/` に移動します。`find ext/ -name '*.jar' -exec mv {} lib/ \;`

3. `lib/` から、次のリストに含まれていない jar ライブラリをすべて削除します。

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Azure Cosmos DB Spark コネクタを入手する

1. Azure Cosmos DB Spark コネクタ `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` と Cosmos DB Java SDK `azure-documentdb-1.10.0.jar` を [GitHub の Azure Cosmos DB Spark コネクタ](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11)のページから入手します。

2. または、ローカルでビルドしてもかまいません。 最新バージョンの Spark-Gremlin は Spark 1.6.1 でビルドされており、現在 Azure Cosmos DB Spark コネクタで使われている Spark 2.0.2 とは互換性がないため、手動で最新の TinkerPop3 のコードをビルドして jar をインストールできます。 以下の手順を実行します。

    a. Azure Cosmos DB Spark コネクタを複製します。

    b. TinkerPop3 をビルドします (先ほど手順で既に実行済み)。 TinkerPop 3.3.0-SNAPSHOT のすべての jar をローカルにインストールします。

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. `tinkerpop.version` `azure-documentdb-spark/pom.xml` を `3.3.0-SNAPSHOT` に更新します。
    
    d. Maven でビルドします。 必要な jar が `target` と `target/alternateLocation` に配置されます。

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 前述の jar をローカル ディレクトリ (~/azure-documentdb-spark) にコピーします。

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Spark ワーカー ノードに依存関係を配布する 

1. グラフ データの変換には TinkerPop3 が使用されるため、関連する依存関係をすべての Spark ワーカー ノードに配布する必要があります。

2. 次の手順に従って、前述の Gremlin の依存関係、CosmosDB Spark コネクタの jar、および CosmosDB Java SDK をワーカー ノードにコピーします。

    a. すべての jar を `~/azure-documentdb-spark` にコピーします。

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. すべての Spark ワーカー ノードの一覧を取得します。これは、Ambari Dashboard の `Spark2` セクション内の `Spark2 Clients` リストで確認できます。

    c. そのディレクトリを各ノードにコピーします。

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>環境変数を設定する

1. Spark クラスターの HDP バージョンを見つけます。 それは `/usr/hdp/` の下のディレクトリの名前です (例: 2.5.4.2-7)。

2. すべてのノードの hdp.version を設定します。 Ambari Dashboard で、**YARN セクション** > **[Configs\(構成\)]** > **[Advanced\(詳細\)]** に移動し、次の操作を行います。 
 
    a. `Custom yarn-site` に新しいプロパティ `hdp.version` を追加し、マスター ノードの HDP バージョンの値を指定します。 
     
    b. 構成を保存します。 警告が表示されますが、無視してかまいません。 
     
    c. 通知アイコンの表示に従って YARN サービスと Oozie サービスを再起動します。

3. マスター ノードで次の環境変数を設定します (値は適宜置き換えてください)。

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>グラフの構成を準備する

1. Azure Cosmos DB の接続パラメーターと Spark の設定を含む構成ファイルを作成し、`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties` に配置します。

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. `spark.driver.extraClassPath` と `spark.executor.extraClassPath` を更新して、先ほどの手順で配布した jar のディレクトリを含めます (この場合は `/home/sshuser/azure-documentdb-spark/*`)。

3. Azure Cosmos DB の次の詳細を指定します。

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>TinkerPop グラフを読み込んで Azure Cosmos DB に保存する
Azure Cosmos DB にグラフを保存する方法を示すため、この例では、TinkerPop によってあらかじめ定義された TinkerPop modern グラフを使用します。 このグラフは、Kryo 形式で保存され、TinkerPop レポジトリに用意されます。

1. ここでは Gremlin を YARN モードで実行するため、グラフ データを Hadoop ファイル システムで利用できるようにする必要があります。 次のコマンドを使用してディレクトリを作成し、ローカル グラフ ファイルをそこにコピーします。 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. グラフの読み取りに `GryoInputFormat` を使用するように `gremlin-spark.properties` ファイルを一時的に更新します。 また、次のように、`inputLocation` を作成するディレクトリを示すように設定します。

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Gremlin コンソールを起動し、構成済みの Azure Cosmos DB コレクションにデータを永続化する次の計算処理手順を作成します。  

    a. グラフを作成します。`graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`

    b. 書き込みには SparkGraphComputer を使用します。`graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. データ エクスプローラーで、データが Azure Cosmos DB に保存されていることを確認できます。

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Azure Cosmos DB からグラフを読み込んで Gremlin クエリを実行する

1. グラフを読み込むには、`gremlin-spark.properties` を編集して、`graphReader` を `DocumentDBInputRDD` に設定します。

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 次の手順に従って、グラフを読み込んでデータを走査し、Gremlin クエリを実行します。

    a. Gremlin コンソールを起動します。`bin/gremlin.sh`

    b. 必要な構成でグラフを作成します。`graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`

    c. SparkGraphComputer でグラフ走査を作成します。`g = graph.traversal().withComputer(SparkGraphComputer)`

    d. 次の Gremlin グラフのクエリを実行します。

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> より詳細なログを表示出力するには、`conf/log4j-console.properties` でログの詳細レベルを引き上げてください。
>

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB と Spark を組み合わせてグラフを操作する方法について説明しました。

> [!div class="nextstepaction"]

