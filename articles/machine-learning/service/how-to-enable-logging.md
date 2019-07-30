---
title: Azure Machine Learning service でログ記録を有効にする
titleSuffix: Azure Machine Learning service
description: 既定の Python ロギング パッケージと SDK 固有の機能の両方を使用して、Azure Machine Learning service でログ記録を有効にする方法について説明します。
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 384cd2df9c629a73095cc55f4f6d65aa6a727225
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360967"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Azure Machine Learning service でログ記録を有効にする

Azure Machine Learning Python SDK を使用すると、既定の Python ロギング パッケージと SDK 固有の機能の両方を使用して、ローカルなログ記録と、ポータルのワークスペースへのログ記録の両方のログ記録を有効にすることができます。 ログでは、アプリケーションの状態に関するリアルタイムの情報が開発者に提供され、エラーまたは警告の診断に役立ちます。 この記事では、次の領域でログ記録を有効にするさまざまな方法について説明します。

> [!div class="checklist"]
> * モデルのトレーニングとコンピューティング先
> * イメージの作成
> * デプロイ済みのモデル
> * Python の `logging` 設定

[Azure Machine Learning service ワークスペースを作成する](setup-create-workspace.md) SDK について詳しくは、[ガイド](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)を使用してください。

## <a name="training-models-and-compute-target-logging"></a>モデルのトレーニングとコンピューティング先のログ記録

モデルのトレーニング プロセス中のログ記録を有効にするには複数の方法があり、例では一般的な設計パターンが示されています。 `Experiment` クラスの `start_logging` 関数を使用すると、実行関連のデータをクラウドのワークスペースに簡単にログ記録することができます。

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

その他のログ記録関数については、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) クラスのリファレンス ドキュメントを参照してください。

トレーニング進行中のアプリケーションの状態のローカルなログ記録を有効にするには、`show_output` パラメーターを使用します。 詳細なログ記録を有効にすると、トレーニング プロセスからの詳細情報と、リモート リソースまたはコンピューティング先に関する情報を見ることができます。 実験を送信するときのログ記録を有効にするには、次のコードを使用します。

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

結果の実行時に `wait_for_completion` 関数で同じパラメーターを使用することもできます。

```python
run.wait_for_completion(show_output=True)
```

SDK では、トレーニングの特定のシナリオにおいて、既定の Python ロギング パッケージの使用もサポートされています。 次の例では、`AutoMLConfig` オブジェクトで `INFO` のログ記録レベルを有効にしています。

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

永続的なコンピューティング先を作成するときに、`show_output` パラメーターを使用することもできます。 コンピューティング先の作成時にログ記録を有効にするには、`wait_for_completion` 関数でパラメーターを指定します。

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>イメージ作成時のログ記録

イメージの作成時のログ記録を有効にすると、ビルド プロセス中のエラーを確認できます。 `wait_for_deployment()` 関数で `show_output` パラメーターを設定します。

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>デプロイ済みモデルのログ記録

以前にデプロイした Web サービスからログを取得するには、サービスを読み込んで、`get_logs()` 関数を使用します。 ログには、デプロイ中に発生したエラーに関する詳細情報が含まれている場合があります。

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

また、Application Insights を有効にすることによって、Web サービスのカスタム スタック トレースを記録することもできます。これにより、要求/応答の時間、失敗率、例外を監視できます。 Application Insights を有効にするには、既存の Web サービスで `update()` 関数を呼び出します。

```python
service.update(enable_app_insights=True)
```

Azure portal で Application Insights を使用する方法について詳しくは、[ハウツー](how-to-enable-app-insights.md)記事をご覧ください。

## <a name="python-native-logging-settings"></a>Python のネイティブ ログ記録の設定

SDK の特定のログには、ログ レベルを DEBUG に設定するよう指示するエラーが含まれる場合があります。 ログ記録のレベルを設定するには、スクリプトに次のコードを追加します。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
