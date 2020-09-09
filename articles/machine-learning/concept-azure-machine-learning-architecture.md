---
title: アーキテクチャと主要な概念
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を構成するアーキテクチャ、用語、概念について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750864"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning のしくみ:アーキテクチャと概念

Azure Machine Learning のアーキテクチャおよび概念について説明します。

> [!NOTE]
> この記事では、Azure Machine Learning で使用される用語と概念を定義しますが、Azure プラットフォームに関する用語と概念は定義しません。 Azure プラットフォームの用語について詳しくは、[Microsoft Azure 用語集](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)に関するページを参照してください。

## <a name="workspace"></a><a name="workspace"></a> ワークスペース

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Azure Machine Learning のアーキテクチャ":::

[機械学習ワークスペース](concept-workspace.md)は、Azure Machine Learning の最上位レベルのリソースです。  ワークスペースは、次のことを行う一元的な場所です。
* [コンピューティング](#compute-instance)など、モデルのトレーニングとデプロイに使用するリソースを管理します
* Azure Machine Learning を使用するときに作成する、次のようなアセットを格納します。
  * [環境](#environments)
  * [計](#runs)
  * [パイプライン](#ml-pipelines)
  * [データセット](#datasets-and-datastores)
  * [モデル](#models)
  * [エンドポイント](#endpoints)

ワークスペースには、ワークスペースによって使用される他の Azure リソースが含まれています。

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/):トレーニング中およびモデルのデプロイ時に使用する Docker コンテナーを登録します。 コストを最小限に抑えるために、ACR は、デプロイ イメージが作成されるまで**遅延読み込み**されます。
+ [Microsoft Azure Storage アカウント](https://azure.microsoft.com/services/storage/):ワークスペースの既定のデータストアとして使用されます。  Azure Machine Learning コンピューティングインスタンスで使用される Jupyter ノートブックもここに保存されます。
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/):モデルに関する監視情報を格納します。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):コンピューティング先で使用されるシークレット、およびワークスペースで必要な他の機密情報を格納します。

他のユーザーとワークスペースを共有できます。

## <a name="studio"></a>スタジオ

[Azure Machine Learning Studio](https://ml.azure.com) には、ワークスペース内のすべての成果物の Web ビューが用意されています。  このポータルでは、Azure Machine Learning の一部である対話型ツールにアクセスすることもできます。

+ [Azure Machine Learning デザイナー (プレビュー)](concept-designer.md) では、コードを記述せずにワークフローのステップを実行します
+ [自動機械学習](concept-automated-ml.md)の Web エクスペリエンス
+ データのラベル付けプロジェクトを作成、管理、および監視するための[データのラベル付けプロジェクト](how-to-create-labeling-projects.md)

##  <a name="computes"></a>コンピューティング

<a name="compute-targets"></a>[コンピューティング先](concept-compute-target.md)は、トレーニング スクリプトを実行したり、サービスのデプロイをホストしたりする場所であるマシンまたはマシンのセットです。 この場所は、ローカル コンピューターでも、リモート コンピューティング リソースでもかまいません。

Azure Machine Learning には、機械学習タスク用に構成されたクラウドベースの 2 つのフル マネージド コンピューティング リソースが導入されています。

* <a name="compute-instance"></a> **コンピューティング インスタンス** ([computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)):コンピューティング インスタンスは、機械学習用にインストールされた複数のツールと環境を含む仮想マシン (VM) です。 コンピューティング インスタンスを開発ワークステーションとして使用して、セットアップが不要なサンプル ノートブックの実行を開始します。 トレーニングおよび推論ジョブのコンピューティング ターゲットとしても使用できます。
* **コンピューティング クラスター** ([amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)):マルチノード スケーリング機能を備えた VM のクラスター。 ジョブが送信されるときに自動的にスケールアップされます。 大規模なジョブと運用環境のコンピューティング ターゲットに適しています。 トレーニング コンピューティング ターゲットとして、または開発/テスト デプロイのために使用します。

トレーニング コンピューティング ターゲットの詳細については、「[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)」を参照してください。  デプロイメント コンピューティング ターゲットの詳細については、「[デプロイメント ターゲット](concept-compute-target.md#deploy)」を参照してください。

## <a name="datasets-and-datastores"></a>データセットとデータストア

[**Azure Machine Learning Datasets**](concept-data.md#datasets) によって、データへのアクセスと操作がより容易になります。 データセットは、モデルのトレーニングやパイプラインの作成など、さまざまなシナリオでデータを管理します。 Azure Machine Learning SDK を使用すると、基礎となるストレージへのアクセス、データの探索、異なるデータセット定義のライフ サイクルの管理が可能になります。

データセットには、`from_delimited_files()` や `to_pandas_dataframe()` を使用するなど、一般的な形式のデータを操作するメソッドが用意されています。

詳細については、[Azure Machine Learning Datasets の作成と登録](how-to-create-register-datasets.md)に関するページを参照してください。  データセットのその他の使用例については、[サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)を参照してください。

[**データストア**](concept-data.md#datastores)は、Azure ストレージ アカウントに対するストレージの抽象化です。 各ワークスペースには既定のデータストアがあり、ユーザーは追加のデータストアを登録できます。 データストアのファイルを格納および取得するには、Python SDK API または Azure Machine Learning CLI を使用します。 

## <a name="models"></a>モデル

簡単に言うと、モデルとは入力を受け取って出力を生成するひとまとまりのコードです。 機械学習モデルの作成には、アルゴリズムの選択、アルゴリズムへのデータの提供、および[ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)が含まれます。 トレーニングはトレーニング済みモデルを生成する反復的なプロセスであり、トレーニング プロセス中にモデルが学習した内容がカプセル化されています。

モデルは、Azure Machine Learning での[実験](#experiments)の[実行](#runs)によって生成されます。 Azure Machine Learning の外部でトレーニングされるモデルを使用することもできます。 次に、ワークスペースに[モデルを登録します](#register-model)。

Azure Machine Learning は、フレームワークに依存しません。 モデルを作成するときは、Scikit-learn、XGBoost、PyTorch、TensorFlow、Chainer などの任意の人気のある機械学習フレームワークを使用できます。

Scikit-learn を使用したモデルのトレーニングの例については、「[チュートリアル: Azure Machine Learning でイメージ分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」を参照してください。

### <a name="model-registry"></a><a name="register-model"></a>モデル レジストリ
[ワークスペース](#workspace) > **モデル レジストリ**

**モデル レジストリ**により、Azure Machine Learning ワークスペース内のすべてのモデルを追跡できます。

モデルは、名前とバージョンによって識別されます。 既存のモデルと同じ名前でモデルを登録するたびに、レジストリではそれが新しいバージョンと見なされます。 バージョンはインクリメントされ、新しいモデルは同じ名前で登録されます。

モデルを登録するときに、追加のメタデータ タグを指定し、モデルを検索するときにそのタグを使用することができます。

> [!TIP]
> 登録済みモデルは、モデルを構成する 1 つまたは複数のファイルの論理コンテナーです。 たとえば、複数のファイルに格納されているモデルがある場合は、Azure Machine Learning ワークスペースに単一モデルとしてそれらを登録することができます。 登録後は、その登録済みモデルをダウンロードするかデプロイし、登録されたすべてのファイルを受信できます。

アクティブなデプロイで使用されている登録済みモデルを削除することはできません。

モデルの登録例については、[Azure Machine Learning での画像分類モデルのトレーニング](tutorial-train-models-with-aml.md)に関するページを参照してください。


### <a name="environments"></a>環境

[ワークスペース](#workspace) > **環境**

[環境](concept-environments.md)とは、機械学習モデルのトレーニングやスコアリングが行われる環境をカプセル化したものです。 環境では、トレーニングとスコアリングのスクリプトに関連する、Python パッケージ、環境変数、およびソフトウェア設定を指定します。

コード例については、[環境の使用方法](how-to-use-environments.md#manage-environments)に関するページの「環境の管理」セクションを参照してください。

### <a name="experiments"></a>実験

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

実行構成は、指定されたコンピューティング先でのスクリプトの実行方法を定義する一連の命令です。 構成には、既存の Python 環境を使用するかどうかや、仕様から構築される Conda 環境を使用するかどうかなど、広範な動作の定義セットが含まれます。

実行構成は、トレーニング スクリプトが含まれるディレクトリ内のファイルに保持できます。   またはメモリ内オブジェクトとして構築して実行の送信に使用できます。

実行構成の例については、[モデルをトレーニングするためのコンピューティング先の選択と使用](how-to-set-up-training-targets.md)に関するページを参照してください。

### <a name="estimators"></a>Estimator

一般的なフレームワークでのモデルのトレーニングを容易にするため、Estimator クラスを使用すると実行構成を簡単に構築できます。 汎用の [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) を作成し、それを使用して、自分で選択した任意の学習フレームワーク (scikit-learn など) を使用するトレーニング スクリプトを送信できます。

PyTorch、TensorFlow、Chainer タスクの場合、Azure Machine Learning には、これらのフレームワークを簡単に使用するための [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)、および [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Estimator が用意されています。

詳細については、次の記事を参照してください。

* [Estimator を使用して ML モデルをトレーニングする](how-to-train-ml-models.md)。
* [Azure Machine Learning を使用して PyTorch ディープ ラーニング モデルを大規模にトレーニングする](how-to-train-pytorch.md)。
* [Azure Machine Learning を使用して TensorFlow モデルを大規模にトレーニングおよび登録する](how-to-train-tensorflow.md)。
* [Azure Machine Learning を使用して大規模な Chainer モデルをトレーニングし、登録する](how-to-train-ml-models.md)。

### <a name="snapshots"></a>スナップショット

[ワークスペース](#workspace) > [実験](#experiments) > [実行](#runs) > **スナップショット**

実行を送信するときに、Azure Machine Learning によって、スクリプトが含まれているディレクトリが zip ファイルとして圧縮され、コンピューティング先に送られます。 その後、zip ファイルが抽出され、そこでスクリプトが実行されます。 Azure Machine Learning では、zip ファイルもスナップショットとして実行レコード内に格納されます。 ワークスペースにアクセスできるすべてのユーザーは、実行レコードを参照し、スナップショットをダウンできます。


### <a name="logging"></a>ログ記録

ソリューションを開発するときは、Python スクリプトで Azure Machine Learning Python SDK を使用して、任意のメトリックを記録します。 実行後にメトリックのクエリを行って、デプロイするモデルが実行によって生成されたかどうかを判断します。


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 これは、推定器、ML パイプライン、またはスクリプトの実行を使用して送信した実行で機能します。 SDK または Machine Learning CLI から送信された実行でも機能します。

詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

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

Web サービスを監視するために Application Insights テレメトリまたはモデルのテレメトリを有効にできます。 テレメトリ データにアクセスできるのは自分だけです。  これは Application Insights とストレージ アカウント インスタンスに格納されます。

自動スケールを有効にしてある場合は、Azure でデプロイが自動的にスケーリングされます。

Web サービスとしてのモデルのデプロイ例については、[Azure Container Instances での画像分類モデルのデプロイ](tutorial-deploy-models-with-aml.md)に関するページを参照してください。

#### <a name="iot-module-endpoints"></a>IoT モジュール エンドポイント

デプロイされる IoT モジュール エンドポイントは Docker コンテナーであり、モデルとそれに関連付けられているスクリプトまたはアプリケーション、および追加の依存関係が含まれます。 エッジ デバイス上の Azure IoT Edge を使用して、これらのモジュールをデプロイします。

監視を有効にしてある場合、Azure では Azure IoT Edge モジュール内のモデルから利用統計情報を収集します。 利用統計情報にアクセスできるのは機能を有効にしたユーザーだけであり、情報はそのユーザーのストレージ アカウント インスタンスに格納されます。

Azure IoT Edge ではモジュールが実行されるのを保証し、モジュールをホストしているデバイスを監視します。
. 
## <a name="automation"></a>オートメーション

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) は Azure CLI への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能には、機械学習アクティビティを自動化するためのコマンドが用意されています。

### <a name="ml-pipelines"></a>ML パイプライン

[機械学習パイプライン](concept-ml-pipelines.md)を使用し、機械学習フェーズをつなげるワークフローを作成して管理します。 たとえば、パイプラインには、データ準備、モデル トレーニング、モデル デプロイ、推論/スコアリングの各フェーズが含まれることが考えられます。 それぞれのフェーズには、複数のステップを含めることができ、各ステップは、さまざまなコンピューティング先において無人実行することができます。 

パイプライン手順は再利用できます。また、その手順の出力が変わっていない場合は、前の手順を再実行しなくても実行できます。 たとえば、データが変わっていない場合は、コストのかかるデータ準備手順を再実行することなく、モデルを再トレーニングできます。 また、パイプラインを使用すると、機械学習ワークフローの別の領域で作業しているときに、データ サイエンティストが共同作業することもできます。

## <a name="interacting-with-machine-learning"></a>機械学習の操作

> [!IMPORTANT]
> 以下に "(プレビュー)" と付記されているツールは、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

+  [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、Python 環境でサービスを操作します。
+ [Azure Machine Learning SDK for R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) を使用して、R 環境でサービスを操作します (プレビュー)。
+ [Azure Machine Learning デザイナー (プレビュー)](concept-designer.md) を使用して、コードを記述せずにワークフローの手順を行います。 (デザイナーを使用するには、[Enterprise ワークスペース](concept-workspace.md#upgrade)が必要です。)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) を自動化に使用します。
+ [多数モデル ソリューション アクセラレータ](https://aka.ms/many-models) (プレビュー) は Azure Machine Learning 上に構築されており、数百または数千もの機械学習モデルをトレーニング、操作、管理できます。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の利用を開始するには、以下を参照してください。

* [Azure Machine Learning とは](overview-what-is-azure-ml.md)
* [Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)
* [チュートリアル (パート 1): モデルをトレーニングする](tutorial-train-models-with-aml.md)
