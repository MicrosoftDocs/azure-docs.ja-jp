---
title: Estimator から ScriptRunConfig への移行
titleSuffix: Azure Machine Learning
description: トレーニング ジョブを構成するために Estimator から ScriptRunConfig に移行するための移行ガイド。
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518874"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Estimator から ScriptRunConfig への移行

これまでは、Estimator、ScriptRunConfig、低レベルの RunConfiguration など、SDK を使用して Azure Machine Learning でトレーニング ジョブを構成する方法が複数ありました。   このようなわかりにくさや、ばらつきに対処するため、Azure ML のジョブ構成プロセスが簡素化されています。  現在、トレーニング ジョブの構成には、ScriptRunConfig を使用する方法が推奨されます。 

Estimator は、Python SDK の 1.19.0 リリースで非推奨となりました。 また、通常は RunConfiguration オブジェクトを自分で明示的にインスタンス化することは避け、代わりに ScriptRunConfig クラスを使用してジョブを構成する必要があります。

この記事では、Estimator から ScriptRunConfig に移行するときの一般的な考慮事項について説明します。

> [!IMPORTANT]
> Estimator から ScriptRunConfig に移行するには、Python SDK 1.15.0 以降を使用してください。

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig のドキュメントとサンプル
Azure Machine Learning のドキュメントとサンプルは、ジョブの構成と送信で [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) を使用するように更新されました。

ScriptRunConfig の使用方法の詳細については、次のドキュメントを参照してください。
* [トレーニングの実行を構成して送信する](how-to-set-up-training-targets.md)
* [PyTorch トレーニングの実行を構成する](how-to-train-pytorch.md)
* [TensorFlow トレーニングの実行を構成する](how-to-train-tensorflow.md)
* [scikit-learn 構成-トレーニングの実行を構成する](how-to-train-scikit-learn.md)

次のサンプルとチュートリアルも参照してください。
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>トレーニング環境を定義する
各種のフレームワーク Estimator には、Docker イメージに基づく構成済みの環境がありますが、これらのイメージの Dockerfile は非公開です。  そのため、これらの環境に何が含まれているかは、あまりわかりません。 また、Estimator は、環境関連の構成を、対応するコンストラクターで個別のパラメーター (`pip_packages`、`custom_docker_image`など) として取り込みます。

ScriptRunConfig を使用する場合、すべての環境関連の構成は、ScriptRunConfig コンストラクターの `environment` パラメーターに渡される `Environment` オブジェクトにカプセル化されます。 トレーニング ジョブを構成するには、トレーニング スクリプトに必要なすべての依存関係を備えた環境を指定します。 環境が指定されない場合、Azure ML は、Azure ML 基本イメージ (`azureml.core.environment.DEFAULT_CPU_IMAGE` によって定義されたイメージ) のいずれかを既定の環境として使用します。 環境を指定するには、いくつかの方法があります。

* [キュレーションされた環境を使用する](how-to-use-environments.md#use-a-curated-environment) - キュレーションされた環境とは、ワークスペース内で既定で使用できる定義済みの環境です。 各フレームワーク Estimator を支える構成済みのフレームワーク/バージョンの Docker イメージごとに、対応するキュレーションされた環境があります。
* [独自のカスタム環境を定義する](how-to-use-environments.md)

キュレーションされた PyTorch 1.6 環境をトレーニングに使用する例を次に示します。

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

トレーニング スクリプトを実行するプロセスで設定される **環境変数** を指定する場合は、環境オブジェクトを使用します。
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Azure ML 環境の構成と管理の詳細については、以下を参照してください。
* [環境の使用方法](how-to-use-environments.md)
* [キュレーションされた環境](resource-curated-environments.md)
* [カスタム Docker イメージを使用したトレーニング](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>トレーニング用のデータを使用する
### <a name="datasets"></a>データセット
トレーニングに Azure ML データセットを使用している場合は、`arguments` パラメーターを使用して、データセットを引数としてスクリプトに渡します。 そうすることで、引数を介してトレーニング スクリプトのデータ パス (マウント ポイントまたはダウンロード パス) を取得します。

次の例では、FileDataset `mnist_ds` がリモート コンピューティングにマウントされるトレーニング ジョブを構成します。
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (旧)
以前の DataReference の方法より Azure ML データセットを使用することをお勧めしますが、何らかの理由で Datareference をまだ使用している場合は、次のようにジョブを構成する必要があります。
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

トレーニング用のデータ使用に関する詳細については、次を参照してください。
* [Azure ML のデータセットを使用してトレーニングする](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>分散トレーニング
トレーニング用に分散ジョブを構成する必要がある場合は、ScriptRunConfig コンストラクターで `distributed_job_config` パラメーターを指定します。 それぞれの種類の分散ジョブに対して、[MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration)、[PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)、または [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) を渡します。

次の例では、MPI/Horovod で分散トレーニングを使用するように PyTorch トレーニング ジョブを構成します。
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

詳細については、次を参照してください。
* [PyTorch を使用した分散トレーニング](how-to-train-pytorch.md#distributed-training)
* [TensorFlow を使用した分散トレーニング](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>その他
何らかの理由で、基になる ScriptRunConfig の RunConfiguration オブジェクトにアクセスする必要がある場合は、次のようにします。
```
src.run_config
```

## <a name="next-steps"></a>次のステップ

* [トレーニングの実行を構成して送信する](how-to-set-up-training-targets.md)