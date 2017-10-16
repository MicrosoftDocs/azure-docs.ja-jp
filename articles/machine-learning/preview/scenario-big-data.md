---
title: "数テラバイトのデータを活用したサーバー ワークロードの予測 - Azure | Microsoft Docs"
description: "Azure ML Workbench を使用してビッグ データに対して機械学習モデルをトレーニングする方法について説明します。"
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b76253fad43be231591023c4d4466bf6e3f329a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="server-workload-forecasting-on-terabytes-data"></a>数テラバイトのデータを活用したサーバー ワークロードの予測

この例では、データ サイエンティストが Azure ML Workbench を使用して、ビッグ データの使用が必要なソリューションを開発する方法を示します。 ここでは、Azure ML Workbench を使用するユーザーが、大規模なデータセットのサンプルから始めて、データ準備、特徴エンジニアリング、および機械学習を繰り返し、最終的に大規模なデータセット全体にプロセスを拡張するという適切な手順を実行できるように説明します。 

その過程で、次に示す Azure ML Workbench の主な機能について説明します。
* コンピューティング ターゲット間の簡単な切り替え: ユーザーが複数のコンピューティング ターゲットを設定し、実験で使用する方法について説明します。 この例では、Ubuntu DSVM と HDInsight クラスターをコンピューティング ターゲットとして使用します。 また、リソースの可用性に応じて、コンピューティング ターゲットを構成する方法についても説明します。 具体的には、Spark クラスターをスケール アウトした後 (つまり、Spark クラスターにより多くの worker ノードを含めた後) に、ユーザーが Azure ML Workbench でリソースを使用して、実験の実行速度を短縮する方法について説明します。
* 実行履歴の追跡: Azure ML Workbench を使用して、ML モデルのパフォーマンスや他の関連実行のメトリックを追跡する方法について説明します。
* 機械学習モデルの運用化: Azure ML Workbench 内で組み込みのツールを使用して、トレーニングされた ML モデルを Azure Container Service (ACS) の Web サービスとしてデプロイする方法について説明します。 また、Web サービスを使用して、REST API の呼び出しで少量のバッチ予測を取得する方法についても説明します。 
* テラバイト単位のサポート。



## <a name="link-to-the-gallery-github-repository"></a>ギャラリーの GitHub リポジトリへのリンク

この例のパブリック GitHub リポジトリには、コード サンプルなど、すべての素材が含まれています。 
 
