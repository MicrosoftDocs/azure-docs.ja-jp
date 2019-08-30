---
title: Linux および Windows 用の Azure データ サイエンス仮想マシンの概要 | Microsoft Docs
description: Windows および Linux 用のデータ サイエンス仮想マシンの主要な分析シナリオとコンポーネント。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 2814ad51d2f0f868833cf9c6964b7ea4a8424435
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574914"
---
# <a name="what-is-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用の Azure Data Science Virtual Machine とは

データ サイエンス仮想マシン (DSVM) とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 

ツールの構成は、安定性と一般的な実行可能性を保証するために、データ サイエンティストやマイクロソフトの開発者に加えて、より広範なデータ サイエンス コミュニティによって綿密にテストされています。

DSVM を利用できる OS:
+ Windows Server 2016、Windows Server 2012
+ Ubuntu 16.04 LTS、CentOS 7.4

**すべての Deep Learning Virtual Machine** ツールは、Data Science VM に組み込まれています。 


## <a name="what-can-i-do-with-dsvm"></a>DSVM で何ができますか?
Data Science Virtual Machine (DSVM) の目標は、業界を越えてすべてのスキル レベルのデータ専門家に、摩擦のない、事前に構成され、かつ完全に統合されたデータ サイエンス環境を提供することです。 同等のワークスペースを独自にロールアウトする代わりに DSVM をプロビジョニングすることによって、インストール、構成、およびパッケージ管理プロセスにかかる数日、場合によっては "_数週間_" の期間を節約できます。 DSVM が割り当てられたら、直ちにデータ サイエンス プロジェクトに関する作業を開始できます。

データ サイエンス VM は、広範な使用シナリオで使えるように設計および構成されています。 環境は、プロジェクトの要件の変化に応じてスケールアップまたはスケールダウンできます。 また、好みの言語を使ってデータ サイエンス タスクをプログラミングしたり、他のツールをインストールして正確なニーズに合わせてシステムをカスタマイズしたりすることもできます。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>クラウド内の事前構成済み分析デスクトップ
データ サイエンス VM は、ローカル デスクトップを管理されたクラウド デスクトップに置き換えようと考えているデータ サイエンス チームに基準となる構成を提供します。 この基準により、チームのすべてのデータ科学者に、実験を確認して共同作業を促進するための一貫性のあるセットアップが保証されます。 また、システム管理者の負担が軽減されるためコストも低減されます。 この負担軽減により、高度な分析を行うために必要なさまざまなソフトウェア パッケージの評価、インストール、および保守に必要な時間が節約されます。

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育
データ サイエンスのクラスを教える企業のトレーナーや教育担当者は、通常、仮想マシン イメージを提供します。 彼らは、受講者が一貫したセットアップを行い、サンプルが予想どおりに動作するように、そのイメージを提供します。 データ サイエンス VM は、サポートと非互換性の問題を軽減する一貫したセットアップでオンデマンドの環境を作成します。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大規模なプロジェクトのためのオンデマンドで柔軟な容量
データ サイエンスのハッカーソンや競技または大規模なデータ モデリングと探索では、スケールアウトしたハードウェア容量が通常は短期間に必要になります。 Data Science VM は、高性能コンピューティング リソースが実行される実験を可能にするスケールアウトされたサーバー上で、必要に応じて迅速にデータ サイエンス環境を複製する際に役立ちます。

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks のカスタム コンピューティング能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) は、インストールせずに、クラウドで Jupyter ノートブックを開発、実行、および共有するための無料のホストされるサービスです。 ただし、無料のサービス レベルは 4GB のメモリと 1GB のデータに制限されています。 すべての制限を解放するには、その後 Notebooks プロジェクトを Data Science VM か、または Jupyter サーバーを実行している他の任意の VM に接続できます。 Azure Active Directory を使用してアカウント (会社のアカウントなど) で Azure Notebooks にサインインすると、Notebooks には、そのアカウントに関連付けられたいずれかのサブスクリプションの Data Science VM が自動的に表示されます。 [Data Science VM を Azure Notebooks に接続して](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)、利用可能なコンピューティング能力を拡張することができます。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価
データ サイエンス VM は、Microsoft ML Server、SQL Server、Visual Studio ツール、Jupyter、ディープ ラーニング/ML ツールキット、およびコミュニティで人気のあるセットアップ作業が最小限の新しいツールなど、ツールの学習と評価のために使用できます。 Data Science VM は短時間で設定できるため、他の短期的な使用シナリオに適用できます。 これらのシナリオには、公開された実験の複製、デモの実行、オンライン セッションでのチュートリアルの実施、会議チュートリアルが含まれます。

