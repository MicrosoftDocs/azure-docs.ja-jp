---
title: Azure Container Instances にモデルをデプロイする方法
titleSuffix: Azure Machine Learning
description: Azure Container Instances を使用して Web サービスとして Azure Machine Learning のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 27988edaf9d6fe70288352b0ba45945e32976d4d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034645"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Azure Container Instances にモデルをデプロイする

Azure Machine Learning を使用して Azure Container Instances (ACI) にモデルを Web サービスとしてデプロイする方法を説明します。 次のいずれかの条件に当てはまる場合に Azure Container Instances を使用します。

- モデルを迅速にデプロイおよび検証する必要があります。 事前に ACI コンテナーを作成する必要はありません。 これらはデプロイ プロセスの一部として作成されます。
- 開発中のモデルをテストします。 

ACI の利用可能なクォータとリージョンについては、[Azure Container Instances のクォータとリージョンの可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- ワークスペースに登録されている機械学習モデル。 モデルが登録されていない場合は、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-vscode-tools.md)。

- この記事の __Python__ コード スニペットは、次の変数が設定されていることを前提としています。

    * `ws` - 使用しているワークスペースに設定されている。
    * `model` - 登録済みのモデルに設定されている。
    * `inference_config` - モデルの推論構成に設定されている。

    これらの変数の設定について詳しくは、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- この記事の __CLI__ スニペットは、`inferenceconfig.json` ドキュメントを作成済みであることを前提としています。 このドキュメントの作成について詳しくは、「[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)」をご覧ください。

## <a name="deploy-to-aci"></a>ACI にデプロイする

Azure Container Instances にモデルをデプロイするには、必要なコンピューティング リソースが記述されている __デプロイ構成__ を作成します。 たとえば、コアの数やメモリなどです。 また、モデルと Web サービスのホストに必要な環境を記述した __推論構成__ も必要です。 推論構成の作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="using-the-sdk"></a>SDK を使用する

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

この例で使われているクラス、メソッド、パラメーターについて詳しくは、次のリファレンス ドキュメントをご覧ください。

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI の使用

CLI を使用してデプロイするには、次のコマンドを使用します。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。 

## <a name="using-vs-code"></a>VS コードを使用する

[VS コードを使用したモデルのデプロイ](how-to-vscode-tools.md#deploy-and-manage-models)に関する記事を参照してください。

> [!IMPORTANT]
> テストするための ACI コンテナーを事前に作成する必要はありません。 ACI コンテナーは必要に応じて作成されます。

## <a name="update-the-web-service"></a>Web サービスを更新する

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>次の手順

* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