[https://github.com/Azure/MachineLearningSamples-BigData](https://github.com/Azure/MachineLearningSamples-BigData)



## <a name="use-case-overview"></a>ユース ケースの概要


サーバー上のワークロードの予測は、自社のインフラストラクチャを管理するテクノロジ企業に共通するビジネス ニーズです。 インフラストラクチャのコストを減らすには、あまり使用されていないサーバーで実行されているサービスは、少数のコンピューターで実行するようにまとめ、高負荷のサーバーで実行されているサービスには、より多くのコンピューターを割り当てます。 このシナリオでは、各コンピューター (またはサーバー) のワークロード予測を中心に説明します。 具体的には、各サーバー上のセッション データを使用して、今後のサーバーのワークロード クラスを予測します。 [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html) の Random Forest Classifier を使用して、各サーバーの負荷を低、中、高クラスに分類します。 この例の機械学習手法とワークフローは、他の同様の問題に簡単に拡張できます。 


## <a name="prerequisites"></a>前提条件

この例を実行するための前提条件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/free/) (無料試用版もご利用いただけます)。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) のインストール済みコピー。[クイックスタート インストール ガイド](./quickstart-installation.md)に従ってプログラムをインストールし、ワークスペースを作成します。
* このシナリオでは、Windows 10 上で Azure Machine Learning (ML) Workbench を実行していることを前提としています。 macOS を使用している場合も、手順の多くは同じです。
* Linux (Ubuntu) 用データ サイエンス仮想マシン (DSVM)。 [こちらの手順](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm)に従って、Ubuntu DSVM をプロビジョニングすることができます。 クイック スタートについては、[こちら](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)をクリックしてください。 少なくとも 8 個のコアと 32 GB のメモリを搭載した仮想マシンを使用することをお勧めします。  この例を試すには、DSVM IP アドレス、ユーザー名、およびパスワードが必要です。 後の手順で参照できるように、次の表に DSVM の情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
DSVM の IP アドレス | xxx|
 ユーザー名  | xxx|
 パスワード   | xxx|

 [Docker エンジン](https://docs.docker.com/engine/)をインストールした仮想マシン (VM) を使用することができます。

* HDP バージョン 3.6 および Spark バージョン 2.1.x がインストールされた HDInsight Spark クラスター。 HDInsight クラスターの詳細な作成方法については、「[Azure HDInsight での Apache Spark クラスターの作成] (https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)」を参照してください。 各 worker に 16 コアと 112 GB のメモリを割り当てた 3 worker クラスターを使用することをお勧めします。 または、ヘッド ノードに VM の種類 "`D12 V2`"、worker ノードに "`D14 V2`" を選択することもできます。 クラスターのデプロイには、約 20 分かかります。 この例を試すには、クラスター名、SSH ユーザー名、およびパスワードが必要です。 後の手順で参照できるように、次の表に Azure HDInsight クラスターの情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
 クラスター名| xxx|
 ユーザー名  | xxx (既定値は sshuser です)|
 パスワード   | xxx|


* Azure Storage のアカウント [こちらの手順](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)に従って、Azure ストレージ アカウントを作成することができます。 また、このストレージ アカウントで "`fullmodel`" および "`onemonthmodel`" という名前で 2 つのプライベート BLOB コンテナーを作成します。 ストレージ アカウントは、中間の計算結果と機械学習モデルを保存するために使用されます。 この例を試すには、ストレージ アカウント名とアクセス キーが必要です。 後の手順で参照できるように、次の表に Azure ストレージ アカウントの情報を記入して保存します。

 フィールド名| 値 |  
 |------------|------|
 ストレージ アカウント名| xxx|
 アクセス キー  | xxx|


前提条件一覧で作成した Ubuntu DSVM および Azure HDInsight クラスターは、コンピューティング ターゲットです。 コンピューティング ターゲットとは、Azure ML Workbench のコンテキストのコンピューティング リソースです。このリソースは、Azure ML Workbench が実行されているコンピューターとは異なる可能性があります。   

## <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに、「Workload Forecasting on Terabytes Data」と入力し、テンプレートを選択します
5.  **[作成]**

こちらの[手順](./tutorial-classifying-iris-part-1.md)に従って事前に作成した Git リポジトリを使用して、Azure ML Workbench プロジェクトを作成できます。  
git status を実行して、バージョン追跡のファイルの状態を検査します。

## <a name="data-description"></a>データの説明

このシナリオで使用されるデータは合成されたサーバー ワークロード データであり、パブリックでアクセス可能な Azure BLOB ストレージ アカウントでホストされます。 具体的なストレージ アカウントの情報は、[`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) の `dataFile` フィールドで確認できます。 Azure BLOB ストレージから直接データを使用できます。 多数のユーザーが同時にストレージを使用する場合は、[azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) を使用してデータを自分のストレージにダウンロードすることができます。 

合計データ サイズは約 1 TB です。 各ファイルは約 1 ～ 3 GB であり、ヘッダーなしの CSV ファイル形式です。 データの各行は、特定のサーバーで発生したトランザクションの負荷を示しています。  データ スキーマの詳細情報は次のとおりです。

列番号 | フィールド名| 型 | Description |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    セッションの開始時間
2  |`SessionEnd`    | DateTime | セッションの終了時間
3 |`ConcurrentConnectionCounts` | 整数 | 同時接続数
4 | `MbytesTransferred` | Double | メガバイト単位で転送される正規化されたデータ
5 | `ServiceGrade` | 整数 |  セッションのサービス グレード
6 | `HTTP1` | 整数|  セッションが HTTP1 と HTTP2 のどちらを使用するか
7 |`ServerType` | 整数   |サーバーの種類
8 |`SubService_1_Load` | Double |   サブサービス 1 の負荷
9 | `SubService_1_Load` | Double |  サブサービス 2 の負荷
10 | `SubService_1_Load` | Double |     サブサービス 3 の負荷
11 |`SubService_1_Load` | Double |  サブサービス 4 の負荷
12 | `SubService_1_Load`| Double |      サブサービス 5 の負荷
13 |`SecureBytes_Load`  | Double | セキュリティで保護されたバイトの読み込み
14 |`TotalLoad` | Double | サーバー上の合計読み込み
15 |`ClientIP` | String|    クライアント IP アドレス
16 |`ServerIP` | String|    サーバーの IP アドレス



上の表には予想されるデータ型が記載されていますが、不足している値やダーティデータの問題があるため、予想どおりのデータ型になる保証はありません。データの処理では、この点を考慮に入れることをお勧めします。 


## <a name="scenario-structure"></a>シナリオの構造

この例のファイルは、次のように整理されます。

| ファイル名 | 型 | Description |
|-----------|------|-------------|
| `Code` | フォルダー | このフォルダーには、この例のすべてのコードが含まれています |
| `Config` | フォルダー | このフォルダーには、構成ファイルが含まれています |
| `Image` | フォルダー | README ファイルの画像を保存するためのフォルダー |
| `Model` | フォルダー | Azure BLOB ストレージからダウンロードしたモデル ファイルを保存するためのフォルダー |
| `Code/etl.py` | Python ファイル | データの準備と特徴エンジニアリングに使用される Python ファイル |
| `Code/train.py` | Python ファイル | 3 クラスの多分類モデルのトレーニングに使用される Python ファイル  |
| `Code/webservice.py` | Python ファイル | 運用化に使用される Python ファイル  |
| `Code/scoring_webservice.py` | Python ファイル |  データ変換と Web サービスの呼び出しに使用される Python ファイル |
| `Code/O16Npreprocessing.py` | Python ファイル | scoring_webservice.py のデータの前処理に使用される Python ファイル。  |
| `Code/util.py` | Python ファイル | Azure BLOB の読み取りと書き込みのコードを含む Python ファイル。  
| `Config/storageconfig.json` | JSON ファイル | 1 か月のデータの処理とトレーニングを行うために、中間の結果とモデルを格納する Azure BLOB コンテナーの構成ファイル |
| `Config/fulldata_storageconfig.json` | JSON ファイル |  完全なデータセットの処理とトレーニングを行うために、中間の結果とモデルを格納する Azure BLOB コンテナーの構成ファイル|
| `Config/webservice.json` | JSON ファイル | scoring_webservice.py の構成ファイル|
| `Config/conda_dependencies.yml` | YAML ファイル | Conda の依存関係ファイル |
| `Config/conda_dependencies_webservice.yml` | YAML ファイル | Web サービス用の Conda の依存関係ファイル|
| `Config/dsvm_spark_dependencies.yml` | YAML ファイル | Ubuntu DSVM 用の Spark の依存関係ファイル |
| `Config/hdi_spark_dependencies.yml` | YAML ファイル | HDInsight Spark クラスター用の Spark の依存関係ファイル |
| `README.md` | マークダウン ファイル | README マークダウン ファイル |
| `Code/download_model.py` | Python ファイル | Azure BLOB のモデル ファイルをローカル ディスクにダウンロードするために使用される Python ファイル |
| `Docs/DownloadModelsFromBlob.md` | マークダウン ファイル | `Code/download_model.py` の実行手順を含むマークダウン ファイル |



### <a name="data-flow"></a>Data flow

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) のコードは、パブリックでアクセス可能なコンテナー ([`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) の `dataFile` フィールド) からデータを読み込みます。 データの準備と特徴エンジニアリングが含まれ、中間の計算結果とモデルが自分のプライベート コンテナーに保存されます。 [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) のコードは、プライベート コンテナーから中間の計算結果を読み込み、多クラス分類モデルをトレーニングし、最後にトレーニングされた機械学習モデルをプライベート コンテナーに書き込みます。 1 か月のデータセットで実験用の 1 つのコンテナーを使用してから、完全なデータセットでの実験用に別のコンテナーを使用することをお勧めします。 データとモデルは Parquet ファイルとして保存されるので、各ファイルは実際には、複数の BLOB を含むコンテナー内の 1 つのフォルダーです。 結果のコンテナーは次のようになります。