### <a name="deep-learning"></a>ディープ ラーニング
Data Science VM は、GPU (グラフィック処理装置) ベースのハードウェア上でディープ ラーニング アルゴリズムを使用するトレーニング モデルに使用できます。 DSVM では、Azure クラウドの VM スケーリング機能を利用すると、必要に応じてクラウド上で GPU ベースのハードウェアを使用できます。 同じ OS ディスクを保持しつつ、大規模モデルをトレーニングするときまたは高速計算が必要なときに GPU ベースの VM に切り替えることができます。  Windows Server 2016 エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング フレームワークが事前インストールされています。 Linux エディションでは、GPU でのディープ ラーニングは、CentOS と Ubuntu DSVM の両方で可能です。 Data Science VM の Ubuntu、CentOS、または Windows 2016 エディションは GPU ベース以外の Azure 仮想マシンにデプロイできます。 この場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックはします。 ディープ ラーニングと AI のフレームワークについては[こちら](dsvm-deep-learning-ai-frameworks.md)をご覧ください。
 
ディープ ラーニングと AI のフレームワークについては[こちら](dsvm-deep-learning-ai-frameworks.md)をご覧ください。

<a name="included"></a>

## <a name="whats-included-on-dsvm"></a>DSVM には何が含まれていますか?
データ サイエンス仮想マシンには、多くの一般的なデータ サイエンスおよびディープ ラーニング ツールが既にインストールされ、構成されています。 また、Microsoft ML Server (R、Python) などの各種の Azure データおよび分析製品を簡単に操作して予測モデルを構築したり、SQL Server 2017 を使用して大規模なデータ セットを探査したりするためのツールも含まれています。 この Data Science VM には、オープンソース コミュニティや Microsoft の他のツールのホストのほか、[サンプル コードやノートブック](dsvm-samples-and-walkthroughs.md)も含まれます。 

ツールとプラットフォーム:
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
| [Microsoft ML Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer エディションには次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) の並列および分散型の高機能フレームワーク (R および Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft の新しい最先端 ML アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R および Python の運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) Pro-Plus (共有アクティベーション付き) - Excel、Word、PowerPoint   |Y                      |N              |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) 2.7、3.5    |Y                      |Y              |
| Julia 言語用の人気のパッケージがプレインストールされた [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| リレーショナル データベース                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer エディション (Ubuntu) |
| データベース ツール                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC ドライバー| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br /> * bcp、sqlcmd <br /> * ODBC/JDBC ドライバー|
| SQL Server ML サービス (R、Python) によるスケーラブルなデータベース内分析 | Y     |N              |
| 次のカーネルを備えた **[Jupyter Notebook Server](https://jupyter.org/)**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu のみ) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (マルチ ユーザー Notebook サーバー)| N | Y |
| JupyterLab (マルチ ユーザー Notebook サーバー) | N | Y (Ubuntu のみ) |
| **開発ツール、IDE、コード エディター**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git プラグイン、Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) を備えた [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim および Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git および GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK | Y | Y |
| **データ移動および管理ツール** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE ドライバー](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB データ移行ツール](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft データ管理ゲートウェイ](https://msdn.microsoft.com/library/dn879362.aspx): オンプレミスとクラウドの間におけるデータの移動 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux コマンド ライン ユーティリティ | Y | Y |
| データ探索用 [Apache Drill](https://drill.apache.org) | Y | Y |
| **Machine Learning ツール** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (R、Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu のみ) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | Y (Ubuntu のみ) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (Ubuntu のみ) |
| **ディープ ラーニング ツール** <br>すべてのツールは GPU または CPU で動作します |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | Y (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe および Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet モデル サーバー](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA、cuDNN、NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) | Y | Y |

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

+ Windows:
  + [Windows DSVM を設定する](provision-vm.md)
  + [Windows DSVM でできる 10 のこと](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM を設定する (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM を設定する (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM のデータ サイエンス](linux-dsvm-walkthrough.md)
