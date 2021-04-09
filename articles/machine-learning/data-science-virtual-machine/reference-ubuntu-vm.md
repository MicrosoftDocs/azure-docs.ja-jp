---
title: 'リファレンス: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Ubuntu Data Science Virtual Machine に含まれるツールの詳細
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661071"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>リファレンス: Ubuntu (Linux) Data Science Virtual Machine

Ubuntu Data Science Virtual Machine で使用可能なツールの一覧については、以下を参照してください。 

## <a name="deep-learning-libraries"></a>ディープ ラーニング ライブラリ

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit は、オープンソースのディープ ラーニング ツールキットです。 Python バインディングは、ルートと py35 の Conda 環境で使用できます。 あらかじめパスが通ったコマンドライン ツール (CNTK) も用意されています。

サンプルの Python Notebook は、JupyterHub で利用できます。 基本的なサンプルをコマンド ラインで実行するには、シェルで次のコマンドを実行します。

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

詳細については、[GitHub](https://github.com/Microsoft/CNTK) の CNTK セクションと [CNTK の Wiki](https://github.com/Microsoft/CNTK/wiki) を参照してください。

### <a name="caffe"></a>Caffe

Caffe は、Berkeley Vision and Learning Center が開発したディープ ラーニング フレームワークです。 /opt/caffe で入手できます。 サンプルは、/opt/caffe/examples にあります。

### <a name="caffe2"></a>Caffe2

Caffe2 は、Caffe に基づいて構築された Facebook のディープ ラーニング フレームワークです。 Conda ルート環境の Python 2.7 で使用できます。 これをアクティブにするには、シェルから次のコマンドを実行します。

```bash
source /anaconda/bin/activate root
```

JupyterHub でいくつかの Notebook サンプルを入手できます。

### <a name="h2o"></a>H2O

H2O は、高速でインメモリの分散型機械学習と予測分析のプラットフォームです。 Python パッケージは、ルートと py35 両方の Anaconda 環境にインストールされます。 また、R パッケージもインストールされます。 

コマンド ラインから H2O を起動するには、`java -jar /dsvm/tools/h2o/current/h2o.jar` を実行します。 多様な[コマンドライン オプション](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)があり、必要に応じて構成できます。 Flow Web UI にアクセスするには、まず `http://localhost:54321` にアクセスします。 サンプルの Notebook は、JupyterHub でも利用できます。

### <a name="keras"></a>Keras

Keras は、Python で書かれた高度なニューラル ネットワーク API です。 TensorFlow、Microsoft Cognitive Toolkit、Theano 上で実行できます。 ルートと py35 の Python 環境で使用できます。

### <a name="mxnet"></a>MXNet

MXNet は、効率性と柔軟性の両方のために設計されたディープ ラーニング フレームワークです。 また MXNet には、DSVM に含まれている R バインディングと Python バインディングもあります。 サンプル Notebook は JupyterHub に含まれており、サンプル コードは /dsvm/samples/mxnet で入手できます。

### <a name="nvidia-digits"></a>NVIDIA DIGITS

DIGITS として知られる NVIDIA ディープ ラーニング GPU トレーニング システムは、一般的なディープ ラーニング タスクを簡略化するシステムです。 そうしたタスクには、データの管理や、GPU システムでのニュートラル ネットワークの設計とトレーニング、リアル タイムでのパフォーマンスの監視と高度な視覚化が挙げられます。

DIGITS は、"*digits (ディジッツ)* " というサービスとして利用できます。 サービスを開始するには、`http://localhost:5000` を参照します。

また DIGITS は、Conda root 環境に Python モジュールとしてインストールされます。

### <a name="tensorflow"></a>TensorFlow

TensorFlow は Google のディープ ラーニング ライブラリです。 データ フロー グラフを使用した数値計算用のオープンソース ソフトウェア ライブラリです。 TensorFlow は py35 の Python 環境で使用でき、いくつかのサンプル Notebook が JupyterHub に含まれています。

### <a name="theano"></a>Theano

Theano は、Python のための効率的な数値計算用ライブラリです。 ルートと py35 の Python 環境で使用できます。 

### <a name="torch"></a>Torch

Torch は、機械学習アルゴリズムを幅広くサポートする科学コンピューティング フレームワークです。 /dsvm/tools/torch で入手でき、**th** 対話型セッションと LuaRocks パッケージ マネージャーはコマンド ラインで利用できます。 サンプルは、/dsvm/samples/torch で入手できます。

また PyTorch も ルート Anaconda 環境で使用できます。 サンプルは、/dsvm/samples/pytorch にあります。

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R は、データ分析と機械学習に最もよく使われる言語の 1 つです。 分析に R を使用する場合、Microsoft R Open と Math Kernel Library を含む Microsoft Machine Learning Server が VM に用意されています。 Math Kernel Library は、分析アルゴリズムの一般的な算術演算を最適化します。 Microsoft R Open は CRAN-R と完全に互換性があり、CRAN で公開されているすべての R ライブラリを Microsoft R Open にインストールできます。 

Machine Learning Server は R モデルのスケーリングと運用化を Web サービスにもたらします。 R プログラムは、RStudio、vi、Emacs などの既定のエディターのいずれかで編集できます。 Emacs エディターを使用する場合、これはプレインストールされています。 Emacs ESS (Emacs Speaks Statistics) パッケージは、Emacs エディター内での R ファイルに対する作業を簡略化します。

R コンソールを開くには、シェルで「**R**」と入力します。 このコマンドを入力すると、対話型環境に移動します。 R プログラムを開発するには、通常、Emacs や vi などのエディターを使用し、R 内でスクリプトを実行します。RStudio をインストールすると、完全なグラフィカル IDE で R プログラムを開発できます。

必要な場合に備えて、[上位 20 の R パッケージ](https://www.kdnuggets.com/2015/06/top-20-r-packages.html)をインストールするための R スクリプトも用意されています。 このスクリプトを実行するには、R の対話型インターフェイスに入る必要があります。 前述のように、シェルで「**R**」と入力することによって、このインターフェイスを開くことができます。  

## <a name="python"></a>Python

Anaconda Python は、Python 2.7 および 3.5 の環境でインストールされます。 2\.7 環境は _root_ と呼ばれ、3.5 環境は _py35_ と呼ばれます。 このディストリビューションには、基本 Python と、約 300 の最も一般的な数学、エンジニアリング、およびデータ分析パッケージが含まれています。

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

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda ディストリビューションは、コードと分析を共有するための環境である Jupyter Notebook にも付属しています。 Jupyter Notebook には JupyterHub からアクセスします。 ローカルの Linux ユーザー名とパスワードを使用してサインインします。

Jupyter Notebook サーバーは、Python 2、Python 3、R カーネルであらかじめ構成されています。 ブラウザーを開いて Notebook サーバーにアクセスするには、**Jupyter Notebook** デスクトップ アイコンを使用します。 SSH または X2Go クライアント経由で VM を使用している場合は、`https://localhost:8000/` で Jupyter Notebook サーバーにアクセスすることもできます。

> [!NOTE]
> 証明書の警告が表示されても続行してください。

Jupyter Notebook サーバーは、どのホストからでもアクセスできます。 **https://\<VM DNS name or IP address\>:8000/** と入力します。

> [!NOTE]
> ポート 8000 は、VM がプロビジョニングされるときに、ファイアウォールの既定で開けてあります。 

サンプルの Notebook がパッケージ化されています (Python と R にそれぞれ 1 つずつ)。Jupyter Notebook に対し、ローカルの Linux ユーザー名とパスワードを使用して認証を行うと、Jupyter Notebook のホーム ページにサンプルへのリンクが表示されます。 **[New]\(新規\)** を選択し、適切な言語カーネルを選択して、新しいノートブックを作成できます。 **[New]\(新規\)** ボタンが表示されない場合は、左上にある **Jupyter** アイコンを選択して、Notebook サーバーのホーム ページに移動します。

## <a name="apache-spark-standalone"></a>Apache Spark スタンドアロン

Linux DSVM には Apache Spark のスタンドアロン インスタンスがプレインストールされているため、Spark アプリケーションをローカルで開発し、その後テストして大規模なクラスターにデプロイすることができます。 

Jupyter カーネルを使用して PySpark プログラムを実行できます。 Jupyter を開いて **[New]\(新規\)** ボタンを選択すると、利用可能なカーネルの一覧が表示されます。 **Spark - Python** は、Python 言語を使用して Spark アプリケーションを構築するのに便利な PySpark カーネルです。 また PyCharm や Spyder などの Python IDE を使用して Spark プログラムを作成することもできます。 

このスタンドアロン インスタンスでは、Spark スタックが呼び出し元のクライアント プログラム内で実行されます。 この機能により、Spark クラスターでの開発に比べて、問題のトラブルシューティングが高速かつ容易になります。

Jupyter には、サンプルの PySpark Notebook が用意されています。 Jupyter のホーム ディレクトリ下の SparkML ディレクトリにあります ($HOME/notebooks/SparkML/pySpark)。 

R で Spark のプログラミングをする場合は、Microsoft Machine Learning Server、SparkR、sparklyr を使用できます。 

Microsoft Machine Learning Server で Spark コンテキストで実行する前に、1 回限りのセットアップ手順を実行して、単一ノードのローカル Hadoop (HDFS と Yarn) インスタンスを有効にする必要があります。 Hadoop サービスはインストールされていますが、既定では DSVM で無効になっています。 これを有効にするには、最初に次のコマンドを root 権限で実行する必要があります。

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Hadoop 関連サービスが不要な場合は、```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` を実行してサービスを停止することができます。

Microsoft Machine Learning Server をリモート Spark コンテキスト (DSVM 上のスタンドアロン Spark インスタンス) で開発およびテストする方法を示したサンプルは、/dsvm/samples/MRS ディレクトリで入手できます。

## <a name="ides-and-editors"></a>IDE とエディター

コード エディターには、vi や Vim、Emacs、PyCharm、RStudio、IntelliJ など複数の選択肢があります。 

PyCharm、RStudio、IntelliJ はグラフィカル エディターです。 使用するには、グラフィカル デスクトップにサインインする必要があります。 これらを起動するには、デスクトップとアプリケーションのメニューのショートカットを使用します。

Vim と Emacs はテキストベースのエディターです。 Emacs の ESS アドオン パッケージにより、Emacs エディター内から R を簡単に利用することができます。 詳細については、[ESS の Web サイト](https://ess.r-project.org/)を参照してください。

LaTex は、texlive パッケージによって、[AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) という Emacs アドオン パッケージと共にインストールされます。 このパッケージを使用すると、Emacs 内での LaTeX ドキュメントの作成が容易になります。  

## <a name="databases"></a>データベース

### <a name="graphical-sql-client"></a>グラフィカル SQL クライアント

グラフィカルな SQL クライアントである SQuirrel SQL は、さまざまなデータベース (Microsoft SQL Server、MySQL など) に接続し、SQL クエリを実行することができます。 SQuirrel SQL は、デスクトップ アイコンを使ってグラフィカル デスクトップ セッションから (X2Go クライアントなどを通じて) 実行できます。 または、シェルから次のコマンドを使用してクライアントを実行することもできます。

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

初めて使用する場合は、先にドライバーとデータベースのエイリアスを設定しておく必要があります。 JDBC ドライバーは /usr/share/java/jdbcdrivers にあります。

詳細については、「 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)」をご覧ください。

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Server にアクセスするためのコマンド ライン ツール

SQL Server の ODBC ドライバー パッケージには、次の 2 つのコマンド ライン ツールも付属しています。

- **bcp**:bcp ツールでは、Microsoft SQL Server のインスタンスと、ユーザーが指定した形式のデータ ファイルとの間でデータを一括コピーします。 bcp ツールを使用して、SQL Server テーブルに多数の新しい行をインポートしたり、テーブルからデータ ファイルにデータをエクスポートしたりできます。 テーブルにデータをインポートするには、そのテーブル用に作成されたフォーマット ファイルを使用する必要があります。 または、テーブルの構造を把握し、さらに、その列に有効なデータの種類を把握する必要があります。

  詳細については、「 [bcp による接続](/sql/connect/odbc/linux-mac/connecting-with-bcp)」をご覧ください。

- **sqlcmd**:sqlcmd ツールを使用して Transact-SQL ステートメントを入力できます。 または、コマンド プロンプトからシステム プロシージャやスクリプト ファイルを入力することもできます。 このツールは ODBC を使用して、Transact-SQL バッチを実行します。

  詳細については、「 [sqlcmd による接続](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)」をご覧ください。

  > [!NOTE]
  > このツールには、Linux プラットフォームと Windows プラットフォームの間でいくつか違いがあります。 詳細については、 に関するドキュメントを参照してください。

### <a name="database-access-libraries"></a>データベース アクセス ライブラリ

R と Python には、データベースにアクセスするためのライブラリが用意されています。

* R では、RODBC パッケージまたは dplyr パッケージを使用して、データベース サーバーに対してクエリまたは SQL ステートメントを実行することができます。
* Python では、基盤となるレイヤーとして ODBC を使用したデータベース アクセスが pyodbc ライブラリによって実現されます。  

## <a name="azure-tools"></a>Azure ツール

VM には、次の Azure ツールがインストールされています。

* **Azure CLI**:Azure のコマンド ライン インターフェイスから、シェル コマンドを通じて Azure リソースを作成したり管理したりできます。 Azure のツールを開くには、「**azure help**」と入力します。 詳細については、 [Azure CLI ドキュメント ページ](/cli/azure/get-started-with-az-cli2)をご覧ください。
* **Azure Storage Explorer**: Azure Storage Explorer は、Azure ストレージ アカウントに保存されているオブジェクトを参照したり、Azure BLOB との間でデータをアップロードまたはダウンロードしたりするために使用できるグラフィカル ツールです。 ストレージ エクスプローラーには、デスクトップのショートカット アイコンからアクセスできます。 シェル プロンプトから「**StorageExplorer**」と入力して開くこともできます。 X2Go クライアントからサインインしているか、X11 転送を設定済みである必要があります。
* **Azure のライブラリ**: プレインストールされているライブラリの一部を次に示します。
  
  * **Python**: Python の Azure 関連ライブラリには、*azure*、*azureml*、*pydocumentdb*、*pyodbc* があります。 最初の 3 つのライブラリを使用すると、Azure Storage サービス、Azure Machine Learning、Azure Cosmos DB (Azure の NoSQL データベース) にアクセスできます。 4 番目のライブラリである pyodbc を (Microsoft ODBC Driver for SQL Server と共に) では、ODBC インターフェイスを使用して、Python から SQL Server、Azure SQL Database、Azure Synapse Analytics にアクセスできます。 すべてのライブラリを一覧表示するには、「 **pip list** 」と入力します。 このコマンドは、必ず Python 2.7 と 3.5 の両方の環境で実行してください。
  * **R**:R の Azure 関連ライブラリは、AzureML と RODBC です。
  * **Java**: Azure Java ライブラリのリストは、VM の /dsvm/sdk/AzureSDKJava ディレクトリにあります。 主要なライブラリは、Azure Storage と管理 API、Azure Cosmos DB、SQL Server の JDBC ドライバーです。  

[Azure ポータル](https://portal.azure.com) には、プレインストールされている Firefox ブラウザーからアクセスできます。 Azure ポータルでは、Azure リソースを作成、管理、監視できます。

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning は、予測分析ソリューションを構築、デプロイ、共有できる、フル マネージドのクラウド サービスです。 実験やモデルは、Azure Machine Learning Studio (プレビュー) で作成できます。 Azure Machine Learning Studio にアクセスするには、Data Science Virtual Machine 上の Web ブラウザーから [Microsoft Azure Machine Learning](https://ml.azure.com) にアクセスします。

Azure Machine Learning Studio にサインインしたら、実験キャンバスを使用して機械学習アルゴリズムの論理フローを作成することができます。 また、Azure Machine Learning でホストされている Jupyter Notebook にアクセスし、Azure Machine Learning Studio 内の実験をシームレスに操作することもできます。 

作成した機械学習モデルを Web サービス インターフェイスにラップすることで、モデルを運用可能な状態にすることができます。 機械学習モデルを運用可能な状態にすると、任意の言語で記述されたクライアントで、機械学習モデルから予測を呼び出すことができます。 詳細については、[Machine Learning のドキュメント](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

VM で R または Python を使ってモデルを作成し、Azure Machine Learning の運用環境にデプロイすることもできます。 この機能を有効にするために、R (**AzureML**) と Python (**azureml**) にライブラリがインストールされています。

> [!NOTE]
> これらの手順は、Windows バージョンの Data Science Virtual Machine 向けに書かれたものです。 ただし、これらに記載されている Azure Machine Learning へのモデルのデプロイに関する情報は Linux VM にも適用されます。

## <a name="machine-learning-tools"></a>機械学習ツール

VM には、プリコンパイルされてローカルにプレインストールされた機械学習ツールとアルゴリズムが備わっています。 これには以下が含まれます。

* **Vowpal Wabbit**: 高速オンライン学習アルゴリズム。
* **xgboost**: 最適化されたブースト ツリー アルゴリズムを提供するツール。
* **Rattle**: データ探索とモデリングに役立つ R ベースのグラフィカル ツール。
* **Python**: Anaconda Python には、Scikit-learn などのライブラリと機械学習アルゴリズムがバンドルされています。 その他のライブラリは、 `pip install` を使用してインストールできます。
* **LightGBM**: デシジョン ツリー アルゴリズムに基づく、高速で分散型の高パフォーマンスな勾配ブースティング フレームワーク。
* **R**:R 向けに、機械学習関数の豊富なライブラリが用意されています。プレインストールされているライブラリには、lm、glm、randomForest、rpart などがあります。 その他のライブラリは、次のコマンドを使用してインストールできます。

    ```r
    install.packages(<lib name>)
    ```

上記の最初の 3 つの機械学習ツールに関する追加情報を次に示します。

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit は、オンライン、ハッシュ、allreduce、リダクション、learning2search、アクティブ、対話型学習などの手法を使用する機械学習システムです。

基本的な例でこのツールを実行するには、次のコマンドを使用します。

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

このディレクトリには、ほかにも多数のデモが用意されています。 Vowpal Wabbit の詳細については、[GitHub のこちらのセクション](https://github.com/JohnLangford/vowpal_wabbit)と [Vowpal Wabbit の Wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) を参照してください。

### <a name="xgboost"></a>xgboost

xgboost は、ブースト (ツリー) アルゴリズム向けに設計および最適化されたライブラリです。 このライブラリの目的は、コンピューターの計算能力の限界を超えて、スケーラブルで移植できる正確で大規模なツリー ブーストを提供することです。

コマンド ラインおよび R ライブラリとして提供されています。 R でこのライブラリを使用するには、(シェルで「**R**」と入力して) 対話型の R セッションを開始し、ライブラリを読み込みます。

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

xgboost コマンド ラインを実行するには、シェルで次のコマンドを実行します。

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

.model ファイルは、指定したディレクトリに書き込まれます。 このデモの例については、[GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification) でご覧いただけます。

xgboost の詳細については、[xgboost ドキュメント ページ](https://xgboost.readthedocs.org/en/latest/)および xgboost の [GitHub リポジトリ](https://github.com/dmlc/xgboost)を参照してください。

### <a name="rattle"></a>Rattle

Rattle (**R** **A** nalytical **T** ool **T** o **L** earn **E** asily) では、GUI ベースのデータ探索とモデリングを使用します。 Rattle では、データの統計の概要を視覚的に表示し、簡単にモデル化できるようにデータを変換します。データから教師なしと教師ありの両方のモデルを作成し、モデルのパフォーマンスをグラフィカルに表示して、新しいデータセットをスコア付けします。 また、UI での操作をレプリケートする R コードを生成することもできます。生成したコードは、R で直接実行することや、詳細な分析の開始点として使用することができます。

Rattle を実行するには、グラフィカル デスクトップのサインイン セッションに入っている必要があります。 ターミナルで「**R**」と入力して、R 環境を開きます。 R プロンプトで、次のコマンドを入力します。

```R
library(rattle)
rattle()
```

これで、グラフィカル インターフェイスが開き、一連のタブが表示されます。 次のクイックスタート手順に従い、Rattle でサンプルの天候データ セットを使用してモデルを構築します。 一部の手順では、必要な R パッケージのうち、システム上にないものを自動的にインストールして読み込むことを確認するメッセージが表示されます。

> [!NOTE]
> システム ディレクトリ (既定) にパッケージをインストールするためのアクセス許可がない場合、R コンソール ウィンドウに、個人用ライブラリにパッケージをインストールするように求めるメッセージが表示されます。 そのメッセージが表示された場合は、 **y** で回答します。

1. **[実行]** を選択します。
1. サンプルの天候データ セットを使用するかどうかをたずねるダイアログ ボックスが表示されます。 **[Yes]\(はい\)** を選択して、サンプルを読み込みます。
1. **[Model]\(モデル\)** タブを選択します。
1. **[Execute]\(実行\)** を選択して、デシジョン ツリーを作成します。
1. **[Draw]\(表示\)** を選択して、デシジョン ツリーを表示します。
1. **[Forest]\(フォレスト\)** オプションをオンにし、 **[Execute]\(実行\)** を選択してランダム フォレストを作成します。
1. **[Evaluate]\(評価\)** タブを選択します。
1. **[Risk]\(リスク\)** オプションをオンにし、 **[Execute]\(実行\)** を選択して、2 つの **リスク (累積)** パフォーマンス プロットを表示します。
1. **[Log]\(ログ\)** タブを選択して、これまでの操作の生成済み R コードを表示します
   (Rattle の現在のリリースにはバグがあるため、ログのテキストの **Export this log** の前に **#** 文字を挿入する必要があります)。
1. **[Export]\(エクスポート\)** ボタンを選択して、R スクリプト ファイルを *weather_script.R* という名前でホーム フォルダーに保存します。

Rattle と R を終了できます。これで、生成された R スクリプトに変更を加えることができます。 または、スクリプトをそのまま使用することもできます。スクリプトをいつでも実行して、Rattle UI 内で実行されたすべての操作を繰り返すことができます。 これは、特に R の初心者向けの方法です。この方法では、シンプルなグラフィカル インターフェイスで分析と機械学習をすばやく実行することができ、変更または学習する R のコードを自動的に生成できます。

## <a name="next-steps"></a>次のステップ

その他の質問がある場合は、 [サポート チケット](https://azure.microsoft.com/support/create-ticket/)の作成を検討してください。