| BLOB のプレフィックス名 | 型 | Description |
|-----------|------|-------------|
| featureScaleModel | Parquet | 数値特徴の標準スケーラー モデル |
| stringIndexModel | Parquet | 数値特徴以外の文字列インデクサー モデル|
| oneHotEncoderModel|Parquet | カテゴリの特徴のワンホット エンコーダー モデル |
| mlModel | Parquet | トレーニングされた機械学習モデル |
| info| Python pickle ファイル | 変換されたデータに関する情報。たとえば、トレーニングの開始、トレーニングの終了、期間、トレーニングとテストの分割のタイムスタンプ、インデックス作成とワンホット エンコーディングの列などです。

前の表のすべてのファイルと BLOB は、運用化に使用されます。


### <a name="model-development"></a>モデルの開発

#### <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム


次の図は、Azure ML Workbench を使用してモデルを開発するエンドツーエンドのワークフローを示しています。![アーキテクチャ](media/scenario-big-data/architecture.PNG)



次に、Azure ML Workbench でリモート コンピューティング ターゲット機能を使用したモデル開発について説明します。 短時間で繰り返し実行できるように、まず Ubuntu DSVM に小規模なサンプル データを読み込み、スクリプト [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) を実行します。 さらに短時間で繰り返し実行できるように追加の引数を渡して、[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) で実行する作業を制限することができます。 最後に、HDInsight クラスターを使用して、完全なデータでトレーニングします。     

