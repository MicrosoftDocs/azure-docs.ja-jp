---
title: Azure HDInsight での ML Services の概要
description: HDInsight 上の ML Services を使用してビッグ データ分析用のアプリケーションを作成する方法について説明します。
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943919"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Azure HDInsight での ML Services とは

Azure で HDInsight クラスターを作成するときのデプロイ オプションとして Microsoft Machine Learning Server を選択できるようになりました。 このオプションを提供するクラスターの種類は、**ML Services** と呼ばれます。 この機能により、HDInsight での柔軟で分散型の分析方法にオンデマンドでアクセスできます。

HDInsight の ML Services は、ほぼすべてのサイズのデータセットで R ベースの分析を行うための最新の機能を備えています。 データセットは、Azure BLOB または Data Lake ストレージのいずれかに読み込むことができます。 R ベースのアプリケーションは、8000 個以上のオープンソースの R パッケージを使用できます。 また、ScaleR (Microsoft のビッグ データ分析パッケージ) のルーチンも使用できます。

エッジ ノードは、クラスターに接続して R スクリプトを実行するのに便利な場所です。 エッジ ノードでは、サーバーのコア間で、ScaleR の並列化された分散関数を実行できます。 また、ScaleR の Hadoop Map Reduce を使用して、クラスターのノード間でそれらを実行することもできます。 Apache Spark のコンピューティング コンテキストも使用できます。

