---
title: クイック スタート:CentOS Linux の作成
titleSuffix: Azure Data Science Virtual Machine
description: 分析と機械学習を行うために、Azure で Linux Data Science Virtual Machine を作成して構成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/16/2018
ms.openlocfilehash: 1ab3a527643bc5a567644a937fdd1eb9e31bf567
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195339"
---
# <a name="quickstart-set-up-a-linux-centos-data-science-virtual-machine-in-azure"></a>クイック スタート:Azure での Linux CentOS Data Science Virtual Machine の設定

Linux Data Science Virtual Machine (DSVM) は、CentOS ベースの Azure 仮想マシンです。 Linux DSVM には、データ分析と機械学習に使用できる一連のツールがプレインストールされています。 

Linux DSVM に含まれている主なソフトウェア コンポーネントは次のとおりです。

* Linux CentOS ディストリビューション オペレーティング システム。
* Microsoft Machine Learning Server。
* 一般的なデータ分析ライブラリを含む Anaconda Python ディストリビューション (バージョン 3.5 および 2.7)。
* JuliaPro (Julia 言語および一般的な科学的およびデータ分析ライブラリの管理されたディストリビューション)。
* Spark スタンドアロン インスタンスと単一ノードの Hadoop (HDFS、YARN)。
* JupyterHub (R、Python、PySpark、Julia カーネルをサポートするマルチユーザー Jupyter Notebook サーバー)。
* Azure Storage Explorer.
* Azure CLI (Azure リソースを管理するための Azure コマンド ライン インターフェイス)。
* PostgresSQL データベース。
* 機械学習ツール:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK): Microsoft Research のディープ ラーニング ソフトウェア ツールキット。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法をサポートする高速機械学習システム。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): 迅速かつ正確なブースト ツリー実装を提供するツール。
  * [Rattle](https://togaware.com/rattle/): R でデータ分析と機械学習を簡単に開始できるツール。 Rattle には、R コードの自動生成を使用した GUI ベースのデータ探索とモデリングが備わっています。
* Java、Python、Node.js、Ruby、PHP の Azure SDK。
* Azure Machine Learning などの Azure サービスで使用する R と Python のライブラリ。
* 開発ツールとエディター (RStudio、PyCharm、IntelliJ、Emacs、gedit、vi)。

データ サイエンスでは、次の一連のタスクを反復処理します。

1. データの検索、読み込み、前処理。
1. モデルのビルドとテスト。
1. インテリジェント アプリケーションで使用するためのモデルの展開。

データ サイエンティストは、こうしたタスクを行うためにさまざまなツールを使用します。 正しいバージョンのソフトウェアを見つけ、ダウンロードし、コンパイルしてインストールするのは時間がかかる場合があります。

Linux DSVM はそうした問題の大部分を解決します。 Linux DSVM を使うと、分析プロジェクトをすぐに開始し、 R、Python、SQL、Java、C++ など、さまざまな言語でタスクを処理できます。 Eclipse には、コードの開発やテストを行うための使いやすい IDE が用意されています。 DSVM に含まれている Azure SDK を使用すると、Microsoft クラウド プラットフォームのさまざまなサービスを Linux で使用してアプリケーションを構築できます。 また、Ruby、Perl、PHP、Node.js など他の言語もプレインストールされています。

この DSVM イメージではソフトウェアの料金は発生しません。 支払うのは、DSVM イメージでプロビジョニングした仮想マシンのサイズを基に評価された Azure のハードウェアの利用料金のみです。 コンピューティング料金の詳細については、Azure Marketplace で [Linux (CentOS) 用 Data Science Virtual Machine のリスト](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)を参照してください。

## <a name="prerequisites"></a>前提条件

Linux Data Science Virtual Machine を作成する前に、次の前提条件を満たす必要があります。

* **Azure サブスクリプション**:Azure サブスクリプションを取得するには、[Azure の無料アカウントの作成](https://azure.microsoft.com/free/)に関するページを参照してください。
* **Azure ストレージ アカウント**:Azure ストレージ アカウントを取得するには、「[ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md)」を参照してください。 既存の Azure ストレージ アカウントを使用したくない場合は、DSVM の作成時にストレージ アカウントを作成できます。

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Data Science Virtual Machine の他のバージョン

Data Science Virtual Machine には、次のバージョンもあります。

* [Ubuntu](dsvm-ubuntu-intro.md):Ubuntu イメージには、ディープ ラーニング フレームワークを含め、CentOS イメージと同じツールが多数あります。 
* [Windows](provision-vm.md)

## <a name="create-a-linux-data-science-virtual-machine"></a>Linux Data Science Virtual Machine を作成する

Linux DSVM のインスタンスを作成するには:

1. [Azure portal](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm) に一覧表示されている仮想マシンに移動します。
1. **[作成]** を選択してウィザードを開きます。

   ![Data Science Virtual Machine を構成するウィザード](./media/linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
1. ウィザードの各ステップで、以下の情報を入力または選択します。
   
   **1** **[基本]** :

      * **[名前]** :作成するデータ サイエンス サーバーの名前です。
      * **[ユーザー名]** : 最初のアカウントのサインイン ID。
      * **パスワード**: 最初のアカウントのパスワード。 (パスワードの代わりに SSH 公開キーを使用できます)。
      * **サブスクリプション**:複数のサブスクリプションがある場合は、マシンが作成されて課金されるサブスクリプションを選択します。 サブスクリプションのリソースを作成するためのアクセス許可が必要です。
      * **[リソース グループ]** :新しいリソース グループを作成するか、既存のグループを使用することができます。
      * **[場所]** :DSVM に使用するデータセンターを選択します。 ほとんどの場合、(ネットワーク アクセスを最速にするために) 物理的に最も近くにあるデータ センターか、データの大半が存在するデータ センターを選びます。
   
   **2** **[サイズ]** :機能の要件とコストの制約を満たしているサーバーの種類を選択します。 **[すべて表示]** を選択すると、VM サイズのさらに多くの選択肢が表示されます。
   
   
   **3** **[設定]** :
      * **[ディスクの種類]** : ソリッドステート ドライブ (SSD) を希望する場合は、 **[Premium]** を選択します。 それ以外の場合は、 **[Standard]** を選択します。
      * **ストレージ アカウント**: サブスクリプションに新しい Azure ストレージ アカウントを作成することも、ウィザードの **[基本]** ステップで選択した場所にある既存の Azure アカウントを使用することもできます。
      * **その他のパラメーター**: その他のパラメーターは、ほとんどの場合、既定値を使用して構成します。 既定値以外の値を確認する場合は、情報リンクにポインターを合わせてパラメーターを表示します。
   
   **4** **[概要]** : 入力した情報が正しいことを確認します。
   
   **5** **[購入]** : プロビジョニングを開始するには、 **[購入]** を選択します。 取引条件へのリンクが表示されます。 DSVM に関して、 **[サイズ]** で選択したサーバー サイズのコンピューティングを超える追加の料金が発生することはありません。

プロビジョニングには 10 から 20 分かかります。 プロビジョニングの状態は、Azure portal に表示されます。

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Linux データ サイエンス仮想マシンにアクセスする方法

作成した DSVM には、SSH を使用してサインインできます。 ウィザードの **[基本]** セクションで作成したアカウントの資格情報をテキスト シェル インターフェイスに使用します。 Windows では、[PuTTY](https://www.putty.org) などの SSH クライアント ツールをダウンロードできます。 グラフィカル デスクトップ (X Window System) を使用する場合は、PuTTY で X11 転送を使用するか、X2Go クライアントをインストールすることができます。

> [!NOTE]
> テストでは、パフォーマンスは、X11 転送よりも、X2Go クライアントの方が優れていました。 グラフィカル デスクトップ インターフェイスでは、X2Go クライアントを使用することをお勧めします。

## <a name="install-and-configure-the-x2go-client"></a>X2Go クライアントをインストールして構成する

Linux DSVM は、X2Go Server を含んだ状態でプロビジョニングされており、クライアント接続を受け入れる準備ができています。 Linux DSVM のグラフィカル デスクトップに接続するには、クライアントで次の手順を実行します。

1. [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)のページから、お使いのクライアント プラットフォーム向けの X2Go クライアントをダウンロードしてインストールします。
1. X2Go クライアントを実行します。 **[New Session]\(新しいセッション\)** を選択します。 複数のタブがある構成ウィンドウが開きます。 次の構成パラメーターを入力します。
   * **[Session] \(セッション) タブ**:
     * **[Host]\(ホスト\)** : Linux DSVM のホスト名または IP アドレスを入力します。
     * **[Login]\(ログイン\)** : Linux DSVM 上のユーザー名を入力します。
     * **[SSH Port]\(SSH ポート\)** : 既定値 (**22**) をそのまま使用します。
     * **[Session Type]\(セッションの種類\)** : 値を **XFCE** に変更します。 現在、Linux DSVM でサポートされるのは XFCE デスクトップのみです。
   * **[Media]\(メディア\) タブ**: 音声のサポートとクライアントの印刷を使用しない場合は、それらをオフにできます。
   * **[Shared folders]\(共有フォルダー\)** : クライアント マシンのディレクトリを Linux DSVM にマウントする場合は、DSVM と共有するクライアント マシンのディレクトリを追加します。

SSH クライアントを使用するか、X2Go クライアントから XFCE グラフィカル デスクトップを使用して DSVM にサインインしたら、DSVM にインストールされ、構成されているツールをすぐに使い始めることができます。 XFCE では、アプリケーション メニューのショートカットとさまざまなツールのデスクトップ アイコンが表示されます。

## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Linux データ サイエンス仮想マシンにインストールされているツール

### <a name="machine-learning-server"></a>Machine Learning Server

R は、データ分析と機械学習に最もよく使われる言語の 1 つです。 分析に R を使用する場合、Microsoft R Open と Math Kernel Library を含む Machine Learning Server が DSVM に用意されています。 Math Kernel Library は、分析アルゴリズムの一般的な算術演算を最適化します。 R Open は CRAN R と完全に互換性があり、CRAN で公開されているすべての R ライブラリを R Open にインストールできます。 

Machine Learning Server を使用することで、R モデルを Web サービスにスケーリングして運用化することができます。 R プログラムは、RStudio、vi、Emacs などの既定のエディターのいずれかで編集できます。 DSVM には、Emacs エディターがプレインストールされています。 Emacs ESS (Emacs Speaks Statistics) パッケージは、Emacs エディターでの R ファイルに対する作業を簡略化します。

R コンソールを開くには、シェルで「**R**」と入力します。このコマンドを入力すると、対話型環境に移動します。 R プログラムを開発するには、通常、Emacs や vi などのエディターを使用し、R でスクリプトを実行します。RStudio には、R プログラムを開発するための完全なグラフィカル IDE が備わっています。

DSVM には、[上位 20 の R パッケージ](https://www.kdnuggets.com/2015/06/top-20-r-packages.html)をインストールするために使用できる R スクリプトが付属しています。 このスクリプトは、R の対話型インターフェイスで実行できます。 前述のように、このインターフェイスを開くには、シェルで「**R**」と入力します。  

### <a name="python"></a>Python

Anaconda Python は、Python 3.5 および 2.7 の環境でインストールされます。 2\.7 環境は _root_ と呼ばれ、3.5 環境は _py35_ と呼ばれます。 このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。

Py35 が既定の環境です。 root (2.7) 環境をアクティブにするには、次のコマンドを使用します。

```bash
source activate root
```

再度 py35 環境をアクティブにするには、次のコマンドを使用します。

```bash
source activate py35
```

Python の対話型セッションを呼び出すには、シェルで「**python**」と入力します。 

Conda または pip を使用して追加の Python ライブラリをインストールします。 pip の場合、既定値が必要ないときは、最初に正しい環境をアクティブにします。

```bash
source activate root
pip install <package>
```

または PIP への完全なパスを指定します。

```bash
/anaconda/bin/pip install <package>
```

Conda の場合は、常に環境名 (py35 または root) を指定する必要があります。

```bash
conda install <package> -n py35
```

グラフィカル インターフェイスを使用している場合や X11 転送を設定している場合は、「**pycharm**」と入力して PyCharm Python IDE を開きます。 既定のテキスト エディターを使用できます。 また、Anaconda Python ディストリビューションにバンドルされている Spyder (Python IDE) を使用することもできます。 Spyder を使用するには、グラフィカル デスクトップまたは X11 転送が必要です。 グラフィカル デスクトップには Spyder へのショートカットがあります。

### <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。 Jupyter Notebook には JupyterHub からアクセスします。 ローカルの Linux ユーザー名とパスワードを使用してサインインします。

Jupyter Notebook サーバーには、Python 2、Python 3、R カーネルがあらかじめ構成されています。 **Jupyter Notebook** デスクトップ アイコンを使用して、ブラウザーを開き、Jupyter Notebook サーバーにアクセスします。 SSH または X2Go クライアント経由で DSVM にアクセスする場合は、https:\//localhost:8000/ で Jupyter Notebook サーバーにアクセスすることもできます。

> [!NOTE]
> 証明書の警告が表示されても続行してください。

Jupyter Notebook サーバーは、どのホストからでもアクセスできます。 「**https:\//\<DSVM DNS 名または IP アドレス\>:8000/** 」と入力してください。

> [!NOTE]
> DSVM がプロビジョニングされるとき、ファイアウォールのポート 8000 が既定で開放されます。 

Microsoft によって、サンプルのノートブックがパッケージ化されています (Python と R にそれぞれ 1 つずつ)。Jupyter Notebook に対し、ローカルの Linux ユーザー名とパスワードを使用して認証を行うと、Jupyter Notebook のホーム ページにサンプルへのリンクが表示されます。 新しいノートブックを作成するには、 **[New]\(新規\)** を選択した後、使用したい言語カーネルを選択します。 **[New]\(新規\)** ボタンが表示されない場合は、左上にある **Jupyter** アイコンを選択して、Notebook サーバーのホーム ページに移動します。

### <a name="spark-standalone"></a>Spark スタンドアロン 

Linux DSVM には Spark スタンドアロン モードのインスタンスがプレインストールされているため、Spark アプリケーションをローカルで開発し、その後テストして大規模なクラスターにデプロイすることができます。 

Jupyter カーネルを使用して PySpark プログラムを実行できます。 Jupyter を開いて **[New]\(新規\)** ボタンを選択すると、利用可能なカーネルの一覧が表示されます。 **Spark - Python** は、Python 言語を使用して Spark アプリケーションを構築するのに便利な PySpark カーネルです。 また PyCharm や Spyder などの Python IDE を使用して Spark プログラムを作成することもできます。 

このスタンドアロン インスタンスでは、Spark スタックが呼び出し元のクライアント プログラム内で実行されます。 この機能により、Spark クラスターでの開発に比べて、問題のトラブルシューティングが高速かつ容易になります。

Jupyter には、サンプルの PySpark Notebook が用意されています。 Jupyter のホーム ディレクトリ下の SparkML ディレクトリにあります ($HOME/notebooks/SparkML/pySpark)。 

R で Spark のプログラミングをする場合は、Machine Learning Server、SparkR、sparklyr を使用できます。 

Machine Learning Server で Spark コンテキストで実行する前に、1 回限りのセットアップ手順を実行して、単一ノードのローカル Hadoop (HDFS と YARN) インスタンスを有効にする必要があります。 Hadoop サービスはインストールされていますが、既定では DSVM で無効になっています。 Hadoop サービスを有効にするには、最初に次のコマンドを root 権限で実行する必要があります。

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop 関連サービスが不要な場合は、`systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn` を実行してサービスを停止することができます。

Machine Learning Server をリモート Spark コンテキスト (DSVM 上のスタンドアロン Spark インスタンス) で開発およびテストする方法を示したサンプルは、/dsvm/samples/MRS ディレクトリで入手できます。

### <a name="ides-and-editors"></a>IDE とエディター

コード エディターには、vi や VIM、Emacs、gedit、PyCharm、RStudio、Eclipse、LaTeX、IntelliJ など複数の選択肢があります。 

* gedit、Eclipse、IntelliJ、R Studio、PyCharm はグラフィカル エディターです。 使用するには、グラフィカル デスクトップにサインインする必要があります。 これらを起動するには、デスクトップとアプリケーションのメニューのショートカットを使用します。

* Vim と Emacs はテキストベースのエディターです。 Emacs の ESS アドオン パッケージにより、Emacs エディター内から R を簡単に利用することができます。 詳細については、[ESS の Web サイト](https://ess.r-project.org/)を参照してください。

* Eclipse は、複数の言語をサポートする拡張可能なオープンソースの IDE です。 DSVM には、Eclipse IDE for Java Developers バージョンがインストールされています。 いくつかの一般的な言語向けのプラグインをインストールすることで環境を拡張できます。 

  DSVM には、Azure Toolkit for Eclipse プラグインが Eclipse と共にインストールされています。 Azure Toolkit for Eclipse を使用すると、Java などの言語がサポートされる Eclipse 開発環境で Azure アプリケーションの作成、開発、テスト、デプロイを行うことができます。

  DSVM には、Azure Toolkit for Eclipse と共に Azure SDK for Java もインストールされています。 Azure SDK for Java を通じて、Java 環境内のさまざまな Azure サービスにアクセスすることができます。 
  
  詳細については、「 [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)」をご覧ください。

* LaTeX は、texlive パッケージによって、[AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) という Emacs アドオン パッケージと共にインストールされます。 このパッケージを使用すると、Emacs 内での LaTeX ドキュメントの作成が容易になります。 

### <a name="databases"></a>データベース

Linux DSVM では、いくつかのデータベースとコマンドライン ツールを利用することができます。

#### <a name="postgressql"></a>PostgresSQL

DSVM では、オープンソース データベースの PostgresSQL を利用できます。PostgresSQL のサービスは実行されており、initdb も完了しています。 データベースとユーザーは自分で作成する必要があります。 詳細については、[PostgresSQL のドキュメント](https://www.postgresql.org/docs/)を参照してください。  

#### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL は、さまざまなデータベース (SQL Server、PostgresSQL、MySQL など) に接続して SQL クエリを実行できるグラフィカルな SQL クライアントです。 SQuirreL SQL は、デスクトップ アイコンを使ってグラフィカル デスクトップ セッションから (X2Go クライアントなどを通じて) 実行できます。 または、シェルから次のコマンドを使用してクライアントを実行することもできます。

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

初めて使用する場合は、先にドライバーとデータベースのエイリアスを設定しておく必要があります。 JDBC ドライバーは /usr/share/java/jdbcdrivers にあります。

詳細については、[SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots) に関するページを参照してください。

#### <a name="command-line-tools-for-accessing-sql-server"></a>SQL Server にアクセスするためのコマンドライン ツール

SQL Server の ODBC ドライバー パッケージには、次の 2 つのコマンド ライン ツールも付属しています。

* **bcp**: bcp ツールでは、SQL Server のインスタンスと、ユーザーが指定した形式のデータ ファイルとの間でデータを一括コピーします。 bcp ツールを使用して、SQL Server テーブルに多数の新しい行をインポートしたり、テーブルからデータ ファイルにデータをエクスポートしたりできます。 テーブルにデータをインポートするには、そのテーブル用に作成されたフォーマット ファイルを使用する必要があります。 または、テーブルの構造を把握し、さらに、その列に有効なデータの種類を把握する必要があります。

  詳細については、[bcp による接続](https://msdn.microsoft.com/library/hh568446.aspx)に関するページを参照してください。

* **sqlcmd**: sqlcmd ユーティリティを使用して、Transact-SQL ステートメントやシステム プロシージャ、スクリプト ファイルをコマンド プロンプトから入力することができます。 sqlcmd ユーティリティでは、ODBC を使用して、Transact-SQL バッチを実行します。

  詳細については、[sqlcmd による接続](https://msdn.microsoft.com/library/hh568447.aspx)に関するページを参照してください。

  > [!NOTE]
  > このツールには、Linux プラットフォームと Windows プラットフォームの間でいくつか違いがあります。 詳細については、 に関するドキュメントを参照してください。

#### <a name="database-access-libraries"></a>データベース アクセス ライブラリ

R と Python には、データベースにアクセスするためのライブラリが用意されています。

* R では、RODBC パッケージまたは dplyr パッケージを使用して、データベース サーバーに対してクエリまたは SQL ステートメントを実行することができます。
* Python では、基盤となるレイヤーとして ODBC を使用したデータベース アクセスが pyodbc ライブラリによって実現されます。

### <a name="azure-tools"></a>Azure ツール

DSVM には、次の Azure ツールがインストールされています。

* **Azure CLI**:Azure のコマンド ライン インターフェイスから、シェル コマンドを通じて Azure リソースを作成したり管理したりできます。 Azure のツールを開くには、「**azure help**」と入力します。 詳細については、 [Azure CLI ドキュメント ページ](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)をご覧ください。
* **Azure Storage Explorer**: Azure Storage Explorer は、Azure ストレージ アカウントに保存されているオブジェクトを参照したり、Azure BLOB との間でデータをアップロードまたはダウンロードしたりするために使用できるグラフィカル ツールです。 ストレージ エクスプローラーには、デスクトップのショートカット アイコンからアクセスできます。 シェル プロンプトから「**StorageExplorer**」と入力して開くこともできます。 X2Go クライアントからサインインしているか、X11 転送を設定済みである必要があります。
* **Azure のライブラリ**: DSVM には、次のライブラリがプレインストールされています。
  
  * **Python**: Python の Azure 関連ライブラリには、*azure*、*azureml*、*pydocumentdb*、*pyodbc* があります。 最初の 3 つのライブラリを使用すると、Azure Storage サービス、Azure Machine Learning、Azure Cosmos DB (Azure の NoSQL データベース) にアクセスできます。 4 番目のライブラリの pyodbc を (Microsoft ODBC Driver for SQL Server と共に) 使用すると、Python から ODBC インターフェイスを使用して SQL Server、Azure SQL Database、Azure SQL Data Warehouse にアクセスできます。 すべてのライブラリを一覧表示するには、「 **pip list** 」と入力します。 このコマンドは、必ず Python 2.7 と 3.5 の両方の環境で実行してください。
  * **R**: R の Azure 関連ライブラリは、AzureML と RODBC です。
  * **Java**: Azure Java ライブラリのリストは、DSVM の /dsvm/sdk/AzureSDKJava ディレクトリにあります。 主要なライブラリは、Azure Storage と管理 API、Azure Cosmos DB、SQL Server の JDBC ドライバーです。  

[Azure portal](https://portal.azure.com) には、プレインストールされている Firefox ブラウザーからアクセスできます。 Azure portal で、Azure リソースを作成、管理、監視できます。

### <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning は、予測分析ソリューションを構築、デプロイ、共有する目的で使用できる、フル マネージドのクラウド サービスです。 実験やモデルは、Azure Machine Learning Studio で作成します。 DSVM 上の Web ブラウザーから Azure Machine Learning にアクセスするには、[Microsoft Azure Machine Learning](https://studio.azureml.net) に移動します。

Azure Machine Learning Studio にサインインしたら、実験キャンバスを使用して機械学習アルゴリズムの論理フローを作成することができます。 また、Azure Machine Learning でホストされている Jupyter Notebook にアクセスすることもできます。 ノートブックで Machine Learning Studio 内の実験をシームレスに操作することができます。 

作成した機械学習モデルを Web サービス インターフェイスにラップすることで、モデルを運用可能な状態にすることができます。 機械学習モデルを運用可能な状態にすると、任意の言語で記述されたクライアントで、機械学習モデルから予測を呼び出すことができます。 詳細については、[Machine Learning のドキュメント](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

DSVM で R または Python を使ってモデルを作成し、Azure Machine Learning の運用環境にデプロイすることもできます。 この機能をサポートする R (**AzureML**) と Python (**azureml**) のライブラリがインストールされています。

R と Python のモデルを Azure Machine Learning にデプロイする方法については、[Data Science Virtual Machine でできる 10 のこと](vm-do-ten-things.md)に関するページを参照してください。

> [!NOTE]
> [Data Science Virtual Machine でできる 10 のこと](vm-do-ten-things.md)に関するページの手順は、Windows バージョンの DSVM 向けに記載されています。 ただし、Azure Machine Learning へのモデルのデプロイに関する情報は Linux DSVM にも当てはまります。

### <a name="machine-learning-tools"></a>機械学習ツール

DSVM には、プリコンパイルされてローカルにプレインストールされたいくつかの機械学習ツールとアルゴリズムが付属しています。 チェックの内容は次のとおりです

* **Microsoft Cognitive Toolkit**: ディープ ラーニングのツールキット。
* **Vowpal Wabbit**: 高速オンライン学習アルゴリズム。
* **XGBoost**: 最適化されたブースト ツリー アルゴリズムを提供するツール。
* **Python**: Anaconda Python には、Scikit-learn などのライブラリと機械学習アルゴリズムがバンドルされています。 その他のライブラリは、 `pip install` を使用してインストールできます。
* **R**: R 向けに、機械学習関数の豊富なライブラリが用意されています。プレインストールされているライブラリには、lm、glm、randomForest、rpart などがあります。 その他のライブラリは、`install.packages(<lib name>)` を実行してインストールできます。

以降のセクションでは、Microsoft Cognitive Toolkit、Vowpal Wabbit、XGBoost について詳しく取り上げます。

#### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit は、オープンソースのディープ ラーニング ツールキットです。 コマンドライン ツール (CNTK) であり、既にパスが通っています。

基本的なサンプルを実行するには、シェルで次のコマンドを実行します。

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

詳細については、[GitHub の CNTK リポジトリ](https://github.com/Microsoft/CNTK)と [CNTK wiki](https://github.com/Microsoft/CNTK/wiki) のページを参照してください。

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit は、オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法を使用する機械学習システムです。

基本的な例でこのツールを実行するには、次のコマンドを実行します。

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit デモ ディレクトリには、他にも、より大きなデモが格納されています。 Vowpal Wabbit の詳細については、[GitHub の Vowpal Wabbit リポジトリ](https://github.com/JohnLangford/vowpal_wabbit)と [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) のページを参照してください。

#### <a name="xgboost"></a>XGBoost

XGBoost は、ブースト (ツリー) アルゴリズム向けに設計および最適化されたライブラリです。 XGBoost ライブラリの目的は、マシンの計算能力の限界を超えて、スケーラブルで移植できる正確で大規模なツリー ブーストを提供することです。

XGBoost はコマンド ラインとして、また R ライブラリとして提供されています。

XGBoost ライブラリを R で使用するには、(シェルで「**R**」と入力して) 対話型の R セッションを開始し、ライブラリを読み込みます。

R プロンプトで実行できる簡単な例を次に示します。

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

XGBoost コマンド ラインを実行するには、シェルで次のコマンドを実行します。

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model ファイルは、指定したディレクトリに書き込まれます。 このデモ サンプルについては、[二項分類](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)に関する GitHub ページを参照してください。

XGBoost の詳細については、[XGBoost のドキュメント](https://xgboost.readthedocs.org/en/latest/)と [XGBoost の GitHub リポジトリ](https://github.com/dmlc/xgboost)を参照してください。

#### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) では、GUI ベースのデータ探索とモデリングを使用します。 Rattle:
- データの統計の概要を視覚的に表示する。
- 簡単にモデル化できるようにデータを変換する。
- データから教師なしと教師ありの両方のモデルを作成する。
- モデルのパフォーマンスをグラフィカルに表示する。
- 新しいデータ セットをスコア付けする。
- R コードを生成する。
- UI での操作をレプリケートする。レプリケートした操作は、R で直接実行したり、詳細な分析の開始点として使用したりすることができます。

Rattle を実行するには、グラフィカル デスクトップ セッションにサインインする必要があります。 ターミナルで「**R**」と入力して、R 環境を開きます。 R プロンプトで、次のコマンドを入力します。

```R
library(rattle)
rattle()
```

一連のタブを備えたグラフィカル インターフェイスが表示されます。 次のクイックスタート手順に従い、Rattle でサンプルの天候データ セットを使用してモデルを構築します。 一部の手順では、必要な R パッケージのうち、システム上にないものを自動的にインストールして読み込むことを確認するメッセージが表示されます。

> [!NOTE]
> システム ディレクトリ (既定) にパッケージをインストールするためのアクセス許可がない場合、R コンソール ウィンドウに、個人用ライブラリにパッケージをインストールするように求めるメッセージが表示されます。 そのメッセージが表示された場合は、「**y**」と入力します。

1. **[Execute (実行)]** を選択します。
1. サンプルの気象データ セットを読み込むよう求めるダイアログ ボックスが表示されます。 **[Yes]\(はい\)** を選択して、サンプルを読み込みます。
1. **[Model]\(モデル\)** タブを選択します。
1. **[Execute]\(実行\)** を選択して、デシジョン ツリーを作成します。
1. **[Draw]\(表示\)** を選択して、デシジョン ツリーを表示します。
1. **[Forest]\(フォレスト\)** オプションをオンにし、 **[Execute]\(実行\)** を選択してランダム フォレストを作成します。
1. **[Evaluate]\(評価\)** タブを選択します。
1. **[Risk]\(リスク\)** オプションをオンにし、 **[Execute]\(実行\)** を選択して、2 つの**リスク (累積)** パフォーマンス プロットを表示します。
1. **[Log]\(ログ\)** タブを選択して、これまでの操作の生成済み R コードを表示します (Rattle の現在のリリースにはバグがあるため、ログのテキストの **Export this log** の前に **#** 文字を挿入する必要があります)。
1. **[Export]\(エクスポート\)** ボタンを選択して、R スクリプト ファイルを *weather_script.R* という名前でホーム フォルダーに保存します。

Rattle と R を終了できます。これで、生成された R スクリプトに変更を加えることができます。 または、スクリプトをそのまま使用することもできます。スクリプトをいつでも実行して、Rattle UI 内で実行されたすべての操作を繰り返すことができます。 これは、特に R の初心者向けの方法です。この方法では、シンプルなグラフィカル インターフェイスで分析と機械学習をすばやく実行することができ、変更または学習する R のコードを自動的に生成できます。

## <a name="next-steps"></a>次の手順

学習と調査をどのように続ければよいかを以下に示します。

* [Linux Data Science Virtual Machine でのデータ サイエンス](linux-dsvm-walkthrough.md)に関するチュートリアルでは、ここでプロビジョニングされた Linux DSVM を使用して、一般的なデータ サイエンス タスクをいくつか実行する方法を示します。 
* この記事で説明しているツールを試して、DSVM 上のさまざまなデータ サイエンス ツールを確認します。 DSVM にインストールされているツールの基本的な概要と詳細情報を入手できる場所は、仮想マシン内のシェルで `dsvm-more-info` を実行して確認することもできます。  
* [Team Data Science Process](https://aka.ms/tdsp)を使用して、エンド ツー エンドの分析ソリューションを体系的に構築する方法を確認します。
* Azure AI サービスを使用する機械学習とデータ分析のサンプルについては、[Azure AI Gallery](https://gallery.azure.ai/) を参照してください。