[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) ファイルは、データ、データの準備、および特徴エンジニアリングの読み込みを実行します。 このファイルは 2 つの引数を受け取ります。(1) 中間の計算結果とモデルを格納する Azure BLOB ストレージ コンテナー用の構成ファイルと、(2) 短時間で繰り返し実行するためのデバッグ構成です。

最初の引数である `configFilename` は、Azure BLOB ストレージ情報を格納し、データの読み込み場所を指定するローカル構成ファイルです。 既定では [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) であり、1 か月のデータ実行で使用されます。 また、[`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) を含めています。これは、完全なデータセットの実行で使用する必要があります。 構成の内容は次のとおりです。 

| フィールド | 型 | Description |
|-----------|------|-------------|
| storageAccount | String | Azure ストレージ アカウント名 |
| storageContainer | String | 中間の結果を格納する Azure ストレージ アカウントのコンテナー |
| storageKey | String |Azure ストレージ アカウントのアクセス キー |
| dataFile|String | データ ソース ファイル  |
| duration| String | データ ソース ファイルのデータの期間|

`Config/storageconfig.json` と `Config/fulldata_storageconfig.json` の両方を変更して、ストレージ アカウント、ストレージ キー、および BLOB コンテナーを構成して中間の結果を格納します。 既定で、1 か月のデータ実行の BLOB コンテナーは "`onemonthmodel`" で、完全なデータセット実行の BLOB コンテナーは "`fullmodel`" です。ストレージ アカウントにこれら 2 つのコンテナーを作成するようにします。 [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) の `"dataFile"` フィールドで、[`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) に読み込まれるデータの内容を構成し、`"duration"` で、データに含める範囲を構成します。 duration を 'ONE_MONTH' に設定すると、読み込まれるデータベースは、2016 年 6 月のデータの 7 個あるファイルのうち 1 つの csv ファイルのみです。 duration が 'FULL' の場合、完全なデータセット (1 TB) が読み込まれます。 これら 2 つの構成ファイルに含まれる `"dataFile"` と `"duration"` を変更する必要はありません。

