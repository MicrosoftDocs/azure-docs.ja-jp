---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: a03f71adc99063fee4374b1436b08adf5bab783d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511034"
---
`inferenceconfig.json` ドキュメント内のエントリは、[InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) クラスのパラメーターにマップされます。 次の表は、JSON ドキュメントのエントリとメソッド用パラメーターの間のマッピングについてまとめたものです。

| JSON エンティティ | メソッド パラメーター | 説明 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | イメージに対して実行するコードを含むローカル ファイルのパス。 |
| `sourceDirectory` | `source_directory` | 省略可能。 イメージを作成するためのすべてのファイルを含むフォルダーへのパス。パスにより、このフォルダーまたはサブ フォルダー内の任意のファイルへのアクセスが簡単になります。 ローカル コンピューターから Webservice の依存関係として、フォルダー全体をアップロードできます。 注: entry_script、conda_file、および extra_docker_file_steps パスは、source_directory パスへの相対パスです。 |
| `environment` | `environment` | 省略可能。  Azure Machine Learning [環境](/python/api/azureml-core/azureml.core.environment.environment)。|

Azure Machine Learning [環境](/python/api/azureml-core/azureml.core.environment.environment)の完全な仕様を、推論構成ファイルに含めることができます。 この環境がワークスペースに存在しない場合は、Azure Machine Learning によって作成されます。 それ以外の場合は、必要に応じて Azure Machine Learning によって環境が更新されます。 次の JSON は例です。

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

また、分離された CLI パラメーターで既存の Azure Machine Learning [環境](/python/api/azureml-core/azureml.core.environment.environment)を使用し、推論構成ファイルから "環境" キーを削除することもできます。 環境名には -e を使用し、環境のバージョンには --ev を使用します。 --ev を指定しない場合は、最新バージョンが使用されます。 推論構成ファイルの例を次に示します。

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

次のコマンドは、前述の推論構成ファイル (myInferenceConfig.json という名前) を使用してモデルをデプロイする方法を示しています。 

また、既存の Azure Machine Learning [環境](/python/api/azureml-core/azureml.core.environment.environment) (AzureML-Minimal という名前) の最新バージョンも使用します。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```