分析によって得られたモデルや予測は、ダウンロードしてオンプレミスで使用できます。 また、Azure 内の他の場所で運用化 (`operationalized`) することもできます。 具体的には、[Azure Machine Learning Studio (classic)](https://studio.azureml.net)、および [Web サービス](../../machine-learning/classic/deploy-a-machine-learning-web-service.md)を使用します。

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight での ML Services の概要

HDInsight で ML Services クラスターを作成するには、**ML Services** クラスターの種類を選択します。 ML Services クラスターの種類には、データ ノード上の ML Server と、エッジ ノードが含まれます。 エッジ ノードは、ML Services ベースの分析のランディング ゾーンとして機能します。 クラスターの作成方法のチュートリアルについては、[Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

## <a name="why-choose-ml-services-in-hdinsight"></a>HDInsight で ML Services を選ぶ理由

HDInsight における ML Services のメリットは次のとおりです。

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Microsoft およびオープン ソースからの AI 革新

  ML Services には、[RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)、[microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package) など、高い適応性を持つ分散型のアルゴリズム セットが含まれます。 これらのアルゴリズムは、物理メモリより大きいサイズのデータ サイズに対応しています。 また、分散された方法で、さまざまなプラットフォームで実行することができます。 製品に付属する Microsoft のカスタム [R パッケージ](/machine-learning-server/r-reference/introducing-r-server-r-package-reference)と [Python パッケージ](/machine-learning-server/python-reference/introducing-python-package-reference)のコレクションの詳細をご覧ください。
  
  ML Services は、Microsoft の革新技術とオープンソース コミュニティの貢献 (R、Python、AI ツールキット) を橋渡しします。 それは、すべて 1 つのエンタープライズ レベルのプラットフォーム上で行われます。 R または Python のすべてのオープン ソース機械学習パッケージが、Microsoft 独自の革新技術とサイド バイ サイドで動作できます。

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>簡単で、安全性が高く、高スケールの運用化と管理

  従来のパラダイムと環境に依存する企業は、運用化に多くの時間と労力を費やすことになります。 その結果、モデルの変換時間、それらを有効で最新状態にしておくためのイテレーション、規制の承認、アクセス許可の管理などのコストと遅延が増加します。

  ML Services は、エンタープライズ レベルの[運用化](/machine-learning-server/what-is-operationalization)を提供します。 機械学習モデルが完成した後は、わずか数クリックで Web サービス API が生成されます。 これらの [Web サービス](/machine-learning-server/operationalize/concept-what-are-web-services)はクラウドのサーバー グリッドでホストされ、基幹業務アプリケーションと統合できます。 弾力性のあるグリッドに展開できるので、バッチとリアルタイム スコアリングの両方について、ビジネスのニーズに合わせてシームレスに拡張できます。 方法については、[HDInsight での ML Services の運用化](r-server-operationalize.md)に関する記事をご覧ください。

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> HDInsight の ML Services クラスター タイプは、HDInsight 3.6 でのみサポートされます。 HDInsight 3.6 は、2020 年 12 月 31 日に廃止される予定です。

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight での ML Services の主要機能

HDInsight の ML Services には次の機能が含まれます。

| 機能カテゴリ | 説明 |
|------------------|-------------|
| R 対応 | [R パッケージ](/machine-learning-server/r-reference/introducing-r-server-r-package-reference): R で記述されたソリューション用であり、R のオープンソース ディストリビューションと、スクリプト実行用のランタイム インフラストラクチャを含みます。 |
| Python 対応 | [Python モジュール](/machine-learning-server/python-reference/introducing-python-package-reference): Python で記述されたソリューション用であり、Python のオープン ソース ディストリビューションと、スクリプト実行用のランタイム インフラストラクチャを含みます。
| [事前トレーニング済みモデル](/machine-learning-server/install/microsoftml-install-pretrained-models) | ビジュアル分析とテキストのセンチメント分析に対応し、ユーザーが提供するデータをスコア付けできる状態になっています。 |
| [展開と使用](r-server-operationalize.md) | サーバーを運用化 (`Operationalize`) し、Web サービスとしてソリューションを展開します。 |
| [リモート実行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | クライアント ワークステーションからネットワーク上の ML Services でリモート セッションを開始します。 |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>HDInsight 上の ML Services 向けのデータ ストレージ オプション

HDFS ファイル システムの既定のストレージには、Azure Storage アカウントまたは Azure Data Lake Storage を指定できます。 分析中にクラスター ストレージにアップロードされたデータは永続的になります。 このデータは、クラスターの削除後も使用できます。 ストレージへのデータ転送は、さまざまなツールで処理できます。 このツールには、ストレージ アカウントのポータルベースのアップロード機能と AzCopy ユーティリティが含まれます。

クラスターの作成中に、追加の BLOB および Data Lake Store へのアクセスを有効にできます。 使用しているプライマリ ストレージ オプションによって制限されることはありません。  複数のストレージ アカウントの使用について詳しくは、[HDInsight の ML Services 向けの Azure Storage オプション](./r-server-storage.md)に関する記事をご覧ください。

エッジ ノードで使用するストレージ オプションとして、Azure Files を使用することもできます。 Azure Files により、Azure Storage で作成されたファイル共有を Linux ファイル システムで有効にできます。 詳細については、[HDInsight の ML Services 向けの Azure Storage オプション](r-server-storage.md)に関する記事を参照してください。

## <a name="access-ml-services-edge-node"></a>ML Services エッジ ノードにアクセスする

ブラウザーまたは SSH/PuTTY を使用して、エッジ ノードの Microsoft ML Server に接続できます。 R コンソールは、クラスターの作成時に既定でインストールされます。  

## <a name="develop-and-run-r-scripts"></a>R スクリプトの開発と実行

R スクリプトでは、8000 個以上のオープンソース R パッケージのいずれも使用できます。 また、ScaleR ライブラリの並列化および分散化されたルーチンも使用できます。 エッジ ノード上で実行されるスクリプトは、そのノードの R インタープリター内で実行されます。 Map Reduce (RxHadoopMR) または Spark (RxSpark) のコンピューティング コンテキストで ScaleR 関数を呼び出すステップを除きます。 この関数は、データに関連付けられているデータ ノード全体で、分散された方法で実行されます。 コンテキスト オプションの詳細については、「[HDInsight 上の ML Services 向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)」をご覧ください。

## <a name="operationalize-a-model"></a>モデルを運用化 (`Operationalize`) する

データ モデリングが完了したら、モデルを運用化 (`operationalize`) して、Azure とオンプレミスの両方で新しいデータについての予測を行うことができます。 このプロセスはスコア付けと呼ばれます。 スコア付けは、HDInsight や Azure Machine Learning のほか、オンプレミスで行うことができます。

### <a name="score-in-hdinsight"></a>HDInsight でのスコア付け

HDInsight でスコア付けを行うには、R 関数を記述します。 この関数は、モデルを呼び出して、ストレージ アカウントに読み込んだ新しいデータ ファイルについて予測を行います。 その後、予測をストレージ アカウントに保存します。 このルーチンは、クラスターのエッジ ノードでオンデマンドで実行することも、スケジュールされたジョブを使用して実行することもできます。

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning (AML) でのスコア付け

Azure Machine Learning を使ってスコア付けを行うには、[AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) と呼ばれるオープン ソースの Azure Machine Learning R パッケージを使って、モデルを Azure Web サービスとして発行します。 便宜上、このパッケージはエッジ ノードにあらかじめインストールされています。 次に、Azure Machine Learning の機能を使って Web サービスのユーザー インターフェイスを作成し、スコア付けが必要なときに Web サービスを呼び出します。 次に、ScaleR モデル オブジェクトを、Web サービスで使う同等のオープンソース モデル オブジェクトに変換します。 この変換には、ScaleR 強制型変換関数 (アンサンブルベースのモデルの `as.randomForest()` など) を使用できます。

### <a name="score-on-premises"></a>オンプレミスのスコア付け

モデルの作成後にオンプレミスのスコア付けを行うには、R でモデルをシリアル化し、ダウンロードし、逆シリアル化してから、新しいデータのスコア付けに使用します。 新しいデータのスコア付けは、「HDInsight でのスコア付け」で前述した方法を使用するか、[Web サービス](/machine-learning-server/operationalize/concept-what-are-web-services)を使用して実行できます。

## <a name="maintain-the-cluster"></a>クラスターの管理

### <a name="install-and-maintain-r-packages"></a>R パッケージのインストールと管理

使用する R パッケージのほとんどは、R スクリプトのほとんどの手順が実行されるエッジ ノードに配置する必要があります。 エッジ ノードに追加の R パッケージをインストールするには、R の `install.packages()` メソッドを使用します。

ScaleR ライブラリ ルーチンを使用しているだけの場合は、通常、追加の R パッケージは必要ありません。 データ ノードで **rxExec** または **RxDataStep** を実行するには、追加のパッケージが必要になる場合があります。

追加のパッケージは、クラスターの作成後にスクリプト アクションを使ってインストールできます。 詳しくは、[HDInsight クラスターでの ML Services の管理](r-server-hdinsight-manage.md)に関する記事をご覧ください。

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce のメモリ設定の変更

ML Services で使用可能なメモリは、MapReduce ジョブを実行しているときに変更できます。 クラスターを変更するには、クラスターに Apache Ambari UI を使用します。 Ambari UI の説明については、「[Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」をご覧ください。

ML Services で使用できるメモリは、**RxHadoopMR** への呼び出しで Hadoop スイッチを使用して変更できます。

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>クラスターのスケーリング

HDInsight 上の既存の ML Services クラスターは、ポータルを使ってスケールアップまたはスケールダウンできます。 スケール アップすることで、より大きな処理タスク用に追加の容量を取得できます。 アイドル状態のときはクラスターをスケール バックできます。 クラスターのスケーリングの手順については、[HDInsight クラスターの管理](../hdinsight-administer-use-portal-linux.md)に関する記事をご覧ください。

### <a name="maintain-the-system"></a>システムのメンテナンス

OS のメンテナンスは、HDInsight クラスター内の基礎となる Linux VM で時間外に行われます。 通常、メンテナンスは毎週月曜日と木曜日の午前 3 時 30 分 (VM のローカル時刻) に実施されます。 更新プログラムの影響を同時に受けるのは、クラスターの 4 分の 1 以下になります。

メンテナンス中は、ジョブの実行速度が低下することがあります。 ただし、完了するまで引き続き実行する必要があります。 クラスターの再構築を必要とする致命的な障害が発生しない限り、カスタム ソフトウェアやローカル データはメンテナンス イベントが発生しても保持されます。

## <a name="ide-options-for-ml-services-on-hdinsight"></a>HDInsight 上の ML Services 向けの IDE オプション

HDInsight クラスターの Linux エッジ ノードは、R ベースの分析のランディング ゾーンです。 HDInsight の最新バージョンには、エッジ ノードに RStudio Server のブラウザーベースの IDE が提供されています。 RStudio Server は、開発および実行用の R コンソールよりも生産性が高くなります。

デスクトップ IDE は、リモート MapReduce または Spark コンピューティング コンテキストを介してクラスターにアクセスできます。 次のオプションがあります。Microsoft の [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS)、RStudio、Walware の Eclipse ベースの StatET。

コマンド プロンプトに **R** と入力して、エッジ ノードの R コンソールにアクセスします。 コンソールのインターフェイスを使用しているときは、テキスト エディターで R スクリプトを開発すると便利です。 次に、必要に応じてスクリプトのセクションを切り取って R コンソールに貼り付けます。

## <a name="pricing"></a>価格

ML Services の HDInsight クラスターに関する価格は、他の HDInsight クラスターの種類と同様に体系化されています。 それらは、名前、データ、およびエッジの各ノードでの基になる VM のサイズ設定に基づきます。 コア時間の割増し分も同様です。 詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

HDInsight クラスターで ML Services を使用する方法の詳細については、次の記事を参照してください。

* [RStudio Server を使用して Azure HDInsight で ML サービス クラスターに対して R スクリプトを実行する](machine-learning-services-quickstart-job-rstudio.md)
* [HDInsight 上の ML サービス クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
* [HDInsight 上の ML Services クラスター向けのストレージ オプション](r-server-storage.md)