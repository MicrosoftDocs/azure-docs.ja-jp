---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926969"
---
`inferenceconfig.json` ドキュメント内のエントリは、[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) クラスのパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | イメージに対して実行するコードを含むローカル ファイルのパス。 |
| `runtime` | `runtime` | 省略可能。 イメージに使用するランタイム。 現在サポートされているランタイムは `spark-py` と `python` です。 `environment` が設定されている場合には、これは無視されます。 |
| `condaFile` | `conda_file` | 省略可能。 イメージに使用する Conda 環境定義を含むローカル ファイルのパス。  `environment` が設定されている場合には、これは無視されます。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 省略可能。 イメージを設定するときに実行する追加の Docker 手順を含むローカル ファイルのパス。  `environment` が設定されている場合には、これは無視されます。|
| `sourceDirectory` | `source_directory` | 省略可能。 イメージを作成するためのファイルをすべて含むフォルダーのパス。 |
| `enableGpu` | `enable_gpu` | 省略可能。 イメージで GPU サポートを有効にするかどうか。 GPU イメージは、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などの Azure サービス上で使用する必要があります。 既定値は False です。 `environment` が設定されている場合には、これは無視されます。|
| `baseImage` | `base_image` | 省略可能。 基本イメージとして使用するカスタム イメージ。 基本イメージが指定されていない場合、イメージは指定されたランタイム パラメーターに基づきます。 `environment` が設定されている場合には、これは無視されます。 |
| `baseImageRegistry` | `base_image_registry` | 省略可能。 基本イメージを含むイメージ レジストリ。 `environment` が設定されている場合には、これは無視されます。|
| `cudaVersion` | `cuda_version` | 省略可能。 GPU のサポートが必要なイメージにインストールする CUDA のバージョン。 GPU イメージは、Azure Container Instances、Azure Machine Learning コンピューティング、Azure Virtual Machines、Azure Kubernetes Service などの Azure サービス上で使用する必要があります。 サポートされているバージョンは 9.0、9.1、10.0 です。 `enable_gpu` が設定されている場合、既定値は 9.1 です。 `environment` が設定されている場合には、これは無視されます。 |
| `description` | `description` | イメージの説明。 `environment` が設定されている場合には、これは無視されます。  |
| `environment` | `environment` | 省略可能。  Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

次の JSON は、CLI で使用する推論構成の例です。

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

次の JSON は、特定のバージョンがインストールされた既存の Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) を CLI で使用するための推論構成の例です：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

次の JSON は、最新のバージョンがインストールされた既存の Azure Machine Learning [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) を CLI で使用するための推論構成の例です：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
