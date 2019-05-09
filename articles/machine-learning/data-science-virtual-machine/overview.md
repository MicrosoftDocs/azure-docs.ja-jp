---
title: Linux および Windows 用の Azure データ サイエンス仮想マシンの概要 | Microsoft Docs
description: Windows および Linux 用のデータ サイエンス仮想マシンの主要な分析シナリオとコンポーネント。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 74fe2345af5d1249d6704c7560b42323561bc623
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918248"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用の Azure データ サイエンス仮想マシンの概要

データ サイエンス仮想マシン (DSVM) とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 Windows Server と Linux で使用できます。 Windows エディションの DSVM は、Windows Server 2016 と Windows Server 2012 で提供しています。 Linux エディションの DSVM は、Ubuntu 16.04 LTS と CentOS 7.4 で提供しています。

このトピックは、データ サイエンス VM でできることを説明し、VM を使う主なシナリオの概要を示し、Windows および Linux バージョンで使用できる主な機能の一覧を示し、使い始める方法について説明します。


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでできること
Data Science Virtual Machine (DSVM) の目標は、業界を越えてすべてのスキル レベルのデータ専門家に、摩擦のない、事前に構成され、かつ完全に統合されたデータ サイエンス環境を提供することです。 同等のワークスペースを独自にロールアウトする代わりに DSVM をプロビジョニングすることによって、インストール、構成、およびパッケージ管理プロセスにかかる数日、場合によっては "_数週間_" の期間を節約できます。 DSVM が割り当てられたら、直ちにデータ サイエンス プロジェクトに関する作業を開始できます。

データ サイエンス VM は、広範な使用シナリオで使えるように設計および構成されています。 プロジェクトのニーズの変化に応じて、環境をスケールアップまたはスケールダウンできます。好みの言語を使ってデータ サイエンス タスクをプログラミングできます。他のツールをインストールし、正確なニーズに合わせてシステムをカスタマイズできます。

## <a name="key-scenarios"></a>主なシナリオ
ここでは、データ サイエンス VM をデプロイできる主要なシナリオをいくつか示します。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>クラウド内の事前構成済み分析デスクトップ
データ サイエンス VM は、ローカル デスクトップを管理されたクラウド デスクトップに置き換えようと考えているデータ サイエンス チームに基準となる構成を提供します。 この基準により、チームのすべてのデータ科学者に、実験を確認して共同作業を促進するための一貫性のあるセットアップが保証されます。 また、システム管理者の負担を軽減し、高度な分析を行うために必要なさまざまなソフトウェア パッケージの評価、インストール、および管理に必要な時間を短縮することで、コストも削減できます。

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育
エンタープライズでデータ サイエンスを教える訓練と教育の担当者は、通常、受講者が一貫したセットアップを使用でき、サンプルが予測どおりに動作するように、仮想マシン イメージを提供します。 データ サイエンス VM は、サポートと非互換性の問題を軽減する一貫したセットアップでオンデマンドの環境を作成します。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大規模なプロジェクトのためのオンデマンドで柔軟な容量
データ サイエンスのハッカーソンや競技または大規模なデータ モデリングと探索では、スケールアウトしたハードウェア容量が通常は短期間に必要になります。 データ サイエンス VM は、高性能コンピューティング リソースを実行する必要がある実験を可能にするスケールアウトされたサーバー上で、必要に応じて迅速にデータ サイエンス環境を複製できます。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価
データ サイエンス VM は、Microsoft ML Server、SQL Server、Visual Studio ツール、Jupyter、ディープ ラーニング/ML ツールキット、およびコミュニティで人気のあるセットアップ作業が最小限の新しいツールなど、ツールの学習と評価のために使用できます。 Data Science VM は迅速に設定できるので、公開された実験の複製、デモの実行、オンライン セッションでのチュートリアルの実施、会議チュートリアルなど、他の短期的な使用シナリオに適用できます。

