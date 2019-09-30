---
title: Azure Data Science Virtual Machine とは
description: Windows および Linux 用のデータ サイエンス仮想マシンの主要な分析シナリオとコンポーネント。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191898"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用の Azure Data Science Virtual Machine とは

Data Science Virtual Machine (DSVM) とは、データ サイエンス専用に構築された Azure クラウド プラットフォーム上のカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 

ツールの構成は、データ サイエンティストや Microsoft の開発者に加えて、より広範なデータ サイエンス コミュニティによって綿密にテストされています。 このテストは、安定性と一般的な実行可能性を保証するために役立ちます。

DSVM を利用できる OS:
+ Windows Server 2016、Windows Server 2012
+ Ubuntu 16.04 LTS、CentOS 7.4

> [!NOTE]
> ディープ ラーニング用のすべての VM ツールは、Data Science Virtual Machine に組み込まれています。 


## <a name="what-can-i-do-with-the-dsvm"></a>DSVM でできること
Data Science Virtual Machine の目標は、業界を越えてすべてのスキル レベルのデータ専門家に、摩擦のない、事前に構成され、かつ完全に統合されたデータ サイエンス環境を提供することです。 DSVM をプロビジョニングすると、同等のワークスペースを独自にロールアウトしなくて済みます。 そうすることによって、インストール、構成、およびパッケージ管理プロセスにかかる数日、場合によっては "_数週間_" の期間を節約できます。 DSVM が割り当てられたら、直ちにデータ サイエンス プロジェクトに関する作業を開始できます。

DSVM は、広範な使用シナリオで使用できるように設計および構成されています。 環境は、プロジェクトの要件の変化に応じてスケールアップまたはスケールダウンできます。 また、好みの言語を使ってデータ サイエンス タスクをプログラミングしたり、他のツールをインストールしてニーズに合わせてシステムをカスタマイズしたりすることもできます。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>クラウド内の事前構成済み分析デスクトップ
DSVM は、ローカル デスクトップを管理クラウド デスクトップに置き換えようと考えているデータ サイエンス チームに基準となる構成を提供します。 この基準により、チームのすべてのデータ科学者に、実験を確認して共同作業を促進するための一貫性のあるセットアップが保証されます。 また、システム管理者の負担が軽減されるためコストも低減されます。 この負担軽減により、高度な分析用のソフトウェア パッケージの評価、インストール、および保守に必要な時間が節約されます。

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育
データ サイエンスのクラスを教える企業のトレーナーや教育担当者は、通常、仮想マシン イメージを提供します。 そのイメージにより、受講者が一貫したセットアップを行い、サンプルが予想どおりに動作するようにすることができます。 

DSVM は、一貫したセットアップでオンデマンドの環境を作成するため、サポートと非互換性の問題を軽減できます。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大規模なプロジェクトのためのオンデマンドで柔軟な容量
データ サイエンスのハッカーソンや競技または大規模なデータ モデリングと探索では、スケールアウトしたハードウェア容量が通常は短期間に必要になります。 DSVM は、高性能コンピューティング リソースで実行できる実験をスケールアウトされたサーバー上で可能にするために、必要に応じて迅速にデータ サイエンス環境を複製する際に役立ちます。

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks のカスタム コンピューティング能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) は、インストールせずに、クラウドで Jupyter ノートブックを開発、実行、および共有するための無料のホストされるサービスです。 無料のサービス レベルは、4 GB のメモリと 1 GB のデータに制限されています。 

すべての制限を解除するには、Notebooks プロジェクトを DSVM か、Jupyter サーバー上で実行されている他の任意の VM に接続します。 Azure Active Directory を使用するアカウント (会社のアカウントなど) で Azure Notebooks にサインインすると、Notebooks には、そのアカウントに関連付けられているすべてのサブスクリプションの DSVM が自動的に表示されます。 [DSVM を Azure Notebooks に接続](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)して、利用可能なコンピューティング能力を拡張することができます。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価
DSVM を使用すると、最小限の設定作業で、次のようなツールを評価または学習できます。

- Microsoft Machine Learning Server
- SQL Server
- Visual Studio Tools
- Jupyter
- ディープ ラーニングと機械学習のツールキット
- コミュニティで人気のある新しいツール 

DSVM は短時間で設定できるため、他の短期的な使用シナリオにも適用できます。 たとえば、公開された実験の複製、デモの実行、オンライン セッションや会議チュートリアルでの手順の実施などのシナリオがあります。

