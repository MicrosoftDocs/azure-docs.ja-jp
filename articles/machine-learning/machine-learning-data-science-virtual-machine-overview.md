---
title: "データ サイエンス仮想マシンとは | Microsoft Docs"
description: "この記事では、重要なシナリオ、機能、および分析に使用できるデータ サイエンス Virtual Machines、環境、ツールキットを使い始める方法について説明します。"
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
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe
ms.lasthandoff: 02/02/2017


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用のクラウド ベースのデータ サイエンス仮想マシンの概要
データ サイエンス仮想マシンとは、データ サイエンス専用に構築された Microsoft の Azure クラウド上にあるカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールや他のツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 Windows Server 2012 または OpenLogic 7.2 CentOS ベースの Linux バージョンで利用できます。 

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

## <a name="whats-included-in-the-data-science-vm"></a>データ サイエンス VM に含まれるもの
データ サイエンス仮想マシンには、多くの一般的なデータ サイエンス ツールが既にインストールおよび構成されています。 また、Azure のさまざまなデータおよび分析製品を簡単に使用できるようにするツールも含まれます。 Microsoft R Server または SQL Server 2016 を使って、大規模なデータ セットに対する予測モデルを研究および構築できます。 オープン ソース コミュニティや Microsoft の他のツールのホスト、およびサンプル コードとノートブックも含まれます。 次の表は、データ サイエンス仮想マシンの Windows エディションと Linux エディションに含まれる主要なコンポーネントを列記して比較したものです。

| **Windows エディション** | **Linux エディション** |
| -- | --|
| 人気のパッケージがプレインストールされた Microsoft R Open | 人気のパッケージがプレインストールされた Microsoft R Open |
| MicrosoftML アルゴリズムと Microsoft R Operationalization を搭載した Microsoft R Server Developer エディション  |Microsoft R Server Developer エディション |
| Anaconda Python 2.7、3.5 |人気のパッケージがプレインストールされた Anaconda Python 2.7、3.5|
| 人気のパッケージがプレインストールされた JuliaPro | 人気のパッケージがプレインストールされた JuliaPro |
| Jupyter Notebook Server (R、Python、Julia) |JupyterHub: マルチユーザー Jupyter ノートブック (R、Python、Julia、PySpark) |
| SQL Server 2016 SP1 Developer エディション: R サービスによるスケーラブルなデータベース内分析 |PostgreSQL、SQuirreL SQL (データベース ツール)、SQL Server ドライバー、コマンド ライン (bcp、sqlcmd) |
|-  Visual Studio Community エディション 2015 (IDE) </br> - Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools </br> - Node.js、Python、R Tools for Visual Studio (RTVS 0.5) </br>- R Studio Desktop|IDE とエディター </br> - Eclipse と Azure ツールキット プラグイン </br> - Emacs (ESS、auctex 付属) gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio Code|
| Power BI Desktop |-- |
| 機械学習ツール </br> - Azure Machine Learning との統合 </br> - Microsoft Cognitive Toolkit (CNTK 2.0) ディープ ラーニング/AI </br> - Xgboost (データ サイエンス競技で人気の ML ツール) </br> - Vowpal Wabbit (高速オンライン学習製品) </br> - Rattle (ビジュアルなクイック スタート データおよび分析ツール) </br> - Mxnet (ディープ ラーニング/AI) </br> - Tensorflow  |機械学習ツール </br> - Azure Machine Learning との統合 </br> - CNTK (ディープ ラーニング/AI) </br> - Xgboost (データ サイエンス競技で人気の ML ツール) </br> - Vowpal Wabbit (高速オンライン学習製品) </br> - Rattle (ビジュアルなクイック スタート データおよび分析ツール) </br> - Mxnet (ディープ ラーニング/AI)|
| Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK |Azure および Cortana Intelligence Suite のサービスにアクセスするための SDK |
| Azure およびビッグ データ リソースのデータの移動と管理のためのツール: Azure Storage エクスプローラー、CLI、PowerShell、AdlCopy (Azure Data Lake)、AzCopy、dtui (DocumentDB 用)、Microsoft データ管理ゲートウェイ |Azure およびビッグ データ リソースのデータの移動と管理のためのツール: Azure Storage エクスプローラー、CLI |
| Git、Visual Studio Team Services プラグイン |Git |
| GitBash/コマンド プロンプトからアクセスできる最も普及している Linux/Unix コマンドライン ユーティリティの Windows ポート |-- |
| Weka | Weka |
| Drill | Drill |
| --| Spark ローカル |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Windows データ サイエンス仮想マシンを使う方法
* [このページ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)に移動し、緑の **[仮想マシンの作成]** ボタンを選んで、Windows 上に VM のインスタンスを作成します。
* VM を作成するときに指定した資格情報を使って、リモート デスクトップから VM にサインインします。
* 使用できるツールを見つけて起動するには、**[Start]** (開始) メニューをクリックします。

## <a name="get-started-with-the-linux-data-science-vm"></a>Linux データ サイエンス仮想マシンを使う
* [このページ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)に移動し、**[仮想マシンの作成]** ボタンを選んで、Linux (OpenLogic CentOS ベース) 上に VM のインスタンスを作成します。
* VM を作成するときに指定した資格情報を使って、Putty や SSH Command などの SSH クライアントから VM にサインインします。
* シェル プロンプトで、「dsvm-more-info」と入力します。
* グラフィカルなデスクトップの場合、[こちら](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)からお使いのクライアント プラットフォーム用の X2Go クライアントをダウンロードし、Linux データ サイエンス VM のドキュメント「[Linux データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)」の説明に従います。

## <a name="next-steps"></a>次のステップ
### <a name="for-the-windows-data-science-vm"></a>Windows データ サイエンス VM の場合
* Windows バージョンで利用可能な特定のツールを実行する方法について詳しくは、「[Microsoft データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-provision-vm.md)」をご覧ください。
* Windows VM のデータ サイエンス プロジェクトに必要なさまざまなタスクを実行する方法について詳しくは、「[データ サイエンス仮想マシンでできる&10; のこと](machine-learning-data-science-vm-do-ten-things.md)」をご覧ください。

### <a name="for-the-linux-data-science-vm"></a>Linux データ サイエンス VM の場合
* Linux バージョンで利用可能な特定のツールを実行する方法について詳しくは、「[Linux データ サイエンス仮想マシンのプロビジョニング](machine-learning-data-science-linux-dsvm-intro.md)」をご覧ください。
* Linux VM で一般的なデータ サイエンス タスクを実行する方法がわかるチュートリアルについては、「[Linux データ サイエンス仮想マシンでのデータ サイエンス](machine-learning-data-science-linux-dsvm-walkthrough.md)」をご覧ください。


