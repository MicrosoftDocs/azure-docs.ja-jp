---
title: Azure Kubernetes Service にモデルをデプロイする方法
titleSuffix: Azure Machine Learning service
description: Azure Kubernetes Service を使用して Web サービスとして Azure Machine Learning service のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 490085da1e8f6b8e151168433836d59329887c6e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623960"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにモデルをデプロイする

Azure Machine Learning service を使って Azure Kubernetes Service (AKS) 上の Web サービスとしてモデルをデプロイする方法について説明します。 Azure Kubernetes Service は高スケールの運用デプロイに適してします。 次のいずれかの機能が必要な場合は、Azure Kubernetes Service を使用します。

- __高速の応答時間__。
- デプロイされたサービスの__自動スケール__。
- GPU や Field Programmable Gate Array (FPGA) などの__ハードウェア アクセラレーション__ オプション。

> [!IMPORTANT]
> クラスターのスケーリングは Azure Machine Learning SDK では提供されません。 AKS クラスターでのノードのスケーリングの詳細については、「[Azure Kubernetes Service (AKS) クラスターでノードの数をスケーリングする](../../aks/scale-cluster.md)」をご覧ください。

Azure Kubernetes Service にデプロイするときは、__ご利用のワークスペースに接続されている__ AKS クラスターにデプロイします。 AKS クラスターをワークスペースに接続するには、次の 2 つの方法があります。

* Azure Machine Learning service SDK、Machine Learning CLI、または Azure portal を使って、AKS クラスターを作成します。 このプロセスにより、クラスターがワークスペースに自動的に接続されます。
* 既存の AKS クラスターを Azure Machine Learning service のワークスペースにアタッチします。 Azure Machine Learning service SDK、Machine Learning CLI、または Azure portal を使って、クラスターをアタッチできます。

> [!IMPORTANT]
> 作成またはアタッチのプロセスは、1 回限りのタスクです。 AKS クラスターがワークスペースに接続されると、デプロイに使用できます。 不要になった AKS クラスターは、デタッチまたは削除できます。 デタッチまたは削除したクラスターには、デプロイできなくなります。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning service ワークスペースの作成](how-to-manage-workspace.md) に関する記事を参照してください。

- ワークスペースに登録されている機械学習モデル。 モデルが登録されていない場合は、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)、または [Azure Machine Learning Visual Studio Code 拡張機能](how-to-vscode-tools.md)。

- この記事の __Python__ コード スニペットは、次の変数が設定されていることを前提としています。

    * `ws` - 使用しているワークスペースに設定されている。
    * `model` - 登録済みのモデルに設定されている。
    * `inference_config` - モデルの推論構成に設定されている。

    これらの変数の設定について詳しくは、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- この記事の __CLI__ スニペットは、`inferenceconfig.json` ドキュメントを作成済みであることを前提としています。 このドキュメントの作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

## <a name="create-a-new-aks-cluster"></a>新しい AKS クラスターを作成する

**推定所要時間**: 約 20 分です。

AKS クラスターの作成またはアタッチは、お使いのワークスペースでの 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。 複数の AKS クラスターをワークスペースに接続できます。

> [!TIP]
> Azure Virtual Network を使用して AKS クラスターをセキュリティで保護する場合は、まず仮想ネットワークを作成する必要があります。 詳細については、[Azure Virtual Network での実験と推論の安全な実行](how-to-enable-virtual-network.md#aksvnet)に関するページを参照してください。

運用のためではなく__開発__、__検証__、__テスト__のための AKS クラスターを作成する場合は、__クラスターの目的__として__開発テスト__を指定できます。

> [!WARNING]
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定した場合、作成されるクラスターは、運用レベルのトラフィックには適しておらず、推論時間が増えることがあります。 開発/テスト クラスターでは、フォールト トレランスも保証されません。 開発/テスト クラスターには、少なくとも 2 つの仮想 CPU を使用することをお勧めします。

次の例では、SDK と CLI を使って新しい AKS クラスターを作成する方法を示します。

**SDK を使用する**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) の場合、`agent_count` と `vm_size` のカスタム値を選択し、`cluster_purpose` が `DEV_TEST` ではない場合は、`agent_count` と `vm_size` の積が 12 仮想 CPU 以上である必要があります。 たとえば、`vm_size` に "Standard_D3_v2" (4 仮想 CPU) を使用する場合、`agent_count` は 3 以上にする必要があります。
>
> Azure Machine Learning SDK では、AKS クラスターのスケーリングのサポートは提供されません。 クラスター内のノードをスケーリングするには、Azure portal 内でご自分の AKS クラスターの UI を使用します。 クラスターの VM サイズではなく、ノード数のみを変更できます。

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**CLI を使用する**

```azurecli
az ml computetarget create aks -n myaks
```

詳細については、[az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) に関するリファレンスをご覧ください。

## <a name="attach-an-existing-aks-cluster"></a>既存の AKS クラスターをアタッチする

