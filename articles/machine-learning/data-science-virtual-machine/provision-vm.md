---
title: Windows Data Science Virtual Machine を作成する
titleSuffix: Azure
description: 分析と機械学習を行うために、Azure でデータ サイエンス用仮想マシンを構成および作成します。
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: gokuma
ms.openlocfilehash: 0b8349c6c59ac64be15c75b28c083a4483f2f68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57896264"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Azure での Windows Data Science Virtual Machine のプロビジョニング

Microsoft Windows Data Science Virtual Machine (DSVM) は、Azure 上の Windows Server 2016 仮想マシン (VM) イメージであり、データ分析と機械学習のためのツールと共にプレインストールおよび構成されています。

## <a name="included-data-science-tools"></a>含まれるデータ サイエンス ツール

DSVM には次のツールが含まれています。

* [Azure Machine Learning service](../service/index.yml) の Python SDK
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer エディション
* Anaconda Python ディストリビューション
* Jupyter Notebook と R、Python、PySpark カーネル
* Microsoft Visual Studio Community
* Microsoft Power BI Desktop
* Microsoft SQL Server 2017 Developer エディション
* ローカル開発およびテスト用のスタンドアロン Apache Spark インスタンス
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* 機械学習ツールと Data Analytics ツール:
  * ディープ ラーニング フレームワーク - AI フレームワークの豊富なセットが VM 上に含まれています。[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)、[TensorFlow](https://www.tensorflow.org/)、[Chainer](https://chainer.org/)、mxNet、および Keras
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) - オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法をサポートする高速機械学習システム
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/) - 迅速かつ正確なブースト ツリー実装を提供するツール
  * [Rattle](https://togaware.com/rattle/) - R で Data Analytics と機械学習を簡単に開始できる R 分析ツール。GUI ベースのデータ探索と R コードの自動生成によるモデリングを含みます。
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/) - Java のビジュアル データ マイニングと機械学習ソフトウェア
  * [Apache Drill](https://drill.apache.org/) - Apache Hadoop、NoSQL、およびクラウド ストレージ向けのスキーマフリーの SQL クエリ エンジン。 ODBC と JDBC のインターフェイスをサポートし、Power BI、Microsoft Excel、Tableau などの標準の BI ツールから NoSQL やファイルに対してクエリを実行できます。
* Azure Machine Learning などの Azure サービスで使用する R と Python のライブラリ
* GitHub、Azure DevOps を含むソース コード リポジトリを操作する Git Bash を含む Git。 Git では、いくつかの一般的な Linux コマンド ライン ユーティリティが提供されており、Git Bash とコマンド プロンプトの両方でアクセスできます。 awk、sed、perl、grep、find、wget、curl などです。

### <a name="about-data-science"></a>データ サイエンスについて

データ サイエンスでは、次の一連のタスクを反復処理します。

1. データの検索、読み込み、前処理
1. モデルのビルドとテスト
1. インテリジェント アプリケーションで使用するためのモデルのデプロイ

データ サイエンティストは、これらのタスクに複数のツールを使用します。 適切なバージョンのソフトウェアを見つけ、ダウンロードしてインストールするには、時間がかかる場合があります。 DSVM は、プレインストールされ、構成されているいくつかの一般的なツールを使って Azure 上でプロビジョニング可能なすぐに使用できるイメージにより、時間を短縮します。

DSVM を使えば分析プロジェクトをすぐに開始できます。 R、Python、SQL、C# など、さまざまな言語でタスクを処理できます。 Visual Studio では、コードを開発してテストするための使いやすい統合開発環境 (IDE) が提供されています。 Azure SDK が VM に含まれているため、Microsoft のクラウド プラットフォームにあるさまざまなサービスを使用してアプリケーションを構築できます。

このデータ サイエンス VM イメージにソフトウェア課金はありません。 Azure の利用料のみを支払います。 料金は、プロビジョニングする仮想マシンのサイズによって異なります。 コンピューティング料金について詳しくは、[Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) ページの**価格の詳細**に関するセクションをご覧ください。

### <a name="other-dsvm-versions"></a>その他の DSVM バージョン

* [Ubuntu](dsvm-ubuntu-intro.md) イメージ。 DSVM に似た多数のツールに加えて、いくつかの追加のディープ ラーニング フレームワークがあります。
* [Linux CentOS](linux-dsvm-intro.md) イメージ。
* Data Science Virtual Machine の [Windows Server 2012 エディション](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm)。 いくつかのツールは、Windows Server 2016 エディションでのみ使用できます。 その他、この記事は Windows Server 2012 エディションにも該当します。

## <a name="prerequisite"></a>前提条件

Microsoft Data Science Virtual Machine を作成するには、Azure サブスクリプションが必要です。 [Azure 無料試用版の取得](https://azure.com/free)に関するページを参照してください。

## <a name="create-your-dsvm"></a>DSVM を作成する

DSVM インスタンスを作成するには:

1. [Azure portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016) に一覧表示されている仮想マシンに移動します。 まだサインインしていない場合は、Azure アカウントへのサインインを求めるメッセージが表示される可能性があります。
1. 下部にある **[作成]** ボタンを選択します。

   ![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)

1. スクリーンショットの右側のウィンドウに表示されている各手順を構成するために、次の情報を入力する必要があります。

   1. **[基本]**:
      * **[Name]**: DSVM の名前
      * **[VM ディスクの種類]**: **[SSD]** または **[HDD]** のどちらか。 NVidia Tesla K80 ベースなどの NC_v1 GPU インスタンスの場合、ディスクの種類として **[HDD]** を選択します。
      * **[ユーザー名]**: 管理者アカウントの ID
      * **[パスワード]**: 管理者アカウントのパスワード
      * **サブスクリプション**:複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。
      * **リソース グループ**。 新しいリソース グループを作成するか、既存のグループを使用できます。
      * **場所**。 最適なデータ センターを選択します。 ネットワーク アクセスを最速にするために、データの大半が存在するデータ センターか、物理的に最も近いデータ センターを選びます。
   1. **[サイズ]**:機能の要件とコストの制約を満たしている、いずれかのサーバーの種類を選択します。 VM サイズのさらに多くの選択肢を表示するには、**[すべて表示]** を選択します。
   1. **設定**:  
      * **[Managed Disks を使用]**。 Azure で VM のディスクを管理する場合、**[管理済み]** を選択します。 管理しない場合は、新規または既存のストレージ アカウントを指定する必要があります。  
      * **その他のパラメーター**。 既定値を使用できます。 既定値以外の値の使用する場合は、情報リンクにポインターを合わせて、該当するフィールドのヘルプを表示します。
   1. **概要**:入力したすべての情報が正しいことを確認します。 **作成**を選択します。

> [!NOTE]
> * **[サイズ]** ステップで選択したサーバー サイズのコンピューティング コストを超える追加の課金が VM で発生することはありません。
> * プロビジョニングには約 10 ～ 20 分かかります。 Azure portal で VM の状態を表示できます。

## <a name="how-to-access-the-dsvm"></a>DSVM にアクセスする方法

VM を作成してプロビジョニングした後は、前述の **[基本]** セクションで作成した管理者アカウントの資格情報を使って、リモート デスクトップを使用できます。 VM にインストールされて構成されたツールを使い始めることができます。 多くのツールには、スタート メニューのタイルとデスクトップ アイコンからアクセスできます。

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Microsoft データ サイエンス仮想マシンにインストールされているツール

DSVM にインストールされている付属ツールについて説明します。

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer エディション

Machine Learning Server Developer エディションが VM にインストールされているため、分析用のスケーラブルな R または Python に対して Microsoft Enterprise Library を使用できます。 以前は Microsoft R Server と呼ばれていた Machine Learning Server は、広範にデプロイできるエンタープライズ クラスの分析プラットフォームです。 R と Python の両方で使用できます。 また、スケーラブルであり、商用サポートがあり、安全です。

Machine Learning Server では、さまざまなビッグ データ統計、予測モデリング、機械学習のタスクがサポートされます。 幅広い分析機能 (探索、分析、視覚化、モデリングなど) をサポートしています。 Machine Learning Server は、オープン ソースの R と Python を使用して拡張することで、R と Python のスクリプトおよび関数と互換性があります。 また、エンタープライズ規模でのデータ分析のために、CRAN、pip、および Conda パッケージとの互換性もあります。

Machine Learning Server は、データの並列処理とチャンク処理を追加することで、オープン ソース R のメモリ内制限に対処します。 これにより、メイン メモリに収まりきるよりもはるかに大きなデータに対して、分析を実行することができます。 VM には Visual Studio Community が含まれています。 R Tools for Visual Studio と、R または Python を操作するための完全な IDE を提供する Python Tools for Visual Studio (PTVS) 拡張機能が含まれています。 また、[RStudio](http://www.rstudio.com) や [PyCharm Community エディション](https://www.jetbrains.com/pycharm/)などの他の IDE も VM 上に提供しています。

### <a name="python"></a>Python

Python を使用して開発するために、Anaconda Python ディストリビューション 2.7 および 3.6 がインストールされています。 このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、および Data Analytics パッケージが含まれています。 Visual Studio Community 2017 にインストールされている PTVS を使用できます。 または、IDLE や Spyder など、Anaconda でバンドルされている IDE の 1 つを使用できます。 これらのパッケージのいずれかを検索して起動します (Win + S)。

> [!NOTE]
> Anaconda Python 2.7 で Python Tools for Visual Studio をポイントするには、各バージョン用のカスタム環境を作成する必要があります。 Visual Studio 2017 Community でこれらの環境パスを設定するには、**[ツール]** > **[Python ツール]** > **[Python 環境]** に移動します。 そして、**[+ カスタム]** を選択します。

Anaconda Python 3.6 は、**C:\Anaconda** にインストールされています。 Anaconda Python 2.7 は、**c:\Anaconda\envs\python2** にインストールされています。 詳細な手順については、[PTVS のドキュメント](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)をご覧ください。

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。 Jupyter Notebook サーバーには、Python 2.7、Python 3.x、PySpark、Julia、および R カーネルがあらかじめ構成されています。 Jupyter サーバーを開始し、ブラウザーを起動して Notebook サーバーにアクセスするには、**[Jupyter Notebook]** デスクトップ アイコンを使用します。

Python および R には、複数のサンプル ノートブックがパッケージングされています。Jupyter にアクセスすると、以下のテクノロジの使用方法がノートブックに表示されます。

* Machine Learning Server
* SQL Server Machine Learning Services、データベース内分析
* Python
* Microsoft Cognitive ToolKit
* Tensorflow
* その他の Azure テクノロジ

前の手順で作成したパスワードを使用して Jupyter Notebook を認証すると、ノートブックのホーム ページにサンプルへのリンクがあります。

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM には Visual Studio Community が含まれています。 これは Microsoft の定評ある IDE の無料版であり、評価用または小規模なチーム用に使用できます。 [ライセンス条項](https://www.visualstudio.com/support/legal/mt171547)をご覧ください。

デスクトップ アイコンまたは **[スタート]** メニューを使用して Visual Studio を開きます。 プログラムを探し (Win + S)、**[Visual Studio]** を探します。 そこから、C#、Python、R、node.js などの言語でプロジェクトを作成できます。 インストールされているプラグインでは、次の Azure サービスを簡単に使用できます。

* Azure Data Catalog
* Azure HDInsight Hadoop と Spark
* Azure Data Lake

Azure Machine Learning とシームレスに統合し、AI アプリケーションの迅速な構築に役立つ、**Azure Machine Learning for Visual Studio Code** というプラグインもあります。

> [!NOTE]
> 評価期間が終了したことを示すメッセージが表示される場合があります。 ご利用の Microsoft アカウントの資格情報を入力してください。 または、新しい無料アカウントを作成して Visual Studio Community にアクセスします。

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer エディション

DSVM には、SQL Server 2017 Developer エディションと Machine Learning Services が付属します。 このエディションの SQL Server は R または Python のどちらかで提供され、データベース内分析を実行できます。 Machine Learning Services はインテリジェント アプリケーションを開発、デプロイするためのプラットフォームです。 これらの言語とコミュニティの多くのパッケージを使用して、SQL Server データを使ったモデルの作成や予測の生成ができます。 Machine Learning Services (データベース内) は SQL Server 内で R と Python の両方の言語を統合するので、分析とデータを緊密に保つことができます。 この統合により、データ移動に伴うコストとセキュリティ リスクの低減ができます。

> [!NOTE]
> SQL Server Developer エディションは、開発およびテスト目的専用です。 運用環境で実行するためにはライセンスが必要です。

SQL Server にアクセスするには、Microsoft SQL Server Management Studio を起動します。 VM の名前が**サーバー名**として設定されます。 Windows の管理者としてサインインする場合は、Windows 認証を使用します。 SQL Server Management Studio にアクセスできたら、他のユーザーの作成、データベースの作成、データのインポート、SQL クエリの実行などを行うことができます。

SQL Machine Learning Services を使ったデータベース内分析を有効にするには、SQL Server Management Studio でサーバー管理者としてサインインした後、次のコマンドを、1 回限りのアクションとして実行します。

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

`%COMPUTERNAME%` を VM の名前に置き換えます。

### <a name="azure"></a>Azure

VM には複数の Azure ツールがインストールされます。

* デスクトップのショートカットは、Azure SDK のドキュメントに移動します。
* Azure Storage アカウントとの間でデータをコピーするには、**AzCopy** を使用します。 使用方法を表示するには、コマンド プロンプトで「**Azcopy**」と入力します。
* Azure Storage アカウントに格納したオブジェクトを参照するには、**Azure Storage Explorer** を使用します。 Azure Storage との間でデータのコピーも行います。 このツールにアクセスするには、**[検索]** フィールドに「**Storage Explorer**」と入力します。 または、Windows の **[スタート]** メニューから探します。
* **Adlcopy** は、Azure Data Lake にデータをコピーします。 使用方法を見るには、コマンド プロンプトで「**adlcopy**」と入力します。
* **dtui** は、クラウド上の NoSQL データベースである Azure Cosmos DB との間でデータをコピーします。 コマンド プロンプトで「**dtui**」と入力します。
* **Azure Data Factory Integration Runtime** は、オンプレミスのデータ ソースとクラウドの間でデータをコピーします。 Azure Data Factory のようなツール内で使用されます。
* PowerShell スクリプト言語で Azure リソースを管理するには、**Microsoft Azure PowerShell** を使用します。 やはり VM にインストールされます。

### <a name="power-bi"></a>Power BI

ダッシュボードの構築と高度な視覚化を支援するために、**Power BI Desktop** が DSVM に付属してインストールされます。 このツールを使用すると、他のソースからデータを抽出し、ダッシュボードとレポートを作成し、それらをクラウドに公開することができます。 詳しくは、[Power BI](https://powerbi.microsoft.com) のサイトをご覧ください。 Power BI Desktop は **[スタート]** メニューにあります。

> [!NOTE]
> Power BI にアクセスするには、Microsoft Office 365 アカウントが必要です。

### <a name="azure-machine-learning-service-python-sdk"></a>Azure Machine Learning service の Python SDK

データ サイエンティストと AI 開発者は、Python 用 Azure Machine Learning SDK を使用して、[Azure Machine Learning service](../service/overview-what-is-azure-ml.md) で機械学習のワークフローをビルドして実行します。 TensorFlow や scikit-learn などのオープンソース フレームワークを使用して、Jupyter Notebooks または別の Python IDE でサービスとやりとりできます。

Python SDK を使用して利用を開始するには、「[Python SDK を使用して Azure Machine Learning の利用を開始する](../service/quickstart-create-workspace-with-python.md)」を参照してください。

Python SDK は、Microsoft Data Science Virtual Machine にプレインストールされています。

## <a name="more-microsoft-development-tools"></a>その他の Microsoft 開発ツール

[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して、他の Microsoft 開発ツールを探してダウンロードできます。 Microsoft Data Science Virtual Machine のデスクトップにはツールのショートカットもあります。  

## <a name="important-directories-on-the-vm"></a>VM 上の重要なディレクトリ

| Item | Directory |
| --- | --- |
| Jupyter Notebook サーバーの構成 | C:\ProgramData\jupyter |
| Jupyter Notebook のサンプルのホーム ディレクトリ | C:\dsvm\notebooks および c:\users\\<username\>\notebooks |
| その他のサンプル | C:\dsvm\samples |
| Anaconda、既定: Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 環境 | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (スタンドアロン) Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 既定の R インスタンス、Machine Learning Server (スタンドアロン) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Machine Learning Services のデータベース内インスタンスのディレクトリ | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| その他のツール | C:\dsvm\tools |

> [!NOTE]
> DSVM の Windows Server 2012 エディションおよび 2018 年 3 月以前の Windows Server 2016 ディションでは、既定の Anaconda 環境は Python 2.7 です。 セカンダリ環境は、**C:\Anaconda\envs\py35** に配置されている Python 3.5 です。

## <a name="next-steps"></a>次の手順

* **[スタート]** メニューを選択して、Data Science VM でツールを調べます。
* Azure Machine Learning service については、「[Azure Machine Learning サービスの概要](../service/overview-what-is-azure-ml.md)」を参照し、利用できる[クイック スタートとチュートリアル](../service/index.yml)をお試しください。
* エクスプローラーで **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** に移動し、エンタープライズ規模での Data Analytics をサポートする R で RevoScaleR ライブラリを使用するサンプルを入手します。  
* 記事「[Windows データ サイエンス仮想マシンでできる 10 のこと](https://aka.ms/dsvmtenthings)」を読みます。
* [Team Data Science Process](../team-data-science-process/index.yml)を使用して、エンド ツー エンドの分析ソリューションを体系的に構築する方法を確認します。
* Azure Machine Learning および関連する Azure 上のデータ サービスを使用した機械学習および Data Analytics については、[Azure AI Gallery](https://gallery.cortanaintelligence.com) をご覧ください。 **[スタート]** メニューと仮想マシンのデスクトップに、このギャラリーへのアイコンも用意されています。
