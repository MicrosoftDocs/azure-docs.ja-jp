---
title: ML モデルを Kubernetes Service にデプロイする
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service を使用して Web サービスとして Azure Machine Learning のモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 6c85a7315fe05bb4fedabd176295523c2fa95d81
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855232"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにモデルをデプロイする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning を使って Azure Kubernetes Service (AKS) 上の Web サービスとしてモデルをデプロイする方法について説明します。 Azure Kubernetes Service は高スケールの運用デプロイに適してします。 次のいずれかの機能が必要な場合は、Azure Kubernetes Service を使用します。

- __高速の応答時間__。
- デプロイされたサービスの __自動スケール__。
- GPU や Field Programmable Gate Array (FPGA) などの __ハードウェア アクセラレーション__ オプション。

> [!IMPORTANT]
> クラスター スケーリングは、Azure Machine Learning SDK では提供されません。 AKS クラスターにおけるノードのスケーリングの詳細については、以下を参照してください 
- [AKS クラスターでノードの数を手動でスケールする](../aks/scale-cluster.md)
- [AKS でクラスター オートスケーラーを設定する](../aks/cluster-autoscaler.md)

Azure Kubernetes Service にデプロイするときは、__ご利用のワークスペースに接続されている__ AKS クラスターにデプロイします。 AKS クラスターをワークスペースに接続するには、次の 2 つの方法があります。