**推定所要時間:** 約 5 分です。

お客様の Azure サブスクリプションに既に AKS クラスターがあり、そのバージョンが 1.12.## である場合は、それを使用してお客様のイメージをデプロイできます。

> [!TIP]
> 既存の AKS クラスターは、Azure Machine Learning service ワークスペースと異なる Azure リージョンに存在してもかまいません。
>
> Azure Virtual Network を使用して AKS クラスターをセキュリティで保護する場合は、まず仮想ネットワークを作成する必要があります。 詳細については、[Azure Virtual Network での実験と推論の安全な実行](how-to-enable-virtual-network.md#aksvnet)に関するページを参照してください。

> [!WARNING]
> AKS クラスターをワークスペースに接続するときに、`cluster_purpose` パラメーターを設定することにより、クラスターを使用する方法を定義できます。
>
> `cluster_purpose` パラメーターを設定しない場合、または `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` を設定した場合は、クラスターでは最低 12 の仮想 CPU が利用できる必要があります。
>
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定した場合、クラスターには 12 の仮想 CPU は必要ありません。 開発/テストには、少なくとも 2 つの仮想 CPU を使用することをお勧めします。 ただし、開発/テスト用に構成されたクラスターは、運用レベルのトラフィックには適しておらず、推論時間が増えることがあります。 開発/テスト クラスターでは、フォールト トレランスも保証されません。

Azure CLI または portal を使用した AKS クラスターの作成の詳細については、次の記事をご覧ください。

* [AKS クラスターの作成 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS クラスターの作成 (ポータル)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

次の例では、既存の AKS 1.12.## クラスターをワークスペースにアタッチする方法を示します。

**SDK を使用する**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**CLI を使用する**

CLI を使って既存のクラスターをアタッチするには、既存のクラスターのリソース ID を取得する必要があります。 この値を取得するには、次のコマンドを使います。 `myexistingcluster` は、AKS クラスターの名前に置き換えます。 `myresourcegroup` は、クラスターが含まれているリソース グループに置き換えます。

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

このコマンドにより、次のテキストのような値が返されます。

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

既存のクラスターをワークスペースにアタッチするには、次のコマンドを使います。 `aksresourceid` は、前のコマンドで返された値に置き換えます。 `myresourcegroup` は、ワークスペースが含まれているリソース グループに置き換えます。 `myworkspace` は、ワークスペースの名前に置き換えます。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

詳細については、[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) に関するリファレンスをご覧ください。

## <a name="deploy-to-aks"></a>AKS にデプロイする

Azure Kubernetes Service にモデルをデプロイするには、必要なコンピューティング リソースを記述した__デプロイ構成__を作成します。 たとえば、コアの数やメモリなどです。 また、モデルと Web サービスのホストに必要な環境を記述した__推論構成__も必要です。 推論構成の作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

### <a name="using-the-sdk"></a>SDK を使用する

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI の使用

CLI を使用してデプロイするには、次のコマンドを使用します。 AKS コンピューティング先の名前に `myaks` を置き換えます。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。 

### <a name="using-vs-code"></a>VS コードを使用する

VS Code の使用については、「[モデルを展開して管理する](how-to-vscode-tools.md#deploy-and-manage-models)」を参照してください。

> [!IMPORTANT] 
> VS Code を使ってデプロイするには、事前にワークスペースに AKS クラスターを作成するかアタッチしておく必要があります。

## <a name="web-service-authentication"></a>Web サービス認証

Azure Kubernetes Service にデプロイする場合、__キーベース__の認証は既定で有効になります。 __トークン__認証を有効にすることもできます。 トークン認証では、クライアントが Azure Active Directory アカウントを使用して認証トークンを要求する必要があります。これは、展開されたサービスへの要求を行うために使用されます。

認証を__無効__にするには、デプロイ構成の作成時に `auth_enabled=False` パラメーターを設定します。 次の例では、SDK を使用して認証を無効にします。

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

クライアント アプリケーションからの認証の詳細については、「[Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)」を参照してください。

### <a name="authentication-with-keys"></a>キーによる認証

キー認証が有効になっている場合は、`get_keys` メソッドを使用して、プライマリおよびセカンダリ認証キーを取得できます。

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> キーを再生成する必要がある場合は、[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) を使用します

### <a name="authentication-with-tokens"></a>トークンによる認証

トークン認証を有効にするには、デプロイの作成時や更新時に `token_auth_enabled=True` パラメーターを設定します。 次の例では、SDK を使用してトークン認証を有効にします。

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JWT トークンとそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その場合、ワークスペースのリージョンが利用可能な状態に戻るまで、事実上、Azure AD Authentication が利用できない状態となります。 また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="update-the-web-service"></a>Web サービスを更新する

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>次の手順

* [仮想ネットワーク内で実験と推論を安全に実行する](how-to-enable-virtual-network.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
