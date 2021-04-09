---
title: Python コンポーネントを使用した Apache Storm - Azure HDInsight
description: Azure HDInsight で、Python コンポーネントを使用する Apache Storm トポロジを作成する方法について説明する
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: e28d21ed71cf5f485165c639a8bd519b3a2736e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928990"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight での Python を使用した Apache Storm トポロジの開発

Python コンポーネントを使用する [Apache Storm](https://storm.apache.org/) トポロジを作成する方法について説明します。 Apache Storm では複数の言語がサポートされています。これにより、1 つのトポロジでの複数の言語からのコンポーネントの結合も可能になります。 [Flux](https://storm.apache.org/releases/current/flux.html) フレームワーク (Storm 0.10.0 で導入) では、Python コンポーネントを使用するソリューションを簡単に作成することができます。

> [!IMPORTANT]  
> このドキュメントの情報は、HDInsight 3.6 で Storm を使用してテストされました。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Storm クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照し、 **[クラスターの種類]** で **[Storm]** を選択してください。

* ローカルの Storm 開発環境 (省略可能)。 ローカルの Storm 環境は、トポロジをローカルで実行する場合にのみ必要です。 詳細については、「[Setting up a development environment (開発環境のセットアップ)](https://storm.apache.org/releases/current/Setting-up-development-environment.html)」を参照してください。

* [Python 2.7 以上](https://www.python.org/downloads/)。

* [Java Developer キット](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK) バージョン 8

* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。

## <a name="storm-multi-language-support"></a>Storm の複数言語サポート

Apache Storm は、任意のプログラミング言語で記述されたコンポーネントで動作するように設計されました。 コンポーネントは、 Storm の Thrift 定義の操作方法を理解する必要があります。 Python では、Storm と簡単に連動できるようにするための Apache Storm プロジェクトの一部として、モジュールが提供されます。 このモジュールは [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) にあります。

Storm は、Java 仮想マシン (JVM) で実行される Java プロセスです。 他の言語で書かれたコンポーネントは、サブプロセスとして実行されます。 Storm は、stdin/stdout 経由で送信される JSON メッセージを使用して、これらのサブプロセスと通信します。 コンポーネント間の通信の詳細については、 [多言語プロトコル](https://storm.apache.org/releases/current/Multilang-protocol.html) に関するドキュメントを参照してください。

## <a name="python-with-the-flux-framework"></a>Python と Flux フレームワーク

Flux フレームワークでは、コンポーネントとは別に Storm トポロジを定義することができます。 Flux フレームワークでは、Storm トポロジの定義に YAML を使用します。 次のテキストは、 YAML ドキュメントでの Python コンポーネントの参照方法の例を示しています。

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

`FluxShellSpout` クラスは、スパウトを実装する `sentencespout.py` スクリプトを開始するために使用されます。

Flux では、Python スクリプトは、トポロジを含む jar ファイル内の `/resources` ディレクトリにあることを予期しています。 そのため、この例では、Python スクリプトを `/multilang/resources` ディレクトリに格納しています。 `pom.xml` では、次の XML を使用してこのファイルをインクルードします。

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

前述したように、Storm の Thrift 定義を実装する `storm.py` ファイルがあります。 Flux フレームワークでは、`storm.py` はプロジェクトのビルド時に自動的にインクルードされるため、気にする必要はありません。

## <a name="build-the-project"></a>プロジェクトのビルド

1. [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) からプロジェクトをダウンロードします。

1. コマンド プロンプトを開き、プロジェクト ルートに移動します (`hdinsight-python-storm-wordcount-master`)。 次のコマンドを入力します。

    ```cmd
    mvn clean compile package
    ```

    このコマンドは、コンパイル済みのトポロジを含む `target/WordCount-1.0-SNAPSHOT.jar` ファイルを作成します。

## <a name="run-the-storm-topology-on-hdinsight"></a>HDInsight で Storm トポロジを実行する

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して、HDInsight クラスターの Storm に `WordCount-1.0-SNAPSHOT.jar` ファイルをコピーします。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. ファイルのアップロードが完了したら、SSH を使用してクラスターに接続します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから、次のコマンドを使用して、クラスターでトポロジを開始します。

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Storm トポロジは、開始すると停止するまで実行されます。

1. Storm UI を使用して、クラスターのトポロジを表示します。 Storm UI は `https://CLUSTERNAME.azurehdinsight.net/stormui` にあります。 `CLUSTERNAME` をクラスター名に置き換えます。

1. Storm トポロジを停止します。 次のコマンドを使用して、クラスターのトポロジを停止します。

    ```bash
    storm kill wordcount
    ```

    別の方法として、Storm UI を使用することもできます。 トポロジの **[トポロジのアクション]** で、**[強制終了]** を選択します。

## <a name="run-the-topology-locally"></a>トポロジをローカルで実行する

トポロジをローカルで実行するには、次のコマンドを使用します。

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> このコマンドは、ローカルの Storm 開発環境を必要とします。 詳細については、「[Setting up a development environment (開発環境のセットアップ)](https://storm.apache.org/releases/current/Setting-up-development-environment.html)」を参照してください。

トポロジが開始されると、次のような情報がローカル コンソールに出力されます。

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

トポロジを停止するには、__Ctrl + C__ を押します。

## <a name="next-steps"></a>次のステップ

HDInsight で Python を使用するその他の方法については、次のドキュメントを参照してください: [Apache Pig と Apache Hive での Python ユーザー定義関数 (UDF) の使用方法](../hadoop/python-udf-hdinsight.md)に関するドキュメント。