* Azure Machine Learning SDK、Machine Learning CLI、または [Azure Machine Learning Studio](https://ml.azure.com) を使って、AKS クラスターを作成します。 このプロセスにより、クラスターがワークスペースに自動的に接続されます。
* 既存の AKS クラスターを Azure Machine Learning のワークスペースにアタッチします。 Azure Machine Learning SDK、Machine Learning CLI、または Azure Machine Learning Studio を使って、クラスターをアタッチできます。

AKS クラスターと AML ワークスペースは異なるリソース グループに配置できます。

> [!IMPORTANT]
> 作成またはアタッチのプロセスは、1 回限りのタスクです。 AKS クラスターがワークスペースに接続されると、デプロイに使用できます。 不要になった AKS クラスターは、デタッチまたは削除できます。 デタッチまたは削除したクラスターには、デプロイできなくなります。

> [!IMPORTANT]
> Web サービスにデプロイする前にローカルでデバッグすることをお勧めします。 詳細については、「[ローカル デバッグ](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)」を参照してください。
>
> Azure Machine Learning の[ローカルの Notebook へのデプロイ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)に関する記事を参照することもできます

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- ワークスペースに登録されている機械学習モデル。 モデルが登録されていない場合は、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

- この記事の __Python__ コード スニペットは、次の変数が設定されていることを前提としています。

    * `ws` - 使用しているワークスペースに設定されている。
    * `model` - 登録済みのモデルに設定されている。
    * `inference_config` - モデルの推論構成に設定されている。

    これらの変数の設定について詳しくは、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- この記事の __CLI__ スニペットは、`inferenceconfig.json` ドキュメントを作成済みであることを前提としています。 このドキュメントの作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

- Basic Load Balancer (BLB) ではなく Standard Load Balancer (SLB) をクラスターにデプロイする必要がある場合は、AKS ポータル、CLI、または SDK でクラスターを作成し、AML ワークスペースにアタッチしてください。

- パブリック IP の作成を制限する Azure Policy がある場合、AKS クラスターの作成は失敗します。 AKS では、[エグレス トラフィック](https://docs.microsoft.com/azure/aks/limit-egress-traffic)にパブリック IP が必要です。 この記事では、いくつかの FQDN を除き、パブリック IP を介したクラスターからのエグレス トラフィックをロックダウンするためのガイダンスも提供します。 パブリック IP を有効にするには、次の 2 つの方法があります。
  - クラスターで、BLB または SLB で既定で作成されるパブリック IP を使用する
  - クラスターをパブリック IP なしで作成し、パブリック IP を[こちら](https://docs.microsoft.com/azure/aks/egress-outboundtype)に記載されているようにユーザ定義のルートを使用したファイアウォールで設定する 
  
  AML コントロール プレーンからこのパブリック IP への通信はありません。 これはデプロイのため、AKS コントロール プレーンと通信します。 

- [API サーバーへのアクセスが有効な承認済みの IP 範囲](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)を持つ AKS クラスターを接続する場合は、AKS クラスターの AML コントロール プレーンの IP 範囲を有効にします。 AML コントロール プレーンは、ペアになっているリージョンにまたがってデプロイされ、AKS クラスター上に推論ポッドをデプロイします。 API サーバーにアクセスできない場合、推論ポッドをデプロイすることはできません。 AKS クラスターで IP 範囲を有効にする場合、[ペアになっているリージョン]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions)の両方に対して [IP 範囲](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519)を使用します。


  承認済みの IP 範囲は、Standard Load Balancer でのみ機能します。
 
 - コンピューティング名はワークスペース内で一意にする必要があります
   - 名前は必須であり、3 文字から 24 文字の長さにする必要があります。
   - 有効な文字は、大文字、小文字、数字、- 文字です。
   - 名前の先頭は文字である必要があります
   - 名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます
   
 - GPU ノードまたは FPGA ノード (または特定の SKU) にモデルをデプロイする場合は、特定の SKU でクラスターを作成する必要があります。 既存のクラスターにセカンダリ ノード プールを作成し、そのセカンダリ ノード プールにモデルをデプロイすることはサポートされていません。

## <a name="create-a-new-aks-cluster"></a>新しい AKS クラスターを作成する

**推定所要時間**: 約 10 分。

AKS クラスターの作成またはアタッチは、お使いのワークスペースでの 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。 複数の AKS クラスターをワークスペースに接続できます。
 
Azure Machine Learning で、Private Link が有効な Azure Kubernetes サービスの使用がサポートされるようになりました。
プライベート AKS クラスターを作成するには、[こちら](https://docs.microsoft.com/azure/aks/private-clusters)のドキュメントに従ってください

> [!TIP]
> Azure Virtual Network を使用して AKS クラスターをセキュリティで保護する場合は、まず仮想ネットワークを作成する必要があります。 詳細については、[Azure Virtual Network での実験と推論の安全な実行](how-to-enable-virtual-network.md#aksvnet)に関するページを参照してください。

運用のためではなく __開発__、__検証__、__テスト__ のための AKS クラスターを作成する場合は、__クラスターの目的__ として __開発テスト__ を指定できます。

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
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

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
> Azure Machine Learning SDK では、AKS クラスターのスケーリングのサポートは提供されません。 クラスター内のノードをスケーリングするには、Azure Machine Learning Studio 内でご自分の AKS クラスターの UI を使用します。 クラスターの VM サイズではなく、ノード数のみを変更できます。

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**CLI を使用する**

```azurecli
az ml computetarget create aks -n myaks
```

詳細については、[az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) に関するリファレンスを参照してください。

## <a name="attach-an-existing-aks-cluster"></a>既存の AKS クラスターをアタッチする

**推定所要時間:** 約 5 分です。

Azure サブスクリプションに既に AKS クラスターがあり、そのバージョンが 1.17 以前である場合は、それを使用してイメージをデプロイできます。

> [!TIP]
> 既存の AKS クラスターは、Azure Machine Learning ワークスペースと異なる Azure リージョンに存在してもかまいません。
>
> Azure Virtual Network を使用して AKS クラスターをセキュリティで保護する場合は、まず仮想ネットワークを作成する必要があります。 詳細については、[Azure Virtual Network での実験と推論の安全な実行](how-to-enable-virtual-network.md#aksvnet)に関するページを参照してください。

AKS クラスターをワークスペースに接続するときに、`cluster_purpose` パラメーターを設定することにより、クラスターを使用する方法を定義できます。

`cluster_purpose` パラメーターを設定しない場合、または `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` を設定した場合は、クラスターでは最低 12 の仮想 CPU が利用できる必要があります。

`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` を設定した場合、クラスターには 12 の仮想 CPU は必要ありません。 開発/テストには、少なくとも 2 つの仮想 CPU を使用することをお勧めします。 ただし、開発/テスト用に構成されたクラスターは、運用レベルのトラフィックには適しておらず、推論時間が増えることがあります。 開発/テスト クラスターでは、フォールト トレランスも保証されません。

> [!WARNING]
> ワークスペースから同じ AKS クラスターに対して複数のアタッチメントを同時に作成することは避けてください。 たとえば、2 つの異なる名前を使用して 1 つの AKS クラスターをワークスペースにアタッチすることが該当します。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。
>
> TLS やその他のクラスター構成設定を変更するためなど、AKS クラスターを再度アタッチしたい場合は、[AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--) を使用して既存のアタッチメントを先に削除しておいてください。

Azure CLI または portal を使用した AKS クラスターの作成の詳細については、次の記事をご覧ください。

* [AKS クラスターの作成 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS クラスターの作成 (ポータル)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [AKS クラスターの作成 (Azure クイックスタート テンプレートでの ARM テンプレート)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

次の例では、既存の AKS クラスターをワークスペースにアタッチする方法を示します。

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
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

Azure Kubernetes Service にモデルをデプロイするには、必要なコンピューティング リソースを記述した __デプロイ構成__ を作成します。 たとえば、コアの数やメモリなどです。 また、モデルと Web サービスのホストに必要な環境を記述した __推論構成__ も必要です。 推論構成の作成の詳細については、「[Azure Machine Learning service を使用してモデルをデプロイする](how-to-deploy-and-where.md)」を参照してください。

> [!NOTE]
> デプロイされるモデルの数は、デプロイごとに 1,000 モデル (コンテナーごと) に制限されます。


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
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI の使用

CLI を使用してデプロイするには、次のコマンドを使用します。 AKS コンピューティング先の名前に `myaks` を置き換えます。 登録されているモデルの名前とバージョンに `mymodel:1` を置き換えます。 このサービスに付ける名前に `myservice` を置き換えます。

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

詳細については、[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) のリファレンスを参照してください。

### <a name="using-vs-code"></a>VS Code を使用する

VS Code の使用については、「[モデルを展開して管理する](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)」を参照してください。

> [!IMPORTANT]
> VS Code を使ってデプロイするには、事前にワークスペースに AKS クラスターを作成するかアタッチしておく必要があります。

### <a name="understand-the-deployment-processes"></a>デプロイ プロセスについて

"デプロイ" という用語は、Kubernetes と Azure Machine Learning の両方で使用されます。 これらの 2 つのコンテキストでは、"デプロイ"の意味が異なります。 Kubernetes では、`Deployment` は具体的なエンティティで、宣言型の YAML ファイルで指定されます。 Kubernetes の `Deployment` には、定義されたライフサイクルが定義されており、`Pods` や `ReplicaSets` などの他の Kubernetes エンティティと具体的な関係があります。 Kubernetes については、「[What is Kubernetes?](https://aka.ms/k8slearning)」 (Kubernetes とは) にあるドキュメントとビデオを参照してください。

Azure Machine Learning では、"デプロイ"は、「プロジェクト リソースを使用可能にする、およびクリーンアップする」というより一般的な意味で使用されます。 Azure Machine Learning でデプロイ部分が考慮される手順は、次のとおりです。

1. プロジェクト フォルダー内のファイルの解凍。.amlignore または .gitignore で指定されたファイルは無視されます
1. コンピューティング クラスターのスケール アップ (Kubernetes 関連)
1. Dockerfile のビルドまたは計算ノードへのダウンロード (Kubernetes 関連)
    1. システムにより、次のハッシュが計算されます。 
        - 基本イメージ 
        - カスタム Docker の手順 (「[カスタム Docker ベース イメージを使用してモデルをデプロイする](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)」を参照してください)
        - Conda 定義 YAML ([Azure Machine Learning でのソフトウェア環境の作成と使用](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)に関するページを参照してください)
    1. システムでは、ワークスペース Azure Container Registry (ACR) の検索で、このハッシュがキーとして使用されます
    1. 見つからない場合、グローバル ACR で一致するものが検索されます
    1. 見つからない場合、システムでは新しいイメージがビルドされます (これはキャッシュされ、ワークスペース ACR に登録されます)
1. 計算ノード上の一時記憶域への圧縮されたプロジェクト ファイルのダウンロード
1. プロジェクト ファイルの解凍
1. `python <entry script> <arguments>` を実行する計算ノード
1. ログ、モデル ファイル、`./outputs` に書き込まれたその他のファイルの、ワークスペースに関連付けられているストレージ アカウントへの保存
1. 一時記憶域の削除など、コンピューティングのスケールダウン (Kubernetes 関連)

AKS を使用している場合、コンピューティングのスケールアップおよびスケールダウンは、ビルドされた、または上記で見つかった Dockerfile を使用して、Kubernetes により制御されます。 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>制御されたロールアウト (プレビュー) を使用して AKS にモデルをデプロイする

エンドポイントを使用して、制御された方法でモデル バージョンの分析とレベル上げを行います。 1 つのエンドポイントの背後に最大 6 つのバージョンをデプロイできます。 エンドポイントには次の機能があります。

* __各エンドポイントに送信されるスコアリング トラフィックの割合__ を構成します。 たとえば、トラフィックの 20% をエンドポイント 'test' にルーティングし、80% を 'production' にルーティングします。

    > [!NOTE]
    > トラフィックの 100% を指定しない場合、残りの割合は __既定の__ エンドポイント バージョンにルーティングされます。 たとえば、エンドポイント バージョン 'test' がトラフィックの 10% を取得し、"prod" が 30% を取得するよう構成した場合、残りの 60% は既定のエンドポイント バージョンに送信されます。
    >
    > 作成された最初のエンドポイント バージョンが、既定値として自動的に構成されます。 これは、エンドポイント バージョンを作成または更新するときに `is_default=True` を設定することによって変更できます。
     
* エンドポイント バージョンに、__コントロール__ または __処理__ のいずれかのタグを付けます。 たとえば、現在の運用エンドポイントのバージョンがコントロールであっても、潜在的な新しいモデルは処理バージョンとしてデプロイされます。 処理バージョンのパフォーマンスを評価した後、現在のコントロールのパフォーマンスを上回る場合は、新しい運用/コントロールに昇格される可能性があります。

    > [!NOTE]
    > コントロールは __1 つ__ しか持つことができません。 処理は複数持つことができます。

App Insights を有効にすると、エンドポイントおよびデプロイされたバージョンの運用メトリックを表示できます。

### <a name="create-an-endpoint"></a>エンドポイントの作成
モデルをデプロイする準備ができたら、スコアリング エンドポイントを作成し、最初のバージョンをデプロイします。 次の例は、SDK を使用してエンドポイントをデプロイおよび作成する方法を示しています。 最初のデプロイは既定のバージョンとして定義されます。これは、すべてのバージョンの未指定のトラフィック パーセンタイルが既定のバージョンに設定されることを意味します。  

> [!TIP]
> 次の例では、構成によって最初のエンドポイント バージョンがトラフィックの 20% を処理するように設定されます。 これは最初のエンドポイントであるため、既定のバージョンでもあります。 また、他の 80% のトラフィックについては他のバージョンがないため、同様に既定にルーティングされます。 トラフィックの一定の割合を受け取る他のバージョンがデプロイされるまでは、これが実質的にトラフィックの 100% を受け取ります。

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>バージョンの更新とエンドポイントへの追加

エンドポイントに別のバージョンを追加し、スコアリング トラフィック パーセンタイルをバージョンに合わせて構成します。 バージョンには、コントロールと処理バージョンの 2 種類があります。 1 つのコントロール バージョンと比較するために、複数の処理バージョンを使用できます。

> [!TIP]
> 次のコード スニペットで作成される 2 番目のバージョンは、トラフィックの 10% を受け入れます。 最初のバージョンは 20% に構成されているため、トラフィックの 30% だけが特定のバージョンに対して構成されます。 残りの 70% は最初のエンドポイント バージョンに送信されます。これは既定のバージョンでもあるためです。

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

既存のバージョンを更新するか、エンドポイント内で削除します。 バージョンの既定の種類、コントロールの種類、およびトラフィック パーセンタイルを変更できます。 次の例では、2 番目のバージョンがトラフィックを 40% に増やし、これが既定になります。

> [!TIP]
> 次のコード スニペットの後は、2 番目のバージョンが既定になります。 現在は 40% に構成されていますが、元のバージョンは引き続き 20% に構成されています。 これは、トラフィックの 40% がバージョン構成によって考慮されないことを意味します。 残りのトラフィックは 2 番目のバージョンにルーティングされます。これが既定になったためです。 実質的にトラフィックの 80% を受け取ります。

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Web サービス認証

Azure Kubernetes Service にデプロイする場合、__キーベース__ の認証は既定で有効になります。 __トークン ベース__ の認証を有効にすることもできます。 トークン ベースの認証では、クライアントが Azure Active Directory アカウントを使用して認証トークンを要求する必要があります。これは、展開されたサービスへの要求を行うために使用されます。

認証を __無効__ にするには、デプロイ構成の作成時に `auth_enabled=False` パラメーターを設定します。 次の例では、SDK を使用して認証を無効にします。

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
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その場合、ワークスペースのリージョンが利用可能な状態に戻るまで、事実上、トークン ベースの認証が利用できない状態となります。 また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。
>
> トークンを取得するには、Azure Machine Learning SDK または [az ml service get-access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) コマンドを使用する必要があります。

## <a name="next-steps"></a>次のステップ

* [仮想ネットワーク内で実験と推論を安全に実行する](how-to-enable-virtual-network.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [デプロイ トラブルシューティング](how-to-troubleshoot-deployment.md)
* [Web サービスを更新する](how-to-deploy-update-web-service.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