### <a name="deep-learning"></a>ディープ ラーニング
DSVM では、トレーニング モデルはグラフィックス処理装置 (GPU) ベースのハードウェア上でディープ ラーニング アルゴリズムを使用できます。 Azure プラットフォームの VM スケーリング機能を利用することにより、DSVM はニーズに応じてクラウドで GPU ベースのハードウェアを使用するのに役立ちます。 大規模モデルをトレーニングするときや、同じ OS ディスクを保持しつつ高速計算が必要なときに、GPU ベースの VM に切り替えることができます。  

Windows Server 2016 エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング フレームワークが事前インストールされています。 Linux エディションでは、GPU 上でのディープ ラーニングは、CentOS と Ubuntu の両方の DSVM で可能です。 

Ubuntu、CentOS、または Windows 2016 エディションの DSVM を、GPU ベースでない Azure 仮想マシンにデプロイすることもできます。 この場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックはします。
 
[使用可能なディープ ラーニングと AI のフレームワークの詳細について学習](dsvm-deep-learning-ai-frameworks.md)してください。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM に含まれているもの
データ サイエンス仮想マシンには、多くの一般的なデータ サイエンスおよびディープ ラーニング ツールが既にインストールされ、構成されています。 また、Azure のさまざまなデータおよび分析製品を簡単に使用できるようにするツールも含まれます。 これらの製品には、予測モデルを構築するための Microsoft Machine Learning Server (R、Python) と、データ セットの大規模な探索のための SQL Server 2017 が含まれています。 DSVM には、オープンソース コミュニティと Microsoft の他のツールと、[サンプル コードおよびノートブック](dsvm-samples-and-walkthroughs.md)が含まれています。 

以下に、ツールとプラットフォームの一覧を示します。
+ [サポートされるプログラミング言語](dsvm-languages.md)

+ [サポートされるデータ プラットフォーム](dsvm-data-platforms.md)

+ [開発ツールと IDE](dsvm-tools-development.md)

+ [ディープ ラーニングと AI のフレームワーク](dsvm-deep-learning-ai-frameworks.md)

+ [機械学習ツールとデータ サイエンス ツール](dsvm-ml-data-science-tools.md)

+ [データ インジェスト ツール](dsvm-tools-ingestion.md)

+ [データの探索と視覚化のツール](dsvm-tools-explore-and-visualize.md)

次の表は、データ サイエンス仮想マシンの Windows エディションと Linux エディションに含まれる主要なコンポーネントを列記して比較したものです。

| **ツール**                                                           | **Windows エディション** | **Linux エディション** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 人気のパッケージがプレインストールされた [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft Machine Learning Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer エディションには、次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR および revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) の並列および分散型の高機能フレームワーク (R および Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft の新しい最先端機械学習アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R および Python の運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus (共有アクティベーション付き): Excel、Word、PowerPoint   |Y                      |N              |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) 2.7 および 3.5    |Y                      |Y              |
| Julia 言語用の人気のパッケージがプレインストールされた [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| リレーショナル データベース                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション (Ubuntu) |
| データベース ツール                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC および JDBC ドライバー|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br />  bcp、sqlcmd <br />  ODBC および JDBC ドライバー|
| SQL Server 機械学習サービス (R、Python) によるスケーラブルなデータベース内分析 | Y     |N              |
| 次のカーネルを備えた [Jupyter Notebook Server](https://jupyter.org/)                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu のみ) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | Y |
| JupyterHub (マルチユーザー Notebook サーバー)| N | Y |
| JupyterLab (マルチユーザー Notebook サーバー) | N | Y (Ubuntu のみ) |
| 開発ツール、IDE、コード エディター:| | |
| Git プラグイン、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) を備えた &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim および Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Git および Git Bash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK | Y | Y |
| データ移動および管理ツール: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [BLOB FUSE ドライバー](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB データ移行ツール](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): オンプレミスとクラウド間でデータを移動する | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix および Linux コマンドライン ツール | Y | Y |
| データ探索用 [Apache Drill](https://drill.apache.org) | Y | Y |
| 機械学習ツール: |||
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (R、Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu のみ) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | Y (Ubuntu のみ) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (Ubuntu のみ) |
| GPU または CPU 上で動作するディープ ラーニング ツール: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | Y (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe および Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet モデル サーバー](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit) | Y | Y |

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

+ Windows:
  + [Windows DSVM を設定する](provision-vm.md)
  + [Windows DSVM でできる 10 のこと](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM を設定する (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM を設定する (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM のデータ サイエンス](linux-dsvm-walkthrough.md)