### <a name="deep-learning"></a>ディープ ラーニング
Data Science VM は、GPU (グラフィック処理装置) ベースのハードウェア上でディープ ラーニング アルゴリズムを使用するトレーニング モデルに使用できます。 DSVM では、Azure クラウドの VM スケーリング機能を利用すると、必要に応じてクラウド上で GPU ベースのハードウェアを使用できます。 同じ OS ディスクを保持しつつ、大規模モデルをトレーニングするときまたは高速計算が必要なときに GPU ベースの VM に切り替えることができます。  Windows Server 2016 エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング フレームワークが事前インストールされています。 Linux エディションでは、GPU でのディープ ラーニングは、CentOS と Ubuntu DSVM の両方で可能です。 Data Science VM の Ubuntu、CentOS、または Windows 2016 エディションは GPU ベースでない Azure 仮想マシンにデプロイできます。その場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックします。

## <a name="whats-included-in-the-data-science-vm"></a>データ サイエンス VM に含まれるもの
データ サイエンス仮想マシンには、多くの一般的なデータ サイエンスおよびディープ ラーニング ツールが既にインストールされ、構成されています。 また、Microsoft ML Server (R、Python) などの各種の Azure データおよび分析製品を簡単に操作して予測モデルを構築したり、SQL Server 2017 を使用して大規模なデータ セットを探査したりするためのツールも含まれています。 オープン ソース コミュニティや Microsoft の他のツールのホスト、およびサンプル コードとノートブックも含まれます。 次の表は、データ サイエンス仮想マシンの Windows エディションと Linux エディションに含まれる主要なコンポーネントを列記して比較したものです。


| **ツール**                                                           | **Windows エディション** | **Linux エディション** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 人気のパッケージがプレインストールされた [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft ML Server (R、Python)](https://docs.microsoft.com/machine-learning-server/) Developer エディションには次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) の並列および分散型の高機能フレームワーク (R および Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft の新しい最先端 ML アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R および Python の運用化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus (共有アクティベーション付き) - Excel、Word および PowerPoint   |Y                      |N              |
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
| **開発ツール、IDE、およびコード エディター**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git プラグイン、Azure HDInsight (Hadoop)、Data Lake、SQL Server データ ツール、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/)を備えた [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) | Y | N |
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
| **ビッグ データ プラットフォーム (Devtest のみ)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* ローカル [Spark](https://spark.apache.org/) Standalone | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* ローカル [Hadoop](https://hadoop.apache.org/) (HDFS、YARN) | N | Y |

## <a name="get-started"></a>作業開始

### <a name="windows-data-science-vm"></a>Windows データ サイエンス VM
* Windows DSVM の作成方法とその使用方法の詳細については、「[Azure での Windows データ サイエンス仮想マシンのプロビジョニング](provision-vm.md)」を参照してください。 Windows DSVM のデータ サイエンス プロジェクトに必要なさまざまなタスクを実行する方法について詳しくは、「[データ サイエンス仮想マシンでできる 10 のこと](vm-do-ten-things.md)」をご覧ください。

### <a name="linux-data-science-vm"></a>Linux データ サイエンス VM
* Ubuntu DSVM の作成方法とその使用方法の詳細については、「[Linux (Ubuntu) データ サイエンス仮想マシンのプロビジョニング](dsvm-ubuntu-intro.md)」を参照してください。 CentOS DSVM の作成方法とその使用方法の詳細については、「[Azure での Linux CentOS データ サイエンス仮想マシンのプロビジョニング](linux-dsvm-intro.md)」を参照してください。
* Linux VM (CentOS および Ubuntu) で一般的なデータ サイエンス タスクを実行する方法がわかるチュートリアルについては、「[Linux データ サイエンス仮想マシンでのデータ サイエンス](linux-dsvm-walkthrough.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
[Azure での R 開発者向けガイド](/azure/architecture/data-guide/technology-choices/r-developers-guide)