2 つ目の引数は DEBUG です。 'FILTER_IP' に設定すると、短時間で繰り返すことができます。 このパラメーターは、スクリプトをデバッグするときに使用すると便利です。

> [!NOTE]
> 次のすべてのコマンドで引数の変数を実際の値に置き換えてください。
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Ubuntu DSVM 上の Docker でのモデル開発

#####  <a name="1-setting-up-the-compute-target-for-docker-on-ubuntu-dsvm"></a>1.Ubuntu DSVM 上で Docker のコンピューティング ターゲットを設定する

Azure ML Workbench の左上にある [ファイル] メニューをクリックし、[コマンド プロンプトを開く] を選択して Azure ML Workbench からコマンドラインを開始し、以下を実行します。 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

コマンドラインの実行が正常に完了すると、作成された次の 2 つのファイルがプロジェクトの aml_config フォルダーに表示されます。

    dockerdsvm.compute: contains the connection and configuration information for a remote execution target
    dockerdsvm.runconfig: set of run options used when executing within the Azure ML Workbench application

dockerdsvm.runconfig に移動し、次のフィールドの構成を以下のように変更します。

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

以下を実行してプロジェクト環境を準備します。

```az ml experiment prepare -c dockerdsvm```


"PrepareEnvironment" を true に設定して、Azure ML Workbench でジョブを送信するたびにランタイム環境を作成することができます。 `Config/conda_dependencies.yml` と `Config/dsvm_spark_dependencies.yml` には、ランタイム環境のカスタマイズが含まれています。 これら 2 つの YMAL ファイルを編集して、いつでも Conda の依存関係、Spark の構成、および Spark の依存関係を変更できます。 この例では、追加の Python パッケージとして `azure-storage` と `azure-ml-api-sdk` を `Config/conda_dependencies.yml` に追加し、"`spark.default.parallelism`"、"`spark.executor.instances`"、"`spark.executor.cores`" などを `Config/dsvm_spark_dependencies.yml` に追加しました。 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2.DSVM Docker 上のデータの準備と特徴エンジニアリング

DSVM Docker 上で、サーバーの IP アドレスを指定して読み込まれるデータをフィルターするデバッグ パラメーターを使用してスクリプト `etl.py` を実行します。

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

サイド パネルに移動し、[実行] をクリックすると、`etl.py` の実行履歴が表示されます。 実行時間は約 2 分です。 新機能を含めるようにコードを変更する予定の場合は、2 つ目の引数として FILTER_IP を指定することで、短時間で繰り返し実行できるようになります。 独自の機械学習の問題を処理してデータセットを調査する場合や、新機能を作成する場合、この手順を複数回実行することがあります。 読み込むデータの制限をカスタマイズし、処理するデータの内容をさらにフィルターすることで、モデル開発の繰り返し処理を高速化することができます。 実験するときは、定期的にコードの変更を Git リポジトリに保存することをお勧めします。  `etl.py` では次のコードを使用し、プライベート コンテナーへのアクセスを有効にしました。

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


次に、デバッグ パラメーター FILTER_IP を使用せずに DSVM Docker でスクリプト `etl.py` を実行します

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

サイド パネルに移動し、[実行] をクリックすると、`etl.py` の実行履歴が表示されます。 実行時間は約 4 分です。 この手順の処理結果はコンテナーに保存され、トレーニングのために train.py に読み込まれます。 また、文字列インデクサー、エンコーダー パイプライン、および標準スケーラーは、プライベート コンテナーにも保存され、運用化 (O16N) で使用されます。 


##### <a name="3-model-training-on-dsvm-docker"></a>3.DSVM Docker でのモデルのトレーニング

DSVM Docker 上でスクリプト `train.py` を実行します。

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

この手順で、`etl.py` の実行から中間の計算結果を読み込み、機械学習モデルをトレーニングします。 この手順の実行時間は約 2 分です。

小さなデータに対して実験を正常に完了したら、次に完全なデータセットに対して実験を実行します。 まず同じコードを使用して実行してから、引数とコンピューティング ターゲットを変更して実験することができます。  

