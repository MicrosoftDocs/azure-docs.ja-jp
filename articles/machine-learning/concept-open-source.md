---
title: オープンソースの機械学習の統合
titleSuffix: Azure Machine Learning
description: オープンソースの Python 機械学習フレームワークを使用して、Azure Machine Learning でエンドツーエンドの機械学習ソリューションをトレーニング、デプロイ、管理する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98223076"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>オープンソースと Azure Machine Learning プロジェクトとの統合

オープンソースの Python 機械学習ライブラリとプラットフォームを使用して、Azure Machine Learning でエンドツーエンドの機械学習プロセスをトレーニング、デプロイ、管理できます。  Jupyter Notebooks や Visual Studio Code などの開発ツールを使用して、Azure Machine Learning の既存のモデルとスクリプトを活用できます。  

この記事では、これらのオープンソース ライブラリとプラットフォームについて詳しく説明します。

## <a name="train-open-source-machine-learning-models"></a>オープンソースの機械学習モデルをトレーニングする

機械学習のトレーニング プロセスには、タスクを実現したり問題を解決したりするためのデータに対するアルゴリズムの適用が伴います。 問題に応じて、タスクとデータに最適なさまざまなアルゴリズムを選択できます。 機械学習のさまざまな部門の詳細については、[ディープ ラーニングと機械学習に関する記事](./concept-deep-learning-vs-machine-learning.md)と[機械学習アルゴリズム チート シート](algorithm-cheat-sheet.md)を参照してください。

### <a name="preserve-data-privacy-using-differential-privacy"></a>差分プライバシーを使用してデータのプライバシーを維持する

機械学習モデルをトレーニングするには、データが必要です。 それは機微なデータである場合があり、データが確実にセキュリティで保護され、公開されないようにすることが重要です。 差分プライバシーは、データセット内の情報の機密性を維持する手法です。 詳細については、[データのプライバシーの維持](concept-differential-privacy.md)に関する記事を参照してください。 

[SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) のようなオープンソースの差分プライバシー ツールキットは、Azure Machine Learning ソリューションの中で[データのプライバシーを維持する](how-to-differential-privacy.md)ために役立ちます。

### <a name="classical-machine-learning-scikit-learn"></a>クラシックな機械学習: scikit-learn

分類、クラスタリング、回帰などのクラシックな機械学習アルゴリズムのタスク を含むトレーニング タスクでは、Scikit-learn のようなものを使用することがあります。 花分類モデルをトレーニングする方法については、[Scikit-learn を使用してトレーニングする方法に関する記事](how-to-train-scikit-learn.md)を参照してください。

### <a name="neural-networks-pytorch-tensorflow-keras"></a>ニューラル ネットワーク: PyTorch、TensorFlow、Keras

機械学習のサブセットであり、ニューラル ネットワークとして知られているオープンソースの機械学習アルゴリズムは、Azure Machine Learning でのディープ ラーニング モデルのトレーニングに役立ちます。

