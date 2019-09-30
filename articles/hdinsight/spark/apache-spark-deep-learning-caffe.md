---
title: 分散型深層学習用に Azure HDInsight Spark で Caffe を使用する
description: Azure HDInsight での分散型深層学習用に Apache Spark で Caffe を使用する
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: bb234e5b34bd8046c4e65d7cc6812cde0db3b5b2
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995614"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>分散型深層学習用に Azure HDInsight Spark で Caffe を使用する


## <a name="introduction"></a>はじめに

深層学習は、医療、輸送、製造など、あらゆる分野に影響を与えています。 企業は、[画像分類](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/)、[音声認識](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html)、物体認識、機械翻訳などの困難な問題を解決するための手段としてディープ ラーニングを取り入れるようになっています。 

[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/)、[Tensorflow](https://www.tensorflow.org/)、[Apache MXNet](https://mxnet.apache.org/)、Theano など、[多くの一般的なフレームワーク](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software)が存在します。[Caffe](https://caffe.berkeleyvision.org/) は、最も有名な非シンボリック (命令的) ニューラル ネットワーク フレームワークの 1 つであり、コンピューター ビジョンを含む多くの分野で広く使用されています。 さらに、[CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) では、Caffe と Apache Spark を結合して、深層学習を既存の Hadoop クラスターで簡単に使用できるようにしています。 深層学習を Spark ETL パイプラインと一緒に使用して、システムの複雑さと完全なソリューション学習の待機時間を短縮できます。

[HDInsight](https://azure.microsoft.com/services/hdinsight/) は、Apache Spark、Apache Hive、Apache Hadoop、Apache HBase、Apache Storm、Apache Kafka、および ML サービス向けに最適化されたオープンソースの分析クラスターを提供するクラウド Apache Hadoop サービスです。 HDInsight は、99.9% の SLA が保証されています。 これらのビッグ データ テクノロジと ISV アプリケーションはそれぞれ、企業向けのセキュリティ機能と監視機能を備えたマネージド クラスターとして簡単にデプロイ可能です。

この記事では、HDInsight クラスター向けの [Caffe on Spark](https://github.com/yahoo/CaffeOnSpark) をインストールする方法を示します。 この記事では、組み込みの MNIST デモを使用して、CPU で HDInsight Spark を使用する分散型深層学習の使用方法についても示します。

次の 4 つの手順でタスクを実行します。

1. すべてのノードに必要な依存関係をインストールする
2. ヘッド ノードで HDInsight 用 CaffeOnSpark をビルドする
3. すべての worker ノードに必要なライブラリを配布する
4. Caffe モデルを作成し、それを分散して実行する

HDInsight は PaaS ソリューションであるため、優れたプラットフォーム機能が用意されています。したがって、いくつかのタスクを簡単に実行できます。 このブログ記事で使用する機能の 1 つは[スクリプト アクション](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)と呼ばれ、シェル コマンドを実行してクラスター ノード (ヘッド ノード、worker ノード、またはエッジ ノード) をカスタマイズ可能にします。

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>手順 1:すべてのノードに必要な依存関係をインストールする

最初に、依存関係をインストールする必要があります。 Caffe サイトと [CaffeOnSpark サイト](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)に、Spark の依存関係を YARN モードでインストールするために役立つ Wiki が用意されています。 HDInsight では、Spark を YARN モードでも使用します。 ただし、HDInsight プラットフォームの依存関係をいくつか追加する必要があります。 これを行うために、スクリプト アクションを使用し、それをすべてのヘッド ノードと worker ノードで実行します。 これらの依存関係は他のパッケージにも依存するため、このスクリプト アクションの実行には約 20 分かかります。 このスクリプト アクションは、GitHub の場所や既定の BLOB ストレージ アカウントなど、HDInsight クラスターで利用できる場所に配置する必要があります。

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


スクリプト アクションには、2 つの手順が含まれています。 最初の手順では、必要なすべてのライブラリをインストールします。 これらのライブラリには、Caffe のコンパイルに必要なライブラリ (gflags、glog など) と Caffe の実行に必要なライブラリ (numpy など) が含まれています。 ここでは CPU の最適化のために libatlas を使用しています。MKL や CUDA (GPU 用) などの他の最適化ライブラリのインストールについては、必ず CaffeOnSpark Wiki の説明に従ってください。

2 番目の手順では、実行時に Caffe 用の protobuf 2.5.0 をダウンロードし、コンパイルしてインストールします。 protobuf 2.5.0 が[必要](https://github.com/yahoo/CaffeOnSpark/issues/87)です。ただし、このバージョンは Ubuntu 16 のパッケージとして入手できないため、ソース コードからコンパイルする必要があります。 コンパイル方法については、インターネットでもいくつかのリソースが見つかります。 詳細については、[このページ](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html)を参照してください。

作業を開始するには、このスクリプト アクションをクラスターに対して実行して、すべての worker ノードとヘッド ノード (HDInsight 3.5 用) に適用します。 既存のクラスターでスクリプト アクションを実行するか、クラスターの作成時にスクリプト アクションを使用します。 スクリプト アクションの詳細については、[こちらの](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)ドキュメントを参照してください。

![依存関係をインストールするスクリプト アクション](./media/apache-spark-deep-learning-caffe/submit-script-action.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>手順 2:ヘッド ノードで HDInsight 用の Caffe on Apache Spark を構築する

2 番目の手順では、ヘッド ノードで Caffe をビルドし、コンパイルされたライブラリをすべての worker ノードに配布します。 この手順では、[ヘッド ノードに ssh 接続する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)必要があります。 その後で、[CaffeOnSpark ビルド プロセス](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn)に従う必要があります。 いくつかの手順が追加された次のスクリプトを使用して、CaffeOnSpark を構築できます。 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

CaffeOnSpark のドキュメントに記載されていること以外に、いくつかの操作が必要になる場合があります。 変更点は次のとおりです。
- CPU のみに変更し、この特定の目的に libatlas を使用する。
- 後で使用するために、すべての worker ノードからアクセスできる共有の場所である BLOB ストレージにデータセットを配置します。
- コンパイルされた Caffe ライブラリを BLOB ストレージに配置します。追加のコンパイル時間が発生するのを避けるために、後でスクリプト アクションを使用してこれらのライブラリをすべてのノードにコピーします。


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>トラブルシューティング:An Ant BuildException has occured: exec returned: (Ant BuildException が発生しました: exec から返された値:)2

最初に CaffeOnSpark をビルドしようとしたときに次のメッセージが返される場合があります。

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

"make clean" でコード リポジトリをクリーンアップしてから "make build" を実行してください。正しい依存関係があれば、この問題は解決します。

### <a name="troubleshooting-maven-repository-connection-time-out"></a>トラブルシューティング:Maven リポジトリの接続タイムアウト

Maven から次のスニペットのような接続タイムアウト エラーが返される場合があります。

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

数分後に再試行する必要があります。


### <a name="troubleshooting-test-failure-for-caffe"></a>トラブルシューティング:Caffe のテスト エラー

おそらく、CaffeOnSpark の最終チェック時にテスト エラーが表示されます。 このメッセージは UTF-8 エンコードに関連すると考えられますが、Caffe の使用には影響しません。

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>手順 3:すべての worker ノードに必要なライブラリを配布する

次の手順では、ライブラリ (基本的に、CaffeOnSpark/caffe-public/distribute/lib/ と CaffeOnSpark/caffe-distri/distribute/lib/ に含まれるライブラリ) をすべてのノードに配布します。 これらのライブラリは手順 2 で BLOB ストレージに配置しました。この手順では、スクリプト アクションを使用して、すべてのヘッド ノードと worker ノードにライブラリをコピーします。

これを行うには、次のスニペットに示すスクリプト アクションを実行します。

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

クラスターに固有の適切な場所を指定したことを確認してください。

手順 2. ですべてのノードからアクセスできる BLOB ストレージに配置しているため、この手順では、このライブラリをすべてのノードにコピーするだけで構いません。

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>手順 4:Caffe モデルを作成し、それを分散して実行する

上記の手順を実行すると、Caffe がインストールされます。 次の手順では、Caffe モデルを作成します。 

Caffe では "表現的アーキテクチャ" が使用されており、ほとんどの場合、モデルを作成するのにコードを記述する必要がなく、構成ファイルを定義するだけで済みます。 それでは、その手順を説明していきます。 

トレーニングするモデルは、MNIST トレーニングのサンプル モデルです。 手書き数字の MNIST データベースには、60,000 件の例が含まれたトレーニング セットと、10,000 件の例が含まれたテスト セットがあります。 これは、NIST から入手できるより大きなセットのサブセットです。 数字は、サイズが正規化され、固定サイズの画像に中心が揃えられています。 CaffeOnSpark には、データセットをダウンロードして適切な形式に変換するスクリプトが用意されています。

CaffeOnSpark には、MNIST トレーニング用にネットワーク トポロジの例が用意されています。 CaffeOnSpark の設計上の優れた点として、ネットワーク アーキテクチャ (ネットワークのトポロジ) と最適化が分離されています。 この場合、2 つのファイルが必要になります。 

"ソルバー" ファイル (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) は、最適化を監視し、パラメーターの更新を生成するために使用されます。 たとえば、CPU と GPU のどちらを使用するか、モーメンタムは何か、処理を何回繰り返すか、などを定義します。また、プログラムで使用するニューロン ネットワーク トポロジも定義します (これは、必要な 2 番目のファイルです)。 ソルバーの詳細については、[Caffe のドキュメント](https://caffe.berkeleyvision.org/tutorial/solver.html)を参照してください。

この例では、GPU ではなく CPU を使用するため、最後の行を次のように変更する必要があります。

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe 構成 1](./media/apache-spark-deep-learning-caffe/caffe-configuration1.png
)

必要に応じて、他の行を変更してください。

2 番目のファイル (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) では、ニューロン ネットワークの概観、関連する入力ファイル、出力ファイルを定義します。 さらに、トレーニング データの場所を反映するようにファイルを更新する必要があります。 lenet_memory_train_test.prototxt の次の部分を変更します (実際のクラスターに合わせて正しい場所を指定する必要があります)

- "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" を "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb" に変更します。
- "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" を "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb" に変更します。

![Caffe 構成 2](./media/apache-spark-deep-learning-caffe/caffe-configuration2.png)

ネットワークを定義する方法の詳細については、[MNIST データセットに関する Caffe のドキュメント](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)を参照してください。

この記事では、この MNIST 例を使用します。 ヘッド ノードから次のコマンドを実行します。

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

上記のコマンドは、必要なファイル (lenet_memory_solver.prototxt と lenet_memory_train_test.prototxt) を各 YARN コンテナーに配布します。 さらに、このコマンドは、各 Spark ドライバー/実行プログラムの関連パスを LD_LIBRARY_PATH に設定します。 LD_LIBRARY_PATH は、前のコード スニペットで定義された CaffeOnSpark ライブラリがある場所を指します。 

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング

ここでは YARN クラスター モードを使用しているため、Spark ドライバーが任意のコンテナー (と任意のワーカー ノード) にスケジュールされたときに、コンソールには次のような出力しか表示されません。

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

どのような処理が実行されたのかを知りたい場合は、より多くの情報が含まれた Spark ドライバーのログを取得する必要があります。 この場合、関連する YARN ログを見つけるために YARN UI に移動する必要があります。 YARN UI は、次の URL で利用できます。 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN UI](./media/apache-spark-deep-learning-caffe/apache-yarn-window-1.png)

この特定のアプリケーションに割り当てられているリソースの数を確認できます。 [Scheduler (スケジューラ)] リンクをクリックすると、このアプリケーションで 9 個のコンテナーが実行されていることがわかります。 これは、YARN に要求している 8 個の Executor と、ドライバー プロセス用のもう 1 個のコンテナーに該当します。 

![YARN スケジューラ](./media/apache-spark-deep-learning-caffe/apache-yarn-scheduler.png)

エラーが発生している場合は、ドライバーまたはコンテナーのログを確認する必要があります。 ドライバーのログを表示するには、YARN UI のアプリケーション ID をクリックし、[Logs (ログ)] をクリックします。 ドライバーのログが stderr に書き込まれます。

![YARN UI 2](./media/apache-spark-deep-learning-caffe/apache-yarn-window-2.png)

たとえば、ドライバーのログに次のようなエラーが表示されることがあります。これは、あまりにも多くの Executor を割り当てていることを示しています。

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

場合によっては、ドライバーではなく Executor に問題が発生することがあります。 この場合、コンテナーのログを確認する必要があります。 いつでもコンテナー ログを取得し、エラーのあるコンテナーを取得することができます。 たとえば、Caffe を実行しているときに次のエラーが発生する可能性があります。

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

この場合、エラーが発生したコンテナーの ID を取得する必要があります (上記の場合は container_1485916338528_0008_05_000005)。 次に、 

    yarn logs -containerId container_1485916338528_0008_03_000005

をヘッド ノードから実行する必要があります。 コンテナー エラーを確認すると、lenet_memory_solver.prototxt で GPU モードを使用していることが原因でエラーが発生していることがわかります (代わりに CPU モードを使用する必要があります)。

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>結果の取得

割り当てられている Executor は 8 個であり、ネットワーク トポロジも単純であるため、結果は約 30 分で得られます。 コマンド ラインでは、モデルを wasb:///mnist.model に配置し、その結果を wasb:///mnist_features_result という名前のフォルダーに格納することを指定しています。

結果を取得するには、次のように指定します。

    hadoop fs -cat hdfs:///mnist_features_result/*

結果は次のようになります。

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID は MNIST データセットの ID を表し、label はモデルによって識別される番号です。


## <a name="conclusion"></a>まとめ

このドキュメントでは、簡単な例を実行して、CaffeOnSpark をインストールしました。 HDInsight は、完全なマネージド型クラウド分散コンピューティング プラットフォームであり、大規模なデータセットに対して機械学習と高度な分析ワークロードを実行するのに最適な場所です。また、分散型深層学習については、HDInsight Spark で Caffe を使用して、深層学習タスクを実行できます。


## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)