####  <a name="model-development-on-the-hdinsight-cluster"></a>HDInsight クラスター上でのモデル開発

##### <a name="1-create-compute-target-in-azure-ml-workbench-for-the-hdinsight-cluster"></a>1.HDInsight クラスター用に Azure ML Workbench にコンピューティング ターゲットを作成する

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

コマンドラインが正常に完了すると、作成された次の 2 つのファイルが aml_config フォルダーに表示されます。
    
    myhdo.compute: contains connection and configuration information for a remote execution target
    myhdi.runconfig: set of run options used when executing within the Azure ML Workbench application


myhdi.runconfig に移動し、次のフィールドの構成を以下のように変更します。

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

以下を実行してプロジェクト環境を準備します。

```az ml experiment prepare -c myhdi```

この手順の実行時間は最長 7 分です。

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2.HDInsight クラスター上のデータの準備と特徴エンジニアリング

HDInsight クラスター上で完全なデータを使用してスクリプト `etl.py` を実行します

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

このジョブの実行時間は比較的長いので (約 2 時間)、"-a" を使用して出力のストリーミングを無効にすることができます。 ジョブが完了すると、[実行履歴] でドライバー ログとコントローラー ログを確認できます。 大きなクラスターがある場合は、いつでもより多くのインスタンスまたはコアを使用するように `Config/hdi_spark_dependencies.yml` の構成を変更することができます。 たとえば、4 worker ノードのクラスターがある場合は "`spark.executor.instances`" の値を 5 から 7 に増やすことができます。 ストレージ アカウントの "fullmodel" コンテナーでこの手順の出力を確認できます。 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3.HDInsight クラスター上のモデルのトレーニング

HDInsight クラスター上でスクリプト `train.py` を実行します。

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

このジョブの実行時間は比較的長いので (約 30 分間)、ここでは "-a" を使用して出力のストリーミングを無効にします。

#### <a name="run-history-exploration"></a>実行履歴の探索

実行履歴は、Azure ML Workbench での実験を追跡することができる機能です。 既定では、実験の期間が追跡されます。 この例では、実験で "`Code/etl.py`" の完全なデータセットに移行すると、期間が大幅に増えることがわかります。 追跡のために、特定のメトリックをログに記録することもできます。 メトリックの追跡を有効にするには、Python ファイルのヘッダーに次のコード行を追加します。
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
以下は特定のメトリックを追跡する例です。

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Azure ML Workbench の右側のサイドバーにある [実行] に移動すると、各 Python ファイルの実行履歴が表示されます。 さらに、GitHub リポジトリにアクセスすると、"AMLHistory" から始まる名前の新しいブランチが作成され、各実行でスクリプトに加えられた変更を追跡することができます。 


### <a name="operationalization"></a>運用化

ここでは、前の手順で Web サービスとして作成したモデルを運用化し、Web サービスを使用してワークロードを予測する方法をデモします。 Azure ML Operationalization Command-Line Interface (CLI) を使用して、コードと依存関係を Docker イメージとしてパッケージ化し、コンテナー化された Web サービスとしてモデルを公開します。 詳細については、「[Operationalization Overview](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md)」(運用化の概要) を参照してください。 Azure ML Workbench のコマンドライン プロンプトを使用して、Azure ML Operationalization CLI を実行することができます。  また、[インストール ガイド](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md)に従って、Ubuntu Linux 上で Azure ML Operationalization CLI を実行することもできます。 

> [!NOTE]
> 次のすべてのコマンドで引数の変数を実際の値に置き換えてください。 このセクションを完了するには、約 40 分かかります。
> 


運用化の環境として一意の文字列を選択します。ここでは、選択した文字列を表すために "[unique]" という文字列を使用します。

