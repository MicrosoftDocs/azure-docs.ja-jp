---
title: "Azure HDInsight での R Server の概要 | Microsoft Docs"
description: "HDInsight での R Server の概要を説明します。 R Server を使用してビッグ データ分析用のアプリケーションを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: cd57f3a43142b3af3546eafd9749123fadd333c2
ms.openlocfilehash: 2f5cf1155f116b22fa28cf86bb8c87667df981b7
ms.lasthandoff: 01/14/2017


---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight での R Server とオープン ソース R の機能の概要

Microsoft Azure HDInsight では、Azure で HDInsight クラスターを作成する際に、オプションとして Microsoft R Server を使用できるようになりました。 この新しい機能により、データ サイエンティスト、統計学者、R プログラマは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。

クラスターは、現在のプロジェクトやタスクに合わせてサイズを設定し、不要になったら破棄できます。 クラスターは Azure HDInsight の一部であるため、エンタープライズ レベルの 24 時間 365 日のサポートと稼働時間 99.9% の SLA が提供され、Azure エコシステムの他のコンポーネントとも柔軟に統合できます。

HDInsight の R Server は、Azure Blob Storage または Data Lake Storage に読み込まれた事実上すべてのサイズのデータセットで R ベースの分析を行うための最新の機能を備えています。 R Server はオープン ソースの R を基盤としているため、自社で構築する R ベースのアプリケーションでは、8000 以上のオープン ソース R パッケージと、R Server に付属する Microsoft のビッグ データ分析パッケージである ScaleR のルーチンを活用できます。

クラスターのエッジ ノードは、クラスターへの接続と R スクリプトの実行に便利な場所です。 エッジ ノードでは、エッジ ノード サーバーのコア間で、ScaleR の並列化された分散関数を実行できます。 また、ScaleR の Hadoop Map Reduce または Spark コンピューティング コンテキストを使用して、クラスターのノード間でこれらの関数を実行することもできます。

分析によって得られたモデルや予測は、ダウンロードしてオンプレミスで使用できます。 また、[Azure Machine Learning Studio](http://studio.azureml.net) [Web サービス](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)などを使用して、Azure の別の場所で運用化することもできます。

## <a name="get-started-with-r-on-hdinsight"></a>HDInsight での R の使用
HDInsight クラスターに R Server を含めるには、Azure ポータルを使用して HDInsight クラスターを作成するときに、R Server クラスター タイプを選択する必要があります。 この R Server クラスター タイプは、クラスターのデータ ノード上の R Server と、R Server ベースの分析用のランディング ゾーンとしてのエッジ ノードを組み込みます。 クラスター作成の詳細な手順については、 [HDInsight での R Server の使用](hdinsight-hadoop-r-server-get-started.md) に関する記事をご覧ください。

## <a name="learn-about-data-storage-options"></a>データ ストレージ オプションについて
HDInsight クラスターの HDFS ファイル システムの既定のストレージを、Azure Storage アカウントまたは Azure Data Lake Store と関連付けることができます。 これにより、分析中にクラスター ストレージにアップロードされるデータがどのようなものであっても、確実に永続化されます。 選択したストレージ オプションにデータを転送するためのさまざまなツールが用意されています。これには、ストレージ アカウントのポータル ベースのアップロード機能や [AzCopy](../storage/storage-use-azcopy.md) ユーティリティなどがあります。

クラスターのプライマリ ストレージとして Azure Blob または Data Lake のいずれを選択した場合でも、クラスター プロビジョニング プロセス中に追加の Blob および Data Lake Store へのアクセスを追加することができます。 追加アカウントへのアクセスの追加について詳しくは、「[HDInsight での R Server の使用](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-get-started)」を、複数のストレージ アカウントの使用について詳しくは、補足記事「[HDInsight の R Server 向けの Azure Storage オプション](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-storage)」をご覧ください。

エッジ ノードで使用するストレージ オプションとして、 [Azure Files](../storage/storage-how-to-use-files-linux.md) を使用することもできます。 Azure Files では、Azure Storage で作成されたファイル共有を Linux ファイル システムにマウントできます。 HDInsight クラスターの R Server 用データ ストレージ オプションの詳細については、「[HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)」をご覧ください。

## <a name="access-r-server-on-the-cluster"></a>クラスターでの R Server へのアクセス
R Server を含むクラスターを作成したら、プロビジョニング処理時に RStudio Server を含めることを選択した場合、RStudio Server を後で追加した場合、または SSH/PuTTY を使用して R コンソールにアクセスすることで、ブラウザーを使用してエッジ ノードの R Server に接続できます。 クラスターの作成後に RStudio Server をインストールする方法ついて詳しくは、[HDInsight クラスターへの RStudio Server のインストール](hdinsight-hadoop-r-server-install-r-studio.md)に関する記事をご覧ください。   

## <a name="develop-and-run-r-scripts"></a>R スクリプトの開発と実行
ユーザーが作成して実行する R スクリプトでは、ScaleR ライブラリの並列化された分散ルーチンに加え、8000 以上のオープン ソース R パッケージを使用できます。 一般に、エッジ ノードの R Server で実行されるスクリプトは、そのノードの R インタープリター内で実行されます。 コンピューティング コンテキストが Hadoop Map Reduce (RxHadoopMR) または Spark (RxSpark) に設定された ScaleR 関数を呼び出すステップは例外です。

この場合、関数は参照先データに関連付けられたクラスターのデータ (タスク) ノード間に分散した形で実行されます。 各種コンピューティング コンテキスト オプションの詳細については、「[HDInsight の R Server の計算コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)」を参照してください。

## <a name="operationalize-a-model"></a>モデルの運用化
データ モデリングが完了したら、モデルを運用化して、Azure とオンプレミスの両方で新しいデータについての予測を行うことができます。 このプロセスはスコア付けと呼ばれます。 次に例をいくつか示します。

### <a name="score-in-hdinsight"></a>HDInsight でのスコア付け
HDInsight でスコア付けを行うには、モデルを呼び出して、ストレージ アカウントに読み込んだ新しいデータ ファイルについて予測を行う R 関数を作成します。 その後、予測をストレージ アカウントに保存します。 このルーチンは、クラスターのエッジ ノードでオンデマンドで実行することも、スケジュールされたジョブを使用して実行することもできます。  

### <a name="score-in-azure-machine-learning"></a>Azure Machine Learning でのスコア付け
Azure Machine Learning Web サービスを使用してスコア付けを行うには、 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) と呼ばれるオープン ソースの Azure Machine Learning R パッケージを使用して、モデルを Azure Web サービスとして発行します。 便宜上、このパッケージはエッジ ノードにあらかじめインストールされています。 次に、Machine Learning の機能を使用して Web サービスのユーザー インターフェイスを作成し、スコア付けが必要なときに Web サービスを呼び出します。

この方法を選択した場合、ScaleR モデル オブジェクトを、Web サービスで使用する同等のオープン ソース モデル オブジェクトに変換する必要があります。 この変換は、ScaleR 強制型変換関数 (アンサンブルベースのモデルの `as.randomForest()` など) を使用して実行できます。

