---
title: コンピューティング インスタンスにモデルをデプロイする
titleSuffix: Azure Machine Learning
description: コンピューティング インスタンスを使用して、Web サービスとして Azure Machine Learning のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98954548"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Azure Machine Learning コンピューティング インスタンスへのモデルのデプロイ



Azure Machine Learning を使用して Azure Machine Learning コンピューティング インスタンス にモデルを Web サービスとしてデプロイする方法を説明します。 次のいずれかの条件に当てはまる場合はコンピューティング インスタンスを使用します。

- モデルを迅速にデプロイおよび検証する必要があります。
- 開発中のモデルをテストします。

> [!TIP]
> コンピューティング インスタンスの Jupyter Notebook から、同じ VM 上の Web サービスへのモデルのデプロイは、"_ローカル デプロイ_" です。 この場合、"ローカル" コンピューターはコンピューティング インスタンスです。 デプロイの詳細については、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- コンピューティング インスタンスが実行されている Azure Machine Learning のワークスペース。 詳細については、[環境とワークスペースの設定](tutorial-1st-experiment-sdk-setup.md)に関する記事を参照してください。

## <a name="deploy-to-the-compute-instances"></a>コンピューティング インスタンスへデプロイする

ローカル デプロイを示すノートブックの例がコンピューティング インスタンスに含まれています。 次の手順を使用してノートブックを読み込み、モデルを Web サービスとして VM にデプロイします。

1. [Azure Machine Learning スタジオ](https://ml.azure.com)で、[ノートブック] を選択し、[サンプル ノートブック] の下にある [how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb] を選択します。 このノートブックをユーザー フォルダーに複製します。

1. 手順 1 で複製したノートブックを見つけて、このノートブックを実行するコンピューティング インスタンスを選択または作成します。

    ![ノートブックで実行されているローカル サービスのスクリーンショット](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. ノートブックには、サービスが実行されている URL とポートが表示されます。 たとえば、「 `https://localhost:6789` 」のように入力します。 また、`print('Local service port: {}'.format(local_service.port))` を含むセルを実行して、ポートを表示することもできます。

    ![実行されているローカル サービス ポートのスクリーンショット](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. コンピューティング インスタンスからサービスをテストするには、URL `https://localhost:<local_service.port>` を使用します。 リモート クライアントからテストするには、コンピューティング インスタンスで実行されているサービスのパブリック URL を取得します。 パブリック URL は、次の形式を使用して決定できます。 
    * ノートブック VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` 
    * コンピューティング インスタンス: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` 

    たとえば、次のように入力します。 
    * ノートブック VM: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * コンピューティング インスタンス: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>サービスをテストする

実行中のサービスにサンプル データを送信するには、次のコードを使用します。 値 `service_url` を、前の手順で取得した URL に置き換えます。

> [!NOTE]
> コンピューティング インスタンスでのデプロイに対して認証を行う場合、認証は Azure Active Directory を使用して行われます。 コード例の `interactive_auth.get_authentication_header()` を呼び出すと、AAD を使用してあなたが認証され、コンピューティング インスタンスでのサービスに対する認証に使用できるヘッダーが返されます。 詳細については、「[Azure Machine Learning のリソースとワークフローの認証を設定する](how-to-setup-authentication.md#interactive-authentication)」を参照してください。
>
> Azure Kubernetes Service または Azure Container Instances でのデプロイに対して認証を行う場合は、別の認証方法が使用されます。 詳細については、「[Web サービスとしてデプロイされた Azure Machine モデルの認証を構成する](how-to-authenticate-web-service.md)」を参照してください。

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>次のステップ

* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
