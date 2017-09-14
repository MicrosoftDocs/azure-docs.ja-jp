---
title: "データ サイエンス仮想マシンとは | Microsoft Docs"
description: "データ サイエンス仮想マシンで主要な分析シナリオを始める方法。"
keywords: "データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: gokuma;bradsev
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: d6346419756cb0841c23f3ba63e479ba2397af54
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用のクラウド ベースのデータ サイエンス仮想マシンの概要
データ サイエンス仮想マシン (DSVM) とは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 Windows Server と Linux で使用できます。 Windows エディションの DSVM は、Windows Server 2016 と Windows Server 2012 で提供しています。 Linux エディションの DSVM は、Ubuntu 16.04 LTS と OpenLogic 7.2 CentOS ベースの Linux ディストリビューションで提供しています。 

このトピックは、データ サイエンス VM でできることを説明し、VM を使う主なシナリオの概要を示し、Windows および Linux バージョンで使用できる主な機能の一覧を示し、使い始める方法について説明します。

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンでできること
データ サイエンス仮想マシンの目的は、円滑なデータ サイエンス環境ですべてのスキル レベルとロールのプロフェッショナルにデータを提供することです。 この VM を使うと、同等の環境を独自に展開する場合と比較してかなりの時間を削減できます。 代わりに、新しく作成された VM インスタンスでデータ サイエンス プロジェクトをすぐに開始できます。 

データ サイエンス VM は、広範な使用シナリオで使えるように設計および構成されています。 プロジェクトのニーズの変化に応じて、環境を拡大または縮小できます。 好みの言語を使ってデータ サイエンス タスクをプログラミングできます。 他のツールをインストールし、正確なニーズに合わせてシステムをカスタマイズできます。

## <a name="key-scenarios"></a>主なシナリオ
ここでは、データ サイエンス VM をデプロイできる主要なシナリオをいくつか示します。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>クラウド内の事前構成済み分析デスクトップ
データ サイエンス VM は、ローカル デスクトップを管理されたクラウド デスクトップに置き換えようと考えているデータ サイエンス チームに基準となる構成を提供します。 この基準により、チームのすべてのデータ科学者に、実験を確認して共同作業を促進するための一貫性のあるセットアップが保証されます。 また、システム管理者の負担を軽減し、高度な分析を行うために必要なさまざまなソフトウェア パッケージの評価、インストール、および管理に必要な時間を短縮することで、コストも削減できます。  

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育
エンタープライズでデータ サイエンスを教える訓練と教育の担当者は、通常、受講者が一貫したセットアップを使用でき、サンプルが予測どおりに動作するように、仮想マシン イメージを提供します。 データ サイエンス VM は、サポートと非互換性の問題を軽減する一貫したセットアップでオンデマンドの環境を作成します。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大規模なプロジェクトのためのオンデマンドで柔軟な容量
データ サイエンスのハッカーソンや競技または大規模なデータ モデリングと探索では、スケールアウトしたハードウェア容量が通常は短期間に必要になります。 データ サイエンス VM は、高性能コンピューティング リソースを実行する必要がある実験を可能にするスケールアウトされたサーバー上で、必要に応じて迅速にデータ サイエンス環境を複製できます。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価
データ サイエンス VM は、Microsoft R Server、SQL Server、Visual Studio ツール、Jupyter、ディープ ラーニング/ML ツールキット、およびコミュニティで人気のあるセットアップ作業が最小限の新しいツールなど、ツールの学習と評価のために使用できます。 データ サイエンス VM は迅速にセットアップできるので、公開された実験の複製、デモの実行、オンライン セッションでのチュートリアルの実施、会議チュートリアルなど、他の短期的な使用シナリオに適用できます。

