---
title: Web サービスとしてデプロイされたモデルの認証を構成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Web サービスにデプロイされた機械学習モデルの認証を構成する方法について説明します。
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447461"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Web サービスとしてデプロイされたモデルの認証を構成する

Azure Machine Learning を使用すると、トレーニング済みの機械学習モデルを Web サービスとしてデプロイできます。 この記事では、これらのデプロイの認証を構成する方法について説明します。

Azure Machine Learning によって作成されたモデル デプロイは、次の 2 つの認証方法のいずれかを使用するように構成できます。

* **キーベース**:静的キーを使用して、Web サービスに対する認証が行われます。
* **トークンベース**:(Azure Active Directory を使用して) Azure Machine Learning ワークスペースから一時的なトークンを取得し、Web サービスに対する認証に使用する必要があります。 このトークンは、一定の時間が経過すると期限切れになり、Web サービスの操作を続行するには更新する必要があります。

    > [!NOTE]
    > トークンベースの認証は、Azure Kubernetes Service にデプロイする場合にのみ使用できます。

## <a name="key-based-authentication"></a>キーベースの認証

Azure Kubernetes Service (AKS) にデプロイされた Web サービスでは、キーベースの認証が既定で *有効* になっています。

Azure Container Instances (ACI) のデプロイ サービスでは、キーベースの認証が既定で *無効* になっていますが、ACI Web サービスの作成時に `auth_enabled=True` を設定して有効にすることができます。 キーベースの認証が有効になっている ACI デプロイ構成を作成するコードの例を次に示します。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

次に、`Model` クラスを使用して、デプロイでカスタム ACI 構成を使用できます。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

認証キーをフェッチするには、`aci_service.get_keys()` を使用します。 キーを再生成するには、`regen_key()` 関数を使用して、**Primary** または **Secondary** のいずれかを渡します。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>トークンベースの認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JSON Web トークンを提示する必要があります。 トークンは、指定された期間後に期限切れとなり、呼び出しを続行するには更新する必要があります。

* Azure Kubernetes Service にデプロイする場合、トークン認証は **既定で無効** になります。
* Azure Container Instances にデプロイする場合、トークン認証は **サポートされません**。
* トークン認証は、**キーベースの認証と同時に使用することはできません**。

トークン認証を制御するには、デプロイの作成時や更新時に `token_auth_enabled` パラメーターを使用します。

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JSON Web トークン (JWT) とそのトークンの有効期限を取得できます。

> [!TIP]
> サービス プリンシパルを使用してトークンを取得し、トークンの取得に必要な最小限のアクセス権を付与する場合は、ワークスペースで **閲覧者** ロールに割り当てます。

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。 Python SDK の外部でトークンを更新する必要がある場合は、前に説明したように、サービス プリンシパル認証で REST API を使用して定期的に `service.get_token()` 呼び出しを行う方法があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。
>
> トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その結果、ワークスペースのリージョンが再び使用可能になるまで Azure AD Authentication は使用できなくなります。
>
> また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="next-steps"></a>次のステップ

配置済みモデルに対する認証の詳細については、[Web サービスとしてデプロイされたモデルのクライアントの作成](how-to-consume-web-service.md)に関するページを参照してください。