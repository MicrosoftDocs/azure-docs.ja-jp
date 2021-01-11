---
title: Azure Machine Learning とは
description: Azure Machine Learning の概要。これは、高度な分析アプリケーションの開発、実験、デプロイをクラウド規模で行うプロフェッショナルなデータ サイエンティスト向けの統合されたエンドツーエンドのデータ サイエンス ソリューションです。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: 2c043ab535504a02a151d308de0562debdba950a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145894"
---
# <a name="what-is-azure-machine-learning"></a>Azure Machine Learning とは

この記事では、ML モデルのトレーニング、デプロイ、自動化、管理、追跡に使用できるクラウドベースの環境である Azure Machine Learning について説明します。 

Azure Machine Learning は、従来の ML からディープ ラーニング、教師あり学習と教師なし学習まで、あらゆる種類の機械学習に使用できます。 SDK を使用して Python または R のコードを記述するか、または [Studio](#build-ml-models-in-the-studio) でのコード不要 (またはローコード) オプションを使用するかにかかわらず、Azure Machine Learning ワークスペースで機械学習およびディープ ラーニング モデルを構築、トレーニング、追跡できます。 

ローカル コンピューターでトレーニングを開始し、その後、クラウドにスケールアウトします。 

また、このサービスは、PyTorch、TensorFlow、scikit-learn、Ray RLlib など、ディープ ラーニングや強化のための一般的なオープン ソース ツールと連携します。 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **無料試用版**  Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。 Azure サービスを使用するクレジットを取得できます。 このクレジットを使い切った後に、アカウントを保持して、[無料の Azure サービス](https://azure.microsoft.com/free/)を使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。


## <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、コンピューターで既存のデータを使って、将来の動き、結果、傾向を予測できるデータ サイエンスの手法の 1 つです。 機械学習を使用することで、明示的にプログラムすることなく、コンピューターが学習します。

機械学習からの予想や予測によってアプリやデバイスの機能性を高めることができます。 たとえばオンライン ショッピングでは、利用者が欲しいと考えそうな他の商品を、過去の購入履歴に基づいてお勧めするのに機械学習が役立っています。 また、クレジット カードの読み取り時に、機械学習では、トランザクションをトランザクションのデータベースと比較することで不正の検出を支援します。 また、ロボット掃除機が部屋を掃除するとき、機械学習は、作業が行われているかどうかを判断するのを支援します。

## <a name="machine-learning-tools-to-fit-each-task"></a>各タスクに適合する機械学習ツール 

Azure Machine Learning には、次のような、開発者やデータ科学者が機械学習ワークフロー用に必要とするすべてのツールが用意されています。
+ [Azure Machine Learning デザイナー](tutorial-designer-automobile-price-train-score.md) (プレビュー): ドラッグアンドドロップ モジュールを使用して実験を構築し、パイプラインをデプロイします。

+ Jupyter Notebook: [サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks)を使用するか、独自のノートブックを作成して、<a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK for Python</a> のサンプルを機械学習に活用します。 

+ R スクリプトまたはノートブックでは、<a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK for R</a> を使用して独自のコードを書いたり、デザイナーで R モジュールを使用したりします。

+ + [多くのモデルのソリューション アクセラレータ](https://aka.ms/many-models) (プレビュー) は Azure Machine Learning 上に構築されており、数百または数千もの機械学習モデルをトレーニング、操作、管理できます。

+ [Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)

+ [Machine Learning CLI](reference-azure-machine-learning-cli.md)

+ PyTorch、TensorFlow、scikit-learn などの、オープンソースのフレームワーク

+ Ray RLlib を使用した[強化学習](how-to-use-reinforcement-learning.md)

[MLflow を使用してメトリックを追跡し、モデルをデプロイする](how-to-use-mlflow.md)ことや、Kubeflow を使用して[エンドツーエンドのワークフロー パイプラインを構築する](https://www.kubeflow.org/docs/azure/)こともできます。

## <a name="build-ml-models-in-python-or-r"></a>Python または R で ML モデルを構築する

Azure Machine Learning の <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> または <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a> を使用してローカル コンピューターでトレーニングを開始します。 その後、クラウドにスケールアウトすることができます。 

クラウドのパワーと、Azure Machine Learning コンピューティングや [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) などの利用可能な多数の[コンピューティング先](how-to-create-attach-compute-sdk.md)と[高度なハイパーパラメーター調整サービス](how-to-tune-hyperparameters.md)を活用することで、高品質のモデルを手早く構築できます。

SDK を使用して、[モデルのトレーニングと調整を自動化](tutorial-auto-train-models.md)することもできます。

## <a name="build-ml-models-in-the-studio"></a>Studio で ML モデルを構築する

[Azure Machine Learning Studio](https://www.ml.azure.com) は Azure Machine Learning 内の Web ポータルであり、モデルのトレーニング、デプロイ、アセット管理を少量のコードで、またはコードを一切記述することなく行うことができます。 Studio は Azure Machine Learning SDK と統合され、シームレスなエクスペリエンスが実現されています。 詳細については、「[Azure Machine Learning Studio とは](overview-what-is-machine-learning-studio.md)」を参照してください。

+ **Azure Machine Learning デザイナー (プレビュー)**

  [デザイナー](concept-designer.md)を使用すると、コードを書かなくても、機械学習モデルのトレーニングとデプロイを行うことができます。 まずは、[デザイナーのチュートリアル](tutorial-designer-automobile-price-train-score.md)をお試しください。 

  ![Azure Machine Learning デザイナーのドラッグ アンド ドロップ インターフェイスのアニメーション GIF](media/concept-designer/designer-drag-and-drop.gif)

+ **実験を追跡する**

  Studio で[データ サイエンスの実験を追跡して視覚化](tutorial-first-experiment-automated-ml.md)する方法をご覧ください。 

    ![Azure Machine Learning Studio での実行の詳細](media/how-to-track-experiments/experimentation-tab.gif)


+ **その他にもたくさんあります。**

  Azure Machine Learning Studio ([ml.azure.com](https://www.ml.azure.com)) にアクセスしてください。 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps:デプロイとライフサイクル管理
適切なモデルがあれば、Web サービス、IoT デバイス、または Power BI で簡単に使用できます。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関する記事を参照してください。

デプロイされたモデルは、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)、[Azure Machine Learning Studio](https://ml.azure.com)、または [Machine Learning CLI](reference-azure-machine-learning-cli.md) を使用して管理できます。

これらのモデルを使用して、[リアルタイム](how-to-consume-web-service.md)で予測を返したり、データが大量の場合は[非同期で](how-to-use-parallel-run-step.md)予測を返したりすることができます。

さらに、高度な[機械学習パイプライン](concept-ml-pipelines.md)を使用して、データの準備、モデルのトレーニングと評価、およびデプロイの各手順で共同で作業することができます。 パイプラインを使用すると、次のことができます。

* クラウドでエンドツーエンドの機械学習プロセスを自動化する
* コンポーネントを再利用し、必要なときにのみステップを再実行する
* ステップごとに異なるコンピューティング リソースを使用する
* バッチ スコアリング タスクを実行する

スクリプトを使用して機械学習ワークフローを自動化する場合、[Machine Learning CLI](reference-azure-machine-learning-cli.md) には、トレーニング実行の送信やモデルのデプロイなどの一般的なタスクを実行するコマンドライン ツールが用意されています。

Azure Machine Learning の基本的な使い方については、「[次の手順](#next-steps)」を参照してください。

## <a name="integration-with-other-services"></a>他のサービスとの統合

Azure Machine Learning は、Azure プラットフォーム上の他のサービスとの連携に加え、Git や MLFlow といったオープン ソース ツールとの統合にも対応します。

+ コンピューティング先 (__Azure Kubernetes Service__、__Azure Container Instances__、__Azure Databricks__、__Azure Data Lake Analytics__、__Azure HDInsight__ など)。 コンピューティング先の詳細については、[コンピューティング先の概要](concept-compute-target.md)に関するページを参照してください。
+ __Azure Event Grid__。 詳細については、[Azure Machine Learning イベントを使用する方法](concept-event-grid-integration.md)に関するページを参照してください。
+ __Azure Monitor__。 詳細については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
+ データ ストア (__Azure Storage アカウント__、__Azure Data Lake Storage__、__Azure SQL Database__、__Azure Database for PostgreSQL__、__Azure Open Datasets__ など)。 詳細については、「[Azure ストレージ サービスのデータにアクセスする](how-to-access-data.md)」および「[Azure Open Datasets を使用してデータセットを作成する](how-to-create-register-datasets.md)」を参照してください。
+ __Azure Virtual Networks__。 詳細については、[仮想ネットワークでの実験と推論の安全な実行](how-to-enable-virtual-network.md)に関するページを参照してください。
+ __Azure Pipelines__。 詳細については、「[機械学習モデルのトレーニングとデプロイ](/azure/devops/pipelines/targets/azure-machine-learning)」を参照してください。
+ __Git リポジトリのログ__。 詳細については、「[Git 統合](concept-train-model-git-integration.md)」を参照してください。
+ __MLFlow__。 詳細については、[MLflow を使用してメトリックを追跡し、モデルをデプロイする方法](how-to-use-mlflow.md)に関するページを参照してください。 
+ __Kubeflow__。 詳細については、[エンド ツー エンドのワークフロー パイプラインを構築する方法](https://www.kubeflow.org/docs/azure/)に関するページを参照してください。

### <a name="secure-communications"></a>セキュリティで保護された通信

Azure Storage アカウントやコンピューティング先などのリソースは、仮想ネットワーク内で安全に使用してモデルをトレーニングし、推論を実行することができます。 詳細については、[仮想ネットワークでの実験と推論の安全な実行](how-to-enable-virtual-network.md)に関するページを参照してください。

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Basic Edition と Enterprise Edition

Azure Machine Learning には、機械学習のニーズに合わせて調整された 2 つのエディションがあります。
+ **Basic**:コードファースト エクスペリエンスによるクラウド規模のオープンソース開発に最適です。

+ **エンタープライズ**:Basic の全機能に加え、Web インターフェイス (Studio) およびすべてのスキル レベルに対応する安全で包括的な ML ライフサイクル管理が利用できます。

これらのエディションと提供される機能の詳細については、["Azure Machine Learning のエディション" に関する記事](concept-editions.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- 任意の方法で最初の実験を作成します。
  + [Python ノートブックを使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-1st-r-experiment.md) 
  + [自動化された機械学習を使用して、ML モデルをトレーニングおよびデプロイする](tutorial-first-experiment-automated-ml.md) 
  + [デザイナーのドラッグ アンド ドロップ機能を使用して、トレーニングおよびデプロイする](tutorial-designer-automobile-price-train-score.md) 
  + [Machine Learning CLI を使用して、モデルをトレーニングおよびデプロイする](tutorial-train-deploy-model-cli.md)

- 機械学習シナリオを構築、最適化、および管理する[機械学習パイプライン](concept-ml-pipelines.md)について学習します。

- [Azure Machine Learning のアーキテクチャと概念](concept-azure-machine-learning-architecture.md)に関する詳細な記事を読みます。