1. 運用化の環境を作成し、リソース グループを作成します。

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   ここでは、`az ml env setup` コマンドで `--cluster` を使用して、環境として Azure Container Service を選択します。 また、コンテナー化されたアプリケーションのデプロイ、スケーリング、および管理を自動化するために [Kubernetes](https://kubernetes.io/) を使用するので、[Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes) 上で機械学習モデルを運用化します。このコマンドの実行には約 20 分かかります。 用途 

        az ml env show -g [unique]rg -n [unique]

   このコマンドで、デプロイが正常に完了したかどうかを確認できます。

   次のコマンドを実行して、作成した環境としてデプロイ環境を設定します。

        az ml env set -g [unique]rg -n [unique]

2. モデル管理アカウントを作成し、モデル管理アカウントを使用します。

   次のコマンドを実行して、モデル管理アカウントを作成します。

    az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   次のコマンドを実行して、運用化にモデル管理を使用します。

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   モデル管理アカウントは、モデルと Web サービスの管理に使用されます。 Azure Portal から、新しいモデル管理アカウントが作成されたことを確認し、そのモデル管理アカウントを使用して作成されたモデル、マニフェスト、Docker イメージ、およびサービスを確認できます。

3. モデルをダウンロードして登録します。

   "fullmodel" コンテナー内のモデルを、ローカル コンピューターのコードのディレクトリにダウンロードします。 "vmlSource.parquet" という名前の parquet データ ファイルはダウンロードしないでください。これはモデル ファイルではなく、中間の計算結果です。 また、Git リポジトリに保存したモデル ファイルを再利用することもできます。 parquet ファイルのダウンロードの詳細については、[DownloadModelsFromBlob.md](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) を参照してください。 

   CLI で `Model` フォルダーに移動し、次のようにモデルを登録します。

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   各コマンドから、次の手順で必要になるモデル ID が出力されます。 後で使用するためにテキスト ファイルに保存してください。

4. Web サービスのマニフェストを作成します。

   マニフェストは、Web サービス コンテナーの Docker イメージの作成に使用されるレシピです。 Web サービス、すべての機械学習モデル、およびランタイム環境の依存関係のコードが含まれています。  CLI で `Code` フォルダーに移動し、次のコマンド ラインを実行します。

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   次の手順で使用されるマニフェスト ID が出力されます。 

   `Code` ディレクトリのままで、次のコマンドを実行して webservice.py をテストできます。 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Docker イメージを作成します。 

        az ml image create -n [unique]image --manifest-id $manifestID

   Docker イメージが ACS で使用されるので、次の手順で使用されるイメージ ID が出力されます。 

6. Web サービスを ACS クラスターにデプロイします

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   サービス ID が出力されます。この ID は、承認キーとサービス URL を取得するために使用する必要があります。

7. Python コードで Web サービスを呼び出して、少量のバッチで評価します。

   次のコマンドを使用して、承認キーを取得します。

         az ml service keys realtime -i $ServiceID 

   また、次のコマンドを使用して、サービス スコア付け URL を取得します。

        az ml service usage realtime -i $ServiceID

   適切なサービス評価 URL と承認キーを使用して `./Config/webservice.json` のコンテンツを変更します (元のファイルの "Bearer" をそのままにして、"xxx" の部分を置き換えます)。 
   
   プロジェクトのルート ディレクトリに移動し、次のコマンドを使用して少量のバッチ スコア付けの Web サービスをテストします。

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Web サービスをスケーリングします。 

   Web サービスのスケーリングについては、「[How to scale operationalization on your ACS cluster](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md)」(ACS クラスターで運用化をスケーリングする方法) を参照してください。
 

## <a name="conclusion"></a>まとめ

この例では、Azure ML Workbench を使用してビッグ データに対して機械学習モデルをトレーニングし、トレーニングしたモデルを運用化する方法を扱っています。 具体的には、次の方法について説明しました。

* さまざまなコンピューティング ターゲットを構成して使用する。

* メトリックと複数の実行を追跡する実行履歴。

* 運用化。

このコードを拡張して、クロス検証とハイパーパラメーター調整を探索することができます。 クロス検証とハイパーパラメーター調整の詳細については、https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning を参照してください。  
時系列の予測の詳細については、https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting を参照してください。
