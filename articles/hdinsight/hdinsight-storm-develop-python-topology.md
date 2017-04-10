---
title: "HDinsight での Storm トポロジの Python コンポーネントの使用 |Microsoft Docs"
description: "Azure HDInsight で Apache Storm から Python コンポーネントを使用する方法について説明します。 Java ベースと Clojure ベースの両方の Storm トポロジから Python コンポーネントを使用する方法を学習します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8b32aa77e1dbe18076d73e10914b59be107c3588
ms.lasthandoff: 03/25/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight での Python を使用した Apache Storm トポロジの開発

Apache Storm では複数の言語がサポートされています。これにより、1 つのトポロジでの複数の言語からのコンポーネントの結合も可能になります。 このドキュメントでは、HDInsight での Java および Clojure ベース Storm トポロジにおける Python コンポーネントの使用方法を学習します。

> [!IMPORTANT]
> このドキュメントでは、Windows ベースと Linux ベースの HDInsight クラスターの両方を使用する方法を説明します。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Python 2.7 以上
* Java JDK 1.7 以上
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Storm の複数言語サポート

Storm は任意のプログラミング言語を使用して記述されたコンポーネントで使用するために設計されていますが、その場合、コンポーネントが [Storm の Thrift 定義](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)の使用方法を理解している必要があります。 Python では、Storm と簡単に連動できるようにするための Apache Storm プロジェクトの一部として、モジュールが提供されます。 このモジュールは [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)にあります。