### <a name="deep-learning"></a>ディープ ラーニング
データ サイエンス VM は、GPU (グラフィック処理装置) ベースのハードウェアで、ディープ ラーニング アルゴリズムを使用したトレーニング モデルに使用できます。 DSVM では、Azure クラウドの VM スケーリング機能を利用すると、必要に応じてクラウド上で GPU ベースのハードウェアを使用できます。 同じ OS ディスクを保持しつつ、大規模モデルをトレーニングするときまたは高速計算が必要なときに GPU ベースの VM に切り替えることができます。  Windows Server 2016 エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング アルゴリズムが事前インストールされています。 Linux では、GPU のディープ ラーニングは [Linux (Ubuntu) エディション用のデータ サイエンス仮想マシン](http://aka.ms/dsvm/ubuntu)でのみ有効です。 データ サイエンス VM の Ubuntu/Windows-2016 エディションは GPU ベースでない Azure 仮想マシンにデプロイできます。その場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックします。 以前、Windows Server 2012 用に[ディープ ラーニング ツールキット](http://aka.ms/dsvm/deeplearning)を公開しましたが、Windows ベースのディープ ラーニング ワークロードには Windows Server 2016 を使うことをお勧めします。 DSVM の CentOS ベースの Linux エディションには、一部のディープ ラーニング ツール (CNTK、Tensorflow、MXNet) の CPU ビルドのみが含まれていますが、GPU ドライバーやフレームワークがプレインストールされていません。 

## <a name="whats-included-in-the-data-science-vm"></a>データ サイエンス VM に含まれるもの
データ サイエンス仮想マシンには、多くの一般的なデータ サイエンスおよびディープ ラーニング ツールが既にインストールされ、構成されています。 また、Azure のさまざまなデータおよび分析製品を簡単に使用できるようにするツールも含まれます。 Microsoft R Server または SQL Server 2016 を使って、大規模なデータ セットに対する予測モデルを研究および構築できます。 オープン ソース コミュニティや Microsoft の他のツールのホスト、およびサンプル コードとノートブックも含まれます。 次の表は、データ サイエンス仮想マシンの Windows エディションと Linux エディションに含まれる主要なコンポーネントを列記して比較したものです。


| **ツール**                                                           | **Windows エディション** | **Linux エディション** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 人気のパッケージがプレインストールされた [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft R Server](https://msdn.microsoft.com/microsoft-r/) Developer エディションには次のものが含まれます。 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-getting-started) 並列および分散ハイ パフォーマンス R フレームワーク<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml-introduction) - Microsoft の新しい最先端 ML アルゴリズム <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R の運用化](https://msdn.microsoft.com/microsoft-r/operationalize/about)                                            |Y                      | Y <br/> (MicrosoftML はまだ使用できません)|
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus (共有アクティベーション付き) - Excel、Word および PowerPoint   |Y                      |N              |
| 人気のパッケージがプレインストールされた [Anaconda Python](https://www.continuum.io/) 2.7、3.5    |Y                      |Y              |
| Julia 言語用の人気のパッケージがプレインストールされた [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| リレーショナル データベース                                                            | [SQL Server 2016 SP1](https://www.microsoft.com/sql-server/sql-server-2016) <br/> Developer エディション| [PostgreSQL](https://www.postgresql.org/) |
| データベース ツール                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC ドライバー| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (クエリ ツール)、 <br /> * bcp、sqlcmd <br /> * ODBC/JDBC ドライバー|
| SQL Server R サービスによるスケーラブルなデータベース内分析 | Y     |N              |
| 次のカーネルを備えた **[Jupyter Notebook Server](http://jupyter.org/)**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 および 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | N | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (Ubuntu のみ) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub (マルチ ユーザー Notebook サーバー)| N | Y |
| **開発ツール、IDE、およびコード エディター**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git プラグイン、Azure HDInsight (Hadoop)、Data Lake、SQL Server データ ツール、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](http://aka.ms/ptvs)、および [R Tools for Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/)を備えた [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim および Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git および GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Y | N |
| PowerBI Desktop | Y | N |
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK | Y | Y |
| **データ移動および管理ツール** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Storage Explorer | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB データ移行ツール](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx) : オンプレミスとクラウドの間でのデータの移動 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux コマンド ライン ユーティリティ | Y | Y |
| データ探索用 [Apache Drill](http://drill.apache.org) | Y | Y |
| **Machine Learning ツール** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) との統合 (R、Python) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (Ubuntu のみ) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (Ubuntu のみ) |
| **GPU ベースのディープ ラーニング ツール** |Windows Server 2016 エディション  |Ubuntu エディション |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](http://cntk.ai) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Tensorflow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe および Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA、CUDNN、Nvidia Driver](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **ビッグ データ プラットフォーム (Devtest のみ)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* ローカル [Spark](http://spark.apache.org/) Standalone | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* ローカル [Hadoop](http://hadoop.apache.org/) (HDFS、YARN) | N | Y |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Windows データ サイエンス仮想マシンを使う方法
* 次の場所に移動して、必要な Windows DSVM エディションのインスタンスを作成します。
  * [Windows Server 2016 ベースの DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  または 
  * [Windows Server 2012 ベースの DSVM](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* **[今すぐ入手する]** ボタンをクリックします。
* VM を作成するときに指定した資格情報を使って、リモート デスクトップから VM にサインインします。
* 使用できるツールを見つけて起動するには、**[Start]** (開始) メニューをクリックします。

## <a name="get-started-with-the-linux-data-science-vm"></a>Linux データ サイエンス仮想マシンを使う
* 次の場所に移動して、必要な Linux DSVM エディションのインスタンスを作成します。 
  * [Ubuntu ベースの DSVM](http://aka.ms/dsvm/ubuntu)

  または

  * [OpenLogic CentOS ベースの DSVM](http://aka.ms/dsvm/centos)

  
* **[今すぐ入手する]** ボタンをクリックします。
* VM を作成するときに指定した資格情報を使って、Putty や SSH Command などの SSH クライアントから VM にサインインします。
* シェル プロンプトで、「dsvm-more-info」と入力します。
* グラフィカルなデスクトップの場合、[こちら](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)からお使いのクライアント プラットフォーム用の X2Go クライアントをダウンロードし、Linux データ サイエンス VM のドキュメント「[Linux データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)」の説明に従います。

## <a name="next-steps"></a>次のステップ
### <a name="for-the-windows-data-science-vm"></a>Windows データ サイエンス VM の場合
* Windows バージョンで利用可能な特定のツールを実行する方法について詳しくは、「[Microsoft データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-provision-vm.md)」をご覧ください。
* Windows VM のデータ サイエンス プロジェクトに必要なさまざまなタスクを実行する方法について詳しくは、「[データ サイエンス仮想マシンでできる 10 のこと](machine-learning-data-science-vm-do-ten-things.md)」をご覧ください。

### <a name="for-the-linux-data-science-vm"></a>Linux データ サイエンス VM の場合
* Linux バージョンで利用可能な特定のツールを実行する方法について詳しくは、「[Linux データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-linux-dsvm-intro.md)」をご覧ください。
* Linux VM で一般的なデータ サイエンス タスクを実行する方法がわかるチュートリアルについては、「[Linux データ サイエンス仮想マシンでのデータ サイエンス](machine-learning-data-science-linux-dsvm-walkthrough.md)」をご覧ください。