オープンソースのディープ ラーニング フレームワークとハウツー ガイドには次のものがあります。

 *  [PyTorch](https://github.com/pytorch/pytorch): [転移学習を使用してディープ ラーニングの画像分類モデルをトレーニングする](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [TensorFlow を使用して手書きの数字を認識する](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [Keras を使用して画像を分析するニューラル ネットワークを構築する](how-to-train-keras.md)

ディープ ラーニング モデルのゼロからのトレーニングには、しばしば、多くの時間、データ、およびコンピューティング リソースが必要です。 転移学習を使用することで、トレーニング プロセスをショートカットできます。 転移学習は、ある問題を解決することで得られた知識を、異なるが関連している問題に適用する手法です。 つまり、既存のモデルを別の目的で使用できます。 転移学習の詳細については、[ディープ ラーニングと機械学習に関する記事](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning)を参照してください。

### <a name="reinforcement-learning-ray-rllib"></a>強化学習: Ray RLLib

強化学習は、アクション、状態、および報酬を使用してモデルをトレーニングする人工知能手法です。強化学習エージェントは、環境の現在の状態に基づいて、指定された報酬を最大化する定義済みのアクションのセットを取得する方法を学習します。 

[Ray RLLib](https://github.com/ray-project/ray) プロジェクトには、トレーニング プロセス全体で高いスケーラビリティを実現できる機能セットが用意されています。 反復プロセスでは、強化学習エージェントがタスクを達成するための最適な方法を学習するための試みが行われるため、時間とリソースが大量に消費されます。  また、Ray RLLib では、TensorFlow や PyTorch などのディープ ラーニング フレームワークがネイティブでサポートされています。  

Azure Machine Learning での Ray RLLib の使用方法については、[強化学習モデルをトレーニングする方法](how-to-use-reinforcement-learning.md)に関する記事を参照してください。

### <a name="monitor-model-performance-tensorboard"></a>モデルのパフォーマンスを監視する: TensorBoard

1 つまたは複数のモデルのトレーニングでは、モデルが想定どおりに動作していることを確認するために、目的のメトリックを視覚化して検査する必要があります。 [Azure Machine Learning で TensorBoard を使用して実験メトリックを追跡して視覚化する](./how-to-monitor-tensorboard.md)ことができます。

### <a name="frameworks-for-interpretable-and-fair-models"></a>解釈可能で公正なモデルのためのフレームワーク

機械学習システムは、銀行業務、教育、医療などの社会のさまざまな分野で使用されています。 そのため、意図しない結果を防ぐために、これらのシステムでは、行われる予測と推奨事項を説明できる必要があります。

[InterpretML](https://github.com/interpretml/interpret/) や Fairlearn (https://github.com/fairlearn/fairlearn) ) のようなオープンソース フレームワークと Azure Machine Learning を連携させることで、透明性が高く公正な機械学習モデルが作成されます。

公正で解釈可能なモデルを構築する方法の詳細については、次の記事を参照してください。

- [Azure Machine Learning でのモデルの解釈可能性](how-to-machine-learning-interpretability.md)
- [機械学習モデルを解釈して説明する](how-to-machine-learning-interpretability-aml.md)
- [AutoML モデルを説明する](how-to-machine-learning-interpretability-automl.md)
- [機械学習モデルでの公平性を軽減する](concept-fairness-ml.md)
- [Azure Machine Learning を使用してモデルの公平性を評価する](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>モデル デプロイ

モデルがトレーニングされ、運用環境で使用する準備ができたら、デプロイ方法を選択する必要があります。 Azure Machine Learning には、さまざまなデプロイ ターゲットが用意されています。 詳細については、[デプロイする方法と場所に関する記事](./how-to-deploy-and-where.md)を参照してください。

### <a name="standardize-model-formats-with-onnx"></a>ONNX でモデルの形式を標準化する

トレーニングが完了すると、学習したパラメーターなどのモデルの内容がシリアル化され、ファイルに保存されます。 各フレームワークには、独自のシリアル化形式があります。 さまざまなフレームワークとツールを使用している場合、これは、各フレームワークの要件に従ってモデルをデプロイする必要があることを意味します。 このプロセスを標準化するために、Open Neural Network Exchange (ONNX) 形式を使用できます。 ONNX は、人工知能モデルのオープンソース形式です。 ONNX は、フレームワーク間の相互運用性をサポートしています。 つまり、PyTorch などの多くの一般的な機械学習フレームワークのいずれかでモデルをトレーニングして ONNX 形式に変換し、ML.NET などの別のフレームワークで ONNX モデルを使用することができます。

ONNX と ONNX モデルの使用方法の詳細については、次の記事を参照してください。

- [ONNX を使用して ML モデルを作成して加速化する](concept-onnx.md)
- [.NET アプリケーションで ONNX モデルを使用する](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>モデルをコンテナーとしてパッケージ化してデプロイする

Docker などのコンテナー テクノロジは、モデルを Web サービスとしてデプロイするための 1 つの方法です。 コンテナーによって、再現性のあるソフトウェア環境を構築して調整するためのプラットフォームとリソースに依存しない方法が提供されます。 これらのコア テクノロジで、[事前構成済みの環境](./how-to-use-environments.md)、[事前構成済みのコンテナー イメージ](./how-to-deploy-custom-docker-image.md)、またはカスタム設定を使用して、機械学習モデルを [Kubernetes クラスター](./how-to-deploy-azure-kubernetes-service.md?tabs=python)などにデプロイできます。 GPU の負荷が高いワークフローでは、NVIDIA Triton 推論サーバーなどのツールを使用して、[GPU の使用予測を行う](how-to-deploy-with-triton.md?tabs=python)ことができます。

### <a name="secure-deployments-with-homomorphic-encryption"></a>準同型暗号を使用したセキュリティで保護されたデプロイ

デプロイをセキュリティで保護することは、デプロイ プロセスの重要な部分です。 [暗号化された推論サービスをデプロイする](how-to-homomorphic-encryption-seal.md)には、オープンソースの Python ライブラリである `encrypted-inference` を使用します。 `encrypted inferencing` パッケージには、準同型暗号ライブラリの [Microsoft SEAL](https://github.com/Microsoft/SEAL) に基づくバインドが提供されています。

## <a name="machine-learning-operations-mlops"></a>機械学習の運用 (MLOps)

一般に機械学習の DevOps とみなされている機械学習の運用 (MLOps) では、透明性が高く、回復性を備えた、再現可能な機械学習ワークフローを構築できます。 MLOps の詳細については、[MLOps の概要に関する記事](./concept-model-management-and-deployment.md)を参照してください。 

継続的インテグレーション (CI) や継続的デプロイ (CD) などの DevOps プラクティスを使用することで、エンドツーエンドの機械学習ライフサイクルを自動化し、その周囲のガバナンス データを把握できます。 [機械学習の CI/CD パイプラインを GitHub アクション](./how-to-github-actions-machine-learning.md)で定義して、Azure Machine Learning のトレーニングとデプロイのタスクを実行できます。 

ソフトウェアの依存関係、メトリック、メタデータ、データ、およびモデルのバージョンを把握することは、透明性、再現可能性、および監査可能性を備えたパイプラインを構築するための MLOps プロセスの重要な部分です。 このタスクのために、[Azure Machine Learning の MLFlow](how-to-use-mlflow.md) を使用できます。[Azure Databricks で機械学習モデルをトレーニングする](./how-to-use-mlflow-azure-databricks.md)ときにも、それを使用できます。 また、[MLflow モデルを Azure Web サービスとしてデプロイ](how-to-deploy-mlflow-models.md)することもできます。 