Apache Storm は Java 仮想マシン (JVM) で実行される Java プロセスであるために、その他の言語で記述されたコンポーネントはサブプロセスとして実行されます。 JVM で実行されている Storm ビットは、stdin/stdout 経由で送信された JSON メッセージを使用してこれらのサブプロセスと通信します。 コンポーネント間の通信の詳細については、 [多言語プロトコル](https://storm.apache.org/documentation/Multilang-protocol.html) に関するドキュメントを参照してください。

### <a name="the-storm-module"></a>Storm モジュール
Storm モジュール (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) は、Storm と連携する Python のコンポーネントを作成するために必要なビットを提供します。

これにより、タプルを出力するための `storm.emit` やログに書き込むための `storm.logInfo` などが提供されます。 このファイルに目を通し、提供内容を理解することをお勧めします。

## <a name="challenges"></a>課題
**storm.py** モジュールを使用して、データを使用する Python スパウトやデータを処理するボルトを作成できますが、コンポーネント間の通信を関連付ける Storm トポロジ定義全体は引き続き Java または Clojure を使用して書き込まれます。 さらに、Java を使用する場合は、Python コンポーネントへのインターフェイスとして機能する Java コンポーネントを作成もする必要もあります。

また、Storm クラスターは分散環境で実行されるため、Python コンポーネントで必要なすべてのモジュールが、クラスター内のすべての worker ノードで使用できることを確認する必要があります。 Storm の場合、多言語リソースではこれを容易に行うことはできません。つまり、トポロジの jar ファイルの一部として依存関係をすべて含めるか、クラスター内の各 worker ノードに依存関係を手動でインストールする必要があります。

### <a name="java-vs-clojure-topology-definition"></a>Java とClojure トポロジの定義
トポロジを定義する 2 つの方法の 1 つである Clojure は、トポロジ定義で Python コンポーネントを直接参照できるため、非常に簡単でわかりやすい方法です。 Java ベースのトポロジ定義の場合、Python コンポーネントから返されるタプルでのフィールド宣言などを処理する Java コンポーネントを定義する必要もあります。

このドキュメントでは両方の方法について説明し、プロジェクト例も示します。

## <a name="python-components-with-a-java-topology"></a>Java トポロジを使用する Python コンポーネント
> [!NOTE]
> この例は、 **JavaTopology** ディレクトリの [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) にあります。 これは Maven ベースのプロジェクトです。 Maven に慣れていない場合は、Storm トポロジの Maven プロジェクトの作成の詳細について、 [HDInsight での Apache Storm を使用する Java ベース トポロジの開発](hdinsight-storm-develop-java-topology.md) に関するページを参照してください。
> 
> 

Python (または他の JVM 言語コンポーネント) を使用する Java ベース トポロジは最初は Java コンポーネントを使用しているように思えますが、各 Java スパウト/ボルトを見ると、コードは次のようになっています。

    public SplitBolt() {
        super("python", "countbolt.py");
    }

この場合、Java は Python を呼び出し、実際のボルト ロジックを含むスクリプトを実行します。 Java スパウト/ボルト (この例の場合) は、基になる Python コンポーネントによって出力されるタプルでフィールドを宣言するだけです。

この例では、実際の Python ファイルは `/multilang/resources` ディレクトリに格納されます。 次のように、 `/multilang` ディレクトリは **pom.xml**で参照されます。

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

これには、このプロジェクトから構築される jar の `/multilang` フォルダー内のファイルがすべて含まれます。

> [!IMPORTANT]
> ここでは、`/multilang/resources` ではなく、`/multilang` ディレクトリのみが指定されることに注意してください。 Storm には `resources` ディレクトリの JVM 以外のリソースが必要であるため、内部的に既に検索されています。 このフォルダーにコンポーネントを配置することで、Java コードでは名前での参照のみが可能になります。 たとえば、「`super("python", "countbolt.py");`」のように入力します。 考えてみると、Storm が多言語リソースへのアクセス時に `resources` ディレクトリをルート (/) と見なすようにする別の方法もあります。
> 
> このプロジェクト例の場合、`storm.py` モジュールは `/multilang/resources` ディレクトリに含まれます。
> 
> 

### <a name="build-and-run-the-project"></a>プロジェクトをビルドして実行する
このプロジェクトをローカルで実行する場合は、単に以下の Maven コマンドを使用して、ローカル モードでビルドおよび実行します。

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

プロセスを強制終了するには、Ctrl キーを押しながら C キーを押します。

Apache Storm を実行して HDInsight クラスターにプロジェクトをデプロイするには、次の手順を使用します。

1. 以下を使用して、uber jar をビルドします。
   
        mvn package
   
    これで、このプロジェクトの `/target` ディレクトリに **WordCount--1.0-SNAPSHOT.jar** という名前のファイルが作成されます。
2. 次のいずれかの方法を使用して、Hadoop クラスターに jar ファイルをアップロードします。
   
   * **Linux ベース** HDInsight クラスターの場合: `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` を使用して、jar ファイルをクラスターにコピーします。その場合、USERNAME を SSH 名に、CLUSTERNAME を HDInsight クラスター名に置き換えます。
     
       ファイルのアップロードが完了したら、SSH を使用してクラスターに接続し、 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * **Windows ベース** HDInsight クラスターの場合: ブラウザーで HTTPS://CLUSTERNAME.azurehdinsight.net/ に移動し、Storm ダッシュボードに接続します。 CLUSTERNAME を HDInsight クラスター名に置き換え、プロンプトが表示されたら、管理者の名前とパスワードを入力します。
     
       フォームを使用して、以下のアクションを実行します。
     
     * **Jar ファイル**: **[参照]** を選択してから **WordCount-1.0-SNAPSHOT.jar** ファイルを選択します。
     * **クラス名**: 「`com.microsoft.example.WordCount`」と入力します。
     * **追加パラメーター**: トポロジを識別するための `wordcount` などのわかりやすい名前を入力します。
       
       最後に、 **[送信]** を選択してトポロジを開始します。

> [!NOTE]
> Storm トポロジは、開始すると停止 (強制終了) するまで実行されます。トポロジを停止するには、コマンドラインから `storm kill TOPOLOGYNAME` コマンドを使用するか (Linux クラスターへの SSH セッションなどの場合)、Storm UI を使用して、トポロジを選択してから **[強制終了]** ボタンをクリックします。
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>Clojure トポロジを使用する Python コンポーネント
> [!NOTE]
> この例は、 [ClojureTopology](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) ディレクトリの **https://github.com/Azure-Samples/hdinsight-python-storm-wordcount** にあります。
> 
> 

このトポロジは、[新しい Clojure プロジェクトを作成](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)するための [Leiningen](http://leiningen.org) を使用して作成されたものです。 その後、スキャフォールディングされたプロジェクトには次の変更が加えられました。

* **project.clj**: Storm の依存関係が追加され、HDInsight サーバーへのデプロイ時に問題が生じる可能性がある項目は除外されました。
* **resources/resources**: Leiningen は既定の `resources` ディレクトリを作成しますが、ここに格納されたファイルはこのプロジェクトから作成された jar ファイルのルートに追加されるように見え、Storm は `resources` という名前のサブディレクトリのファイルを予期します。 したがって、サブディレクトリが追加され、Python ファイルは `resources/resources`に格納されています。 実行時に、これは Phthon コンポーネントにアクセスするためのルート (/) として扱われます。
* **src/wordcount/core.clj**: このファイルはトポロジ定義を含み、**project.clj** ファイルから参照されます。 Storm トポロジを定義するための Clojure の使用に関する詳細については、「[Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)」を参照してください。

### <a name="build-and-run-the-project"></a>プロジェクトをビルドして実行する
**プロジェクトをローカルでビルドして実行するには**、次のコマンドを使用します。

    lein clean, run

トポロジを停止するには、 **Ctrl キーを押しながら C キーを押します**。

**uberjar をビルドして、HDInsight にデプロイするには**、次の手順を使用します。

1. 以下のコマンドを使用して、トポロジと必要な依存関係を含む uberjar を作成します。
   
        lein uberjar
   
    これで、 `wordcount-1.0-SNAPSHOT.jar` ディレクトリの `target\uberjar+uberjar` にあります。
2. 以下のいずれかの方法を使用し、HDInsight クラスターにトポロジをデプロイして実行します。
   
   * **Linux ベースの HDInsight**
     
     1. `scp`を使用して、HDInsight クラスターのヘッド ノードにファイルをコピーします。 For example:
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         USERNAME は HDInsight クラスターの SSH ユーザーに、CLUSTERNAME は HDInsight クラスター名に置き換えます。
     2. ファイルがクラスターにコピーされたら、SSH を使用してクラスターに接続し、ジョブを送信します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
     
     3. 接続したら、次のコマンドを使用してトポロジを開始します。
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **Windows ベースの HDInsight**
     
     1. ブラウザーで HTTPS://CLUSTERNAME.azurehdinsight.net/ に移動し、Storm ダッシュボードに接続します。 CLUSTERNAME を HDInsight クラスター名に置き換え、プロンプトが表示されたら、管理者の名前とパスワードを入力します。
     2. フォームを使用して、以下のアクションを実行します。
        
        * **Jar ファイル**: **[参照]** を選択してから **wordcount-1.0-SNAPSHOT.jar** ファイルを選択します。
        * **クラス名**: 「`wordcount.core`」と入力します。
        * **追加パラメーター**: トポロジを識別するための `wordcount` などのわかりやすい名前を入力します。
          
          最後に、 **[送信]** を選択してトポロジを開始します。

> [!NOTE]
> Storm トポロジは、開始すると停止 (強制終了) するまで実行されます。トポロジを停止するには、コマンドラインから `storm kill TOPOLOGYNAME` コマンドを使用するか (Linux クラスターへの SSH セッション)、Storm UI を使用して、トポロジを選択してから **[強制終了]** ボタンをクリックします。
> 
> 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Storm トポロジから Python コンポーネントを使用する方法を学習しました。 HDInsight で Phthon を使用する他の方法については、次のドキュメントを参照してください。

* [HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)
* [HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)


