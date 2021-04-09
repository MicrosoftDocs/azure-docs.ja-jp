---
title: アーキテクチャと主要な概念
titleSuffix: Azure Machine Learning
description: この記事では、Azure Machine Learning を構成するアーキテクチャ、用語、および概念の概要について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: dc1954c97da0d7f40deaf0f4efa7ca99793107bb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503693"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning のしくみ:アーキテクチャと概念

[Azure Machine Learning](overview-what-is-azure-ml.md) のアーキテクチャおよび概念について説明します。  この記事では、コンポーネントについて概説し、これらが連携することにより、どのように機械学習モデルの構築、デプロイ、保守のプロセスに役立つかについて説明します。

## <a name="workspace"></a><a name="workspace"></a> ワークスペース

[機械学習ワークスペース](concept-workspace.md)は、Azure Machine Learning の最上位レベルのリソースです。

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="図:ワークスペースの Azure Machine Learning アーキテクチャとそのコンポーネント":::

ワークスペースは、次のことを行う一元的な場所です。

* [コンピューティング](#compute-instance)など、モデルのトレーニングとデプロイに使用するリソースを管理します
* Azure Machine Learning を使用するときに作成する、次のようなアセットを格納します。
  * [環境](#environments)
  * [実験](#experiments)
  * [パイプライン](#ml-pipelines)
  * [データセット](#datasets-and-datastores)
  * [モデル](#models)
  * [エンドポイント](#endpoints)

ワークスペースには、ワークスペースによって使用される他の Azure リソースが含まれています。

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/):トレーニング中およびモデルのデプロイ時に使用する Docker コンテナーを登録します。 コストを最小限に抑えるために、ACR は、デプロイ イメージが作成される時にだけ作成されます。
+ [Microsoft Azure Storage アカウント](https://azure.microsoft.com/services/storage/):ワークスペースの既定のデータストアとして使用されます。  Azure Machine Learning コンピューティングインスタンスで使用される Jupyter ノートブックもここに保存されます。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/):モデルに関する監視情報を格納します。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):コンピューティング先で使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

他のユーザーとワークスペースを共有できます。

## <a name="computes"></a>コンピューティング

<a name="compute-targets"></a>[コンピューティング先](concept-compute-target.md)は、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりするために使用する、マシンまたはマシンのセットです。 コンピューティング先として、ローカル コンピューターまたはリモート コンピューティング リソースを使用できます。  コンピューティング先を使用することにより、ローカル コンピューターでトレーニングを開始し、その後トレーニング スクリプトを変更することなくクラウドにスケールアウトできます。

Azure Machine Learning には、機械学習タスク用に構成された以下の 2 つのフル マネージド クラウドベース仮想マシン (VM) が導入されています。

* <a name="compute-instance"></a> **コンピューティング インスタンス**:コンピューティング インスタンスは、機械学習用にインストールされた複数のツールと環境を含む VM です。 コンピューティング インスタンスの主な用途は、開発ワークステーションです。  セットアップを行うことなく、サンプル ノートブックの実行を開始できます。 コンピューティング インスタンスは、トレーニング ジョブと推論ジョブのコンピューティング先として使用できます。

* **コンピューティング クラスター**:コンピューティング クラスターは、マルチノード スケーリング機能を備えた VM のクラスターです。 コンピューティング クラスターは、大規模なジョブと運用環境のコンピューティング先に適しています。  クラスターは、ジョブが送信されるときに自動的にスケールアップされます。  トレーニング コンピューティング ターゲットとして、または開発/テスト デプロイのために使用します。

トレーニング コンピューティング ターゲットの詳細については、「[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)」を参照してください。  デプロイメント コンピューティング ターゲットの詳細については、「[デプロイメント ターゲット](concept-compute-target.md#deploy)」を参照してください。

## <a name="datasets-and-datastores"></a>データセットとデータストア

[**Azure Machine Learning Datasets**](concept-data.md#datasets) によって、データへのアクセスと操作がより容易になります。 データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るため、追加のストレージ コストは発生せず、データ ソースの整合性が損なわれることはありません。

詳細については、[Azure Machine Learning Datasets の作成と登録](how-to-create-register-datasets.md)に関するページを参照してください。  データセットのその他の使用例については、[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)を参照してください。

データセットは、ご使用の Azure ストレージ サービスに安全に接続するために[データストア](concept-data.md#datastores)を使用します。 データストアは、認証資格情報と元のデータソースの整合性を損なうことなく、接続情報を格納します。 これらには、ワークスペースに関連付けられているキー コンテナー内のサブスクリプション ID やトークン承認のような接続情報が格納されるため、スクリプトでハードコーディングすることなく、ストレージに安全にアクセスできます。

## <a name="environments"></a>環境

[ワークスペース](#workspace) > **環境**

[環境](concept-environments.md)とは、機械学習モデルのトレーニングやスコアリングが行われる環境をカプセル化したものです。 環境では、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、環境変数、およびソフトウェア設定を指定します。  

コード例については、[環境の使用方法](how-to-use-environments.md#manage-environments)に関するページの「環境の管理」セクションを参照してください。

## <a name="experiments"></a>実験

[ワークスペース](#workspace) > **実験**

実験は、指定されたスクリプトからの多くの実行のグループです。 実験は、常に 1 つのワークスペースに属します。 実行を送信するときは、実験名を指定します。 実行に関する情報は、その実験に格納されます。 実験を送信するときに名前が存在しない場合は、新しい実験が自動的に作成されます。
  
実験の使用例については、「[チュートリアル:最初のモデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)」を参照してください。

### <a name="runs"></a>実行

[ワークスペース](#workspace) > [実験](#experiments) > **実行**

実行とは、トレーニング スクリプトの 1 回の実行です。 実験には、通常、複数の実行が含まれます。

Azure Machine Learning では、すべての実行を記録して、実験に次の情報を保存します。

* 実行に関するメタデータ (タイムスタンプ、期間など)
* スクリプトによって記録されるメトリック
* 実験によって自動収集される、またはユーザーによって明示的にアップロードされる、出力ファイル
* 実行前の、スクリプトを含むディレクトリのスナップショット

モデルをトレーニングするためにスクリプトを送信するときに実行を生成します。 実行は、0 個以上の子実行を持つことができます。 たとえば、最上位レベルの実行は 2 つの子実行を持つ可能性があり、それぞれが独自の子実行を持つ場合があります。

### <a name="run-configurations"></a>実行構成

[ワークスペース](#workspace) > [実験](#experiments) > [実行](#runs) > **実行構成**

実行構成は、指定されたコンピューティング先でのスクリプトの実行方法を定義します。 構成を使用して、スクリプト、コンピューティング先、実行する Azure ML 環境、任意の分散ジョブ固有の構成、いくつかの追加のプロパティを指定します。 実行のための構成可能なオプションのセット全体の詳細については、[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) に関する記事を参照してください。

実行構成は、トレーニング スクリプトが含まれるディレクトリ内のファイルに保持できます。   またはメモリ内オブジェクトとして構築して実行の送信に使用できます。

実行構成の例については、[トレーニングの実行の構成](how-to-set-up-training-targets.md)に関する記事を参照してください。

### <a name="snapshots"></a>スナップショット

[ワークスペース](#workspace) > [実験](#experiments) > [実行](#runs) > **スナップショット**

実行を送信するときに、Azure Machine Learning によって、スクリプトが含まれているディレクトリが zip ファイルとして圧縮され、コンピューティング先に送られます。 その後、zip ファイルが抽出され、そこでスクリプトが実行されます。 Azure Machine Learning では、zip ファイルもスナップショットとして実行レコード内に格納されます。 ワークスペースにアクセスできるすべてのユーザーは、実行レコードを参照し、スナップショットをダウンできます。

### <a name="logging"></a>ログ記録

Azure Machine Learning では、標準的な実行メトリックが自動的にログに記録されます。 ただし、[Python SDK を使用して任意のメトリックをログに記録する](how-to-track-experiments.md)こともできます。

ログを表示する方法は複数あり、リアルタイムで実行状態を監視するか、完了後に結果を表示します。 詳細については、[ML 実行ログの監視と表示](how-to-monitor-view-training-logs.md)に関する記事を参照してください。


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 これは、スクリプト実行構成または ML パイプラインを使用して送信した実行で機能します。 SDK または Machine Learning CLI から送信された実行でも機能します。

詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

### <a name="training-workflow"></a>トレーニング ワークフロー

モデルをトレーニングするための実験を実行すると、次の手順が行われます。 それを図解したものが下のトレーニング ワークフロー図です。

* 前のセクションで保存されたコード スナップショットのスナップショット ID を指定して、Azure Machine Learning が呼び出されます。
* Azure Machine Learning では実行 ID (省略可能) と Machine Learning service トークンが作成され、これは後で Machine Learning コンピューティング/VM などのコンピューティング先によって、Machine Learning service と通信するために使用されます。
* トレーニング ジョブを実行するために、マネージド コンピューティング先 (Machine Learning コンピューティングなど) またはアンマネージド コンピューティング先 (VM など) のいずれかを選択できます。 両方のシナリオのデータ フローを次に示します。
   * VM/HDInsight。Microsoft サブスクリプションのキー コンテナー内の SSH 資格情報でアクセスされます。 Azure Machine Learning では、以下を行うコンピューティング先で管理コードを実行します。

   1. 環境を準備します (Docker は VM とローカル コンピューターのオプションです。 Docker コンテナーで実験を行う方法については、Machine Learning コンピューティングに関する以下の手順を参照してください)。
   1. コードをダウンロードします。
   1. 環境変数と構成を設定します。
   1. ユーザー スクリプト (前のセクションで説明したコード スナップショット) を実行します。

   * Machine Learning コンピューティング。ワークスペース マネージド ID を使用してアクセスされます。
Machine Learning コンピューティングはマネージド コンピューティング先である (つまり、Microsoft によって管理される) ため、Microsoft サブスクリプションで実行されます。

   1. 必要な場合は、リモートの Docker の構築が開始されます。
   1. 管理コードは、ユーザーの Azure Files 共有に書き込まれます。
   1. コンテナーは、初期コマンドを使用して開始されます。 つまり、前の手順で説明した管理コードです。

* 実行が完了すると、実行とメトリクスを照会できます。 以下のフロー図では、トレーニング コンピューティング先で、Cosmos DB データベースのストレージから Azure Machine Learning に実行メトリックが書き戻された場合に、この手順が行われます。 クライアントで、Azure Machine Learning を呼び出すことができます。 その後、Machine Learning によって Cosmos DB データベースからメトリックがプルされ、クライアントに戻されます。

[![トレーニング ワークフロー](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>モデル

簡単に言うと、モデルとは入力を受け取って出力を生成するひとまとまりのコードです。 機械学習モデルの作成には、アルゴリズムの選択、アルゴリズムへのデータの提供、および[ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)が含まれます。 トレーニングはトレーニング済みモデルを生成する反復的なプロセスであり、トレーニング プロセス中にモデルが学習した内容がカプセル化されています。

Azure Machine Learning の外部でトレーニングされたモデルを使用することもできます。 または、[実験](#experiments)の[実行](#runs)を Azure Machine Learning の[コンピューティング先](#compute-targets)に送信することにより、モデルをトレーニングすることもできます。 モデルを入手したら、ワークスペースに[モデルを登録](#register-model)します。

Azure Machine Learning は、フレームワークに依存しません。 モデルを作成するときは、Scikit-learn、XGBoost、PyTorch、TensorFlow、Chainer などの任意の人気のある機械学習フレームワークを使用できます。

Scikit-learn を使用したモデルのトレーニングの例については、「[チュートリアル: Azure Machine Learning でイメージ分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」を参照してください。


### <a name="model-registry"></a><a name="register-model"></a>モデル レジストリ

[ワークスペース](#workspace) > **モデル**

**モデル レジストリ** により、Azure Machine Learning ワークスペース内のすべてのモデルを追跡できます。

モデルは、名前とバージョンによって識別されます。 既存のモデルと同じ名前でモデルを登録するたびに、レジストリではそれが新しいバージョンと見なされます。 バージョンはインクリメントされ、新しいモデルは同じ名前で登録されます。

モデルを登録するときに、追加のメタデータ タグを指定し、モデルを検索するときにそのタグを使用することができます。

> [!TIP]
> 登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、Azure Machine Learning ワークスペースに単一モデルとしてそれらを登録することができます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

アクティブなデプロイで使用されている登録済みモデルを削除することはできません。

モデルの登録例については、[Azure Machine Learning での画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するページを参照してください。

## <a name="deployment"></a>デプロイ

[登録済みのモデル](#register-model)をサービス エンドポイントとしてデプロイします。 次のコンポーネントが必要です。

* **環境**。 この環境は、推論用モデルを実行するために必要な依存関係をカプセル化します。
* **スコアリング コード**。 このスクリプトは、要求を受け入れ、モデルを使用してその要求にスコアを付け、その結果を返します。
* **推論構成**。 推論構成では、サービスとしてのモデルを実行するために必要な環境、エントリ スクリプト、およびその他のコンポーネントを指定します。

これらのコンポーネントの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="endpoints"></a>エンドポイント

[ワークスペース](#workspace) > **エンドポイント**

エンドポイントは、クラウドでホストできる Web サービスまたは統合デバイス デプロイ用 IoT モジュールへのモデルのインスタンス化です。

#### <a name="web-service-endpoint"></a>Web サービス エンドポイント

Web サービスとしてモデルをデプロイする場合、エンドポイントを Azure Container Instances、Azure Kubernetes Service、または FPGA にデプロイできます。 モデル、スクリプト、および関連ファイルからサービスを作成します。 これらは、モデルの実行環境を含むベース コンテナー イメージに配置されます。 イメージには、Web サービスに送信されるスコアリング要求を受け取る、負荷分散された HTTP エンドポイントがあります。

Web サービスを監視するために Application Insights テレメトリまたはモデルのテレメトリを有効にできます。 テレメトリ データにアクセスできるのは自分だけです。  これは Application Insights とストレージ アカウント インスタンスに格納されます。 自動スケールを有効にしてある場合は、Azure でデプロイが自動的にスケーリングされます。

次の図は、Web サービス エンドポイントとしてデプロイされているモデルの推論ワークフローを示しています。

これらについて詳しく説明します。

* ユーザーは、Azure Machine Learning SDK などのクライアントを使用して、モデルを登録します。
* ユーザーは、モデル、スコア ファイル、およびその他のモデルの依存関係を使用して、イメージを作成します。
* Docker イメージが作成されて、Azure Container Registry で格納されます。
* Web サービスは、前の手順で作成されたイメージを使用して、コンピューティング先 (Container Instances/AKS) にデプロイされます。
* スコアリング要求の詳細は、ユーザーのサブスクリプション内の Application Insights に格納されます。
* テレメトリも Microsoft/Azure サブスクリプションにプッシュされます。

[![推論のワークフロー](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Web サービスとしてのモデルのデプロイ例については、[Azure Container Instances での画像分類モデルのデプロイ](tutorial-deploy-models-with-aml.md)に関するページを参照してください。

#### <a name="real-time-endpoints"></a>リアルタイム エンドポイント

トレーニングされたモデルをデザイナーに配置すると、[モデルをリアルタイム エンドポイントとして配置](tutorial-designer-automobile-price-deploy.md)できます。 リアルタイム エンドポイントは、通常、REST エンドポイントを介して単一の要求を受信し、リアルタイムで予測を返します。 これは、一度に複数の値を処理し、完了後に結果をデータストアに保存するというバッチ処理とは異なります。

#### <a name="pipeline-endpoints"></a>パイプライン エンドポイント

パイプライン エンドポイントを使用すると、REST エンドポイントを使用して、プログラムで [ML パイプライン](#ml-pipelines)を呼び出すことができます。 パイプライン エンドポイントによって、パイプライン ワークフローを自動化できます。

パイプライン エンドポイントは、発行されたパイプラインのコレクションです。 この論理的な組織では、同じエンドポイントを使用して複数のパイプラインを管理し、呼び出すことができます。 パイプライン エンドポイントで公開されている各パイプラインは、バージョン管理されます。 エンドポイントの既定のパイプラインを選択するか、REST 呼び出しでバージョンを指定できます。
 

#### <a name="iot-module-endpoints"></a>IoT モジュール エンドポイント

デプロイされる IoT モジュール エンドポイントは Docker コンテナーであり、モデルとそれに関連付けられているスクリプトまたはアプリケーション、および追加の依存関係が含まれます。 エッジ デバイス上の Azure IoT Edge を使用して、これらのモジュールをデプロイします。

監視を有効にしてある場合、Azure では Azure IoT Edge モジュール内のモデルから利用統計情報を収集します。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーのストレージ アカウント インスタンスに格納されます。

Azure IoT Edge ではモジュールが実行されるのを保証し、モジュールをホストしているデバイスを監視します。 
## <a name="automation"></a>オートメーション

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) は Azure CLI への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能には、機械学習アクティビティを自動化するためのコマンドが用意されています。

### <a name="ml-pipelines"></a>ML パイプライン

[機械学習パイプライン](concept-ml-pipelines.md)を使用し、機械学習フェーズをつなげるワークフローを作成して管理します。 たとえば、パイプラインには、データ準備、モデル トレーニング、モデル デプロイ、推論/スコアリングの各フェーズが含まれることが考えられます。 それぞれのフェーズには、複数のステップを含めることができ、各ステップは、さまざまなコンピューティング先において無人実行することができます。 

パイプライン手順は再利用できます。また、その手順の出力が変わっていない場合は、前の手順を再実行しなくても実行できます。 たとえば、データが変わっていない場合は、コストのかかるデータ準備手順を再実行することなく、モデルを再トレーニングできます。 また、パイプラインを使用すると、機械学習ワークフローの別の領域で作業しているときに、データ サイエンティストが共同作業することもできます。

## <a name="monitoring-and-logging"></a>監視およびログ記録

Azure Machine Learning で提供される監視およびログ記録機能は次のとおりです。

* "__データ科学者__" の場合、実験を監視し、トレーニングの実行から情報をログに記録することができます。 詳細については、次の記事を参照してください。
   * [トレーニングの実行の開始、監視、およびキャンセル](how-to-manage-runs.md)
   * [トレーニング実行のメトリックを記録する](how-to-track-experiments.md)
   * [MLflow を使用して実験を追跡する](how-to-use-mlflow.md)
   * [TensorBoard を使用して実行を視覚化する](how-to-monitor-tensorboard.md)
* "__管理者__" の場合、Azure Monitor を使用して、ワークスペース、関連する Azure リソース、およびリソースの作成や削除などのイベントに関する情報を監視できます。 詳細については、[Azure Machine Learning を監視する方法](monitor-azure-machine-learning.md)に関する記事を参照してください。
* __DevOps__ または __MLOps__ の場合、Web サービスまたは IoT Edge モジュールとしてデプロイされたモデルによって生成された情報を監視して、デプロイに関する問題を特定し、サービスに送信されたデータを収集できます。 詳細については、[モデル データの収集](how-to-enable-data-collection.md)および [Application Insights での監視](how-to-enable-app-insights.md)に関する記事を参照してください。

## <a name="interacting-with-your-workspace"></a>ワークスペースの操作

### <a name="studio"></a>スタジオ

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) には、ワークスペース内のすべての成果物の Web ビューが用意されています。  データセット、実験、パイプライン、モデル、エンドポイントの結果と詳細を確認できます。  スタジオのコンピューティング リソースとデータストアを管理することもできます。

スタジオでは、Azure Machine Learning の一部である対話型ツールにアクセスすることもできます。

+ コードを記述せずにワークフローの手順を行うための [Azure Machine Learning デザイナー](concept-designer.md)
+ [自動機械学習](concept-automated-ml.md)の Web エクスペリエンス
+ 統合された Jupyter ノートブック サーバーで独自のコードを作成して実行するための [Azure Machine Learning ノートブック](how-to-run-jupyter-notebooks.md)
+ データをラベル付けするプロジェクトを作成、管理、および監視するための[データのラベル付けプロジェクト](how-to-create-labeling-projects.md)

### <a name="programming-tools"></a>プログラミング ツール

> [!IMPORTANT]
> 以下に "(プレビュー)" と付記されているツールは、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

+  [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro) を使用して、Python 環境でサービスを操作します。
+ [Azure Machine Learning SDK for R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) を使用して、R 環境でサービスを操作します (プレビュー)。
+ [Azure Machine Learning デザイナー](concept-designer.md)を使用して、コードを記述せずにワークフローの手順を行います。 
+ [Azure Machine Learning CLI](./reference-azure-machine-learning-cli.md) を自動化に使用します。
+ [多数モデル ソリューション アクセラレータ](https://aka.ms/many-models) (プレビュー) は Azure Machine Learning 上に構築されており、数百または数千もの機械学習モデルをトレーニング、操作、管理できます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の利用を開始するには、以下を参照してください。

* [Azure Machine Learning とは](overview-what-is-azure-ml.md)
* [Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)
* [チュートリアル (パート 1): モデルをトレーニングする](tutorial-train-models-with-aml.md)