---
title: Azure Notebooks の概要
description: 無料の Azure Notebooks サービスを使用して、クラウド上で Jupyter Notebook を実行します。セットアップも構成も不要です。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/05/2019
ms.author: kraigb
ms.openlocfilehash: 4840a9839fe1f2a31470d4a67b3755b82077fd90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280113"
---
# <a name="overview-of-azure-notebooks"></a>Azure Notebooks の概要

Azure Notebooks は、インストールせずにクラウド上で Jupyter ノートブックを作成、実行するための無料でホストされているサービスです。 [Jupyter](https://jupyter.org/) (旧称 IPython) は、マークダウン テキスト、実行可能コード、永続データ、グラフィックス、およびグラフを組み合わせて "*ノートブック*" (画像提供: jupyter.org) と呼ばれる 1 つの共有可能なキャンバスを簡単に作成できるオープン ソース プロジェクトです。

[![Jupyter Notebooks の例](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

コード、グラフィックス、および説明テキストのこの強力な組み合わせにより、Jupyter は、データ サイエンス教育、データのクリーニングと変換、数値シミュレーション、統計モデリング、機械学習モデルの開発などを行う多数のユーザーの間で一般に使用されるようになっています。

## <a name="hassle-free-experience"></a>手間のかからないエクスペリエンス

Azure Notebooks の使用は、プロトタイプ作成、データ サイエンス、学術研究、または Python のプログラミング学習をすばやく開始するのに役立ちます。

- データ サイエンティストは、インストールなしで Anaconda 環境全体にすぐにアクセスできます。
- 教師は、手間のかからない Python 環境を学生に提供できます。
- プレゼンターは、出席者に 45 分かけてソフトウェアをインストールするように依頼することなく、ウェビナーなどを実施できます。
- 開発者や愛好家は、コードをすぐに記述するためのスクラッチパッドとして Notebooks を使用できます。

ユーザーが Azure Notebooks (プレビュー段階) などのブラウザーでアクセスできるクラウド サービスを通して共同作業を行うと、ノートブックの力がさらに発揮されます。 クラウドでは、ユーザーは、Jupyter をローカルにインストールする必要も、環境の管理を意識する必要もありません。 さらに、クラウドでは、他の承認されたユーザーとノートブック (および関連付けられたデータ ファイル) を簡単に共有でき、ソース管理リポジトリなどの外部手段を使用したノートブックの共有の複雑さを回避できます。 ユーザーは、Azure Notebooks を使用して、改変や実験のためにノートブックを自分のアカウントにコピー (複製) することもできます。これは教育が目的の場合に特に有用です。

Azure Notebooks はコードを作成、実行、および共有するための汎用プラットフォームであるため、多くのさまざまなシナリオで使用できます。

- 新しいプログラミング言語を学習する – [入門チュートリアル](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)のいずれかをお試しください。
- データ サイエンスを学習する – [Jake VanderPlas の書籍](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)をご覧ください。
- 何百人もの受講者に[コースを提供する](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- インストール時間を費やすことなく、オンラインまたは会議でウェビナーを実施する 
- [GitHub 起動バッジを作成](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)することで、GitHub ユーザーがノートブックを直接読み込んで実行できるようにする
- スライド内のコードが実行可能であるときに [PowerPoint のようなスライドショー](https://notebooks.azure.com/help/jupyter-notebooks/slides)を表示する

つまり、Azure Notebooks を使用すれば、作業をさらに効率的に進めることができ、その結果、さらに多くの成果を上げるのに役立ちます。

> [!Note]
> Jupyter 自体の詳細については、[jupyter.org](https://jupyter.org/)、および [Jupyter のドキュメント](https://jupyter-notebook.readthedocs.io/en/latest/)で確認できます。

## <a name="pricing-and-quotas"></a>価格とクォータ

Azure Notebooks は無料のサービスですが、乱用を防ぐために各プロジェクトのメモリは 4 GB、データは 1 GB に制限されています。 これらの制限を超える正当なユーザーは、CAPTCHA チャレンジをクリアしてノートブックの実行を継続してください。

すべての制限を解除するには、Azure Active Directory を使用したアカウント (会社のアカウントなど) で Azure Notebooks にサインインします。 そのアカウントが Azure サブスクリプションに関連付けられている場合は、そのサブスクリプション内の任意の Azure Data Science Virtual Machine インスタンスに接続できます。 詳細については、「プロジェクトの管理と構成」の「[Compute tier (コンピューティング レベル)](configure-manage-azure-notebooks-projects.md#compute-tier)」を参照してください。

Notebook サーバーは、最大で 8 時間存在することが保証されます。 ほとんどの場合、コンテナーはこの制限を受けず、この期間を超えて実行を続けます。ただし、システムの安定性のために、有効期間の長いセッションがシャットダウンされることがあります。

## <a name="available-kernels-and-environments"></a>利用可能なカーネルと環境

各ノートブックでは、コード セルを実行するために使用されるカーネル (つまり、ランタイム環境) を選択します。 Azure Notebooks では、次のカーネルをサポートしています。

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (非推奨になる予定)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks には、基本ディストリビューションの他に、追加のパッケージも含まれています。 たとえば、Python カーネルには、numpy、pandas、scikit-learn、matplotlib、および bokeh ライブラリが含まれています。

プロジェクトをカスタマイズして、そのプロジェクトに含まれるすべてのノートブック用の環境を作成することもできます。 詳細については、「[クイック スタート: カスタム環境でのプロジェクトの作成](quickstart-create-jupyter-notebook-project-environment.md)」を参照してください。

基本ディストリビューションに加え、Azure Notebooks には、データ サイエンティストにとって有用な多くのパッケージが事前インストールされています。 各言語の一般的なプロセスを使用して、ご自分のパッケージをインストールすることもできます。

## <a name="pre-configured-jupyter-extensions"></a>事前構成済みの Jupyter の拡張機能

Azure Notebooks では、次の Jupyter の拡張機能が事前構成されています。

- [RISE](https://github.com/damianavila/RISE): Jupyter のスライド ショー拡張機能 (live_reveal とも呼ばれます)。 詳細については、「[Run a notebook slideshow](present-jupyter-notebooks-slideshow.md)」(ノートブックのスライドショーを実行する) を参照してください。
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Jupyter Notebook を操作するための完全なコンピューティング環境。
- [Altair](https://github.com/ellisonbg/altair): Python 用の宣言型統計可視化ライブラリです。
- [BQPlot](https://github.com/bloomberg/bqplot): Jupyter Notebook 用の対話型プロット フレームワーク。
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Jupyter Notebook 用の対話型 HTML ウィジェット。

## <a name="issues-and-getting-help"></a>問題とヘルプの取得

Azure Notebooks はまだプレビュー段階であるため、一時的な障害が他の Azure サービスよりも頻繁に発生したり、長時間続いたりする可能性があります。 不完全な機能やバグが含まれている機能もあります。

現時点では、ビジネスに不可欠なアプリケーションで使用したり、重要性の高いノートブックや機微なデータを処理したりしないことをお勧めします。

Azure Notebooks に関する質疑応答を行うには、[GitHub リポジトリ](https://github.com/Microsoft/AzureNotebooks/issues)に問題を送信してください。

## <a name="next-steps"></a>次の手順  

- [サンプル ノートブックの調査](azure-notebooks-samples.md)

- クイック スタート:

  - [ノートブックの作成と共有](quickstart-create-share-jupyter-notebook.md)
  - [ノートブックの複製](quickstart-clone-jupyter-notebook.md)
  - [ローカルの Jupyter ノートブックの移行](quickstart-migrate-local-jupyter-notebook.md)
  - [カスタム環境の使用](quickstart-create-jupyter-notebook-project-environment.md)
  - [サインインとユーザー ID の設定](quickstart-sign-in-azure-notebooks.md)

- チュートリアル:

  - [ノートブックの作成と実行](tutorial-create-run-jupyter-notebook.md  )

- ハウツー記事:
  
  - [プロジェクトの作成と複製](create-clone-jupyter-notebooks.md)
  - [デバイスの構成と管理](configure-manage-azure-notebooks-projects.md)
  - [ノートブック内からのパッケージのインストール](install-packages-jupyter-notebook.md)
  - [スライド ショーの表示](present-jupyter-notebooks-slideshow.md)
  - [データ ファイルの操作](work-with-project-data-files.md)
  - [データ リソースへのアクセス](access-data-resources-jupyter-notebooks.md)
  - [Azure Machine Learning Services の使用](use-machine-learning-services-jupyter-notebooks.md)