### <a name="score-on-premises"></a>オンプレミスのスコア付け
モデルの作成後にオンプレミスのスコア付けを行うには、R でモデルをシリアル化し、モデルをダウンロードして逆シリアル化してから、新しいデータのスコア付けに使用します。 新しいデータのスコア付けは、「[HDInsight でのスコア付け](#scoring-in-hdinsight)」で前述した方法を使用するか、[DeployR](https://deployr.revolutionanalytics.com/) を使用して実行できます。

## <a name="maintain-the-cluster"></a>クラスターの管理
### <a name="install-and-maintain-r-packages"></a>R パッケージのインストールと管理
使用する R パッケージのほとんどは、R スクリプトのほとんどの手順が実行されるエッジ ノードに配置する必要があります。 エッジ ノードに追加の R パッケージをインストールするには、R の通常の `install.packages()` メソッドを使用します。

ほとんどの場合、クラスターで ScaleR ライブラリのルーチンを使用するだけであれば、データ ノードに追加の R パッケージをインストールする必要はありません。 ただし、データ ノードで **rxExec** または **RxDataStep** 実行の使用をサポートするために、追加のパッケージが必要になる場合があります。

このような場合、クラスターの作成後にスクリプト アクションを使用して追加のパッケージをインストールできます。 詳細については、 [R Server を含む HDInsight クラスターの作成](hdinsight-hadoop-r-server-get-started.md)に関する記事をご覧ください。   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Hadoop Map Reduce のメモリ設定の変更
クラスターに変更を加えることで、Map Reduce ジョブの実行時に R Server で使用可能なメモリ容量を変更できます。 クラスターを変更するには、クラスターの Azure ポータル ブレードで使用できる Apache Ambari UI を使用します。 クラスターの Ambari UI にアクセスする手順については、「 [Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

次のように、 **RxHadoopMR** の呼び出しで Hadoop スイッチを使用して、R Server で使用可能なメモリ容量を変更することもできます。

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>クラスターのスケーリング
既存のクラスターは、ポータルを使用してスケールアップまたはスケールダウンできます。 スケーリングにより、大規模な処理タスクに必要な追加容量が得られます。また、クラスターがアイドル状態のときに、クラスターを縮小することもできます。 クラスターのスケーリングの手順については、[HDInsight クラスターの管理](hdinsight-administer-use-portal-linux.md)に関する記事をご覧ください。

### <a name="maintain-the-system"></a>システムのメンテナンス
OS 修正プログラムや他の更新プログラムを適用するために、HDInsight クラスターの基になる Linux VM で時間外にメンテナンスが実施されます。 通常、メンテナンスは毎週月曜日と木曜日の午前 3 時 30 分 (VM のローカル時刻に基づく) に実施されます。 更新は、4 分の&1; 以上のクラスターに同時に影響を与えないような方法で実行されます。  

ヘッド ノードが冗長化されており、すべてのデータ ノードが影響を受けるわけではないため、この期間に実行中のジョブは速度が低下する場合がありますが、 完了するまで引き続き実行されます。 クラスターの再構築を必要とする致命的な障害が発生しない限り、カスタム ソフトウェアやローカル データはメンテナンス イベントで保持されます。

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>HDInsight クラスターの R Server の IDE オプションについて
HDInsight クラスターの Linux エッジ ノードは、R ベースの分析のランディング ゾーンです。  HDInsight の最新バージョンには、ブラウザー ベースの IDE としてエッジ ノードに [RStudio Server](https://www.rstudio.com/products/rstudio-server/) の Community バージョンをインストールするための既定のオプションが備わっています。 R スクリプトの開発と実行に IDE として RStudio Server を使用すると、単に R コンソールを使用した場合よりも生産性を大幅に高めることができます。 クラスターの作成時に RStudio Server を追加せずに、後で追加することを選択した場合は、[HDInsight クラスターへの RStudio Server のインストール](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio)に関する記事をご覧ください。

別の完全な IDE オプションとして、デスクトップ IDE (マイクロソフトが先頃発表した [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS)、RStudio、Walware の Eclipse ベースの [StatET](http://www.walware.de/goto/statet) など) をインストールして、リモート Map Reduce または Spark コンピューティング コンテキストを使用してクラスターにアクセスします。  

また、SSH または PuTTY 経由で接続した後、単に Linux コマンド プロンプトで「**R**」と入力して、エッジ ノードの R Server コンソールにアクセスすることもできます。 別のウィンドウで R スクリプト開発用のテキスト エディターを実行し、必要に応じてスクリプトのセクションを切り取って R コンソールに貼り付けると、コンソール インターフェイスの操作を改善できます。

## <a name="learn-about-pricing"></a>価格について
R Server を含む HDInsight クラスターに関する料金は、Standard HDInsight クラスターの料金と同様に体系化されています。 料金は、ネーム、データ、エッジの各ノードの基になる VM のサイズに基づきますが、コア時間の割増分が加算されます。 HDInsight の価格の詳細、30 日間無料試用版の提供状況の詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## <a name="next-steps"></a>次のステップ
HDInsight クラスターで R Server を使用する方法の詳細については、以下のリンクをご覧ください。

* [HDInsight での R Server の使用](hdinsight-hadoop-r-server-get-started.md)
* [HDInsight へ RStudio Server を追加する (クラスター作成時にインストールされていない場合)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight の R Server (プレビュー) の計算コンテキストのオプション](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)

