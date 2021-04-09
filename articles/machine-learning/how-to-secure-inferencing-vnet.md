---
title: 仮想ネットワークを使用して推論環境をセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning 推論環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 1a1a9158c06a12caaeb5702f2fdf7da3c801c143
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573440"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning 推論環境をセキュリティで保護する

この記事では、Azure Machine Learning で仮想ネットワークを使用して、推論環境をセキュリティで保護する方法について説明します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートからなるシリーズのパート 4 です。 まずは[パート 1:VNet の概要](how-to-network-security-overview.md)に関するページを読んで、アーキテクチャ全体を理解することを強くお勧めします。 

このシリーズの他の記事は次のとおりです。

[1.VNet の概要](how-to-network-security-overview.md) > [ワークスペースをセキュリティで保護する](how-to-secure-workspace-vnet.md) > [3.トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md) > **4.推論環境をセキュリティで保護する** > [5.Studio の機能を有効にする](how-to-enable-studio-virtual-network.md)

この記事では、仮想ネットワークで次の推論リソースをセキュリティで保護する方法について説明します。
> [!div class="checklist"]
> - 既定の Azure Kubernetes Service (AKS) クラスター
> - プライベート AKS クラスター
> - プライベート リンクを使用する AKS クラスター
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

仮想ネットワーク内で AKS クラスターを使用するには、次のネットワーク要件を満たす必要があります。

> [!div class="checklist"]
> * [Azure Kubernetes サービス (AKS) における高度なネットワークの構成](../aks/configure-azure-cni.md#prerequisites)に記載されている前提条件に従っている必要があります。
> * AKS インスタンスと仮想ネットワークは同じリージョンに存在する必要があります。 仮想ネットワークでワークスペースによって使用される Azure Storage アカウントをセキュリティで保護する場合、それらもまた AKS インスタンスと同じ仮想ネットワークに存在する必要があります。

仮想ネットワーク内の AKS をワークスペースに追加するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。

1. 左側にある __[Compute]__ を選択します。

1. 中央から __[推論クラスター]__ を選択し、 __[+]__ を選択します。

1. __[New Inference Cluster]\(新しい推論クラスター\)__ ダイアログで、 __[ネットワーク構成]__ の下の __[詳細]__ を選択します。

1. このコンピューティング リソースを仮想ネットワークを使用するように構成するには、次の操作を実行します。

    1. __[リソース グループ]__ ボックスの一覧で、目的の仮想ネットワークが含まれているリソース グループを選択します。
    1. __[仮想ネットワーク]__ ボックスの一覧で、目的のサブネットが含まれている仮想ネットワークを選択します。
    1. __[サブネット]__ ボックスの一覧で、サブネットを選択します。
    1. __[Kubernetes サービスのアドレス範囲]__ ボックスに、Kubernetes サービスのアドレス範囲を入力します。 このアドレス範囲は、Classless Inter-Domain Routing (CIDR) 表記の IP 範囲を使用して、クラスターで使用できる IP アドレスを定義します。 どのサブネットの IP 範囲とも重複していてはなりません (例: 10.0.0.0/16)。
    1. __[Kubernetes DNS サービスの IP アドレス]__ ボックスに、Kubernetes DNS サービスの IP アドレスを入力します。 この IP アドレスは Kubernetes DNS サービスに割り当てられます。 Kubernetes サービスのアドレス範囲内に含まれるアドレスを指定する必要があります (例: 10.0.0.10)。
    1. __[Docker ブリッジ アドレス]__ ボックスに、Docker ブリッジ アドレスを入力します。 この IP アドレスは Docker ブリッジに割り当てられます。 サブネットの IP 範囲または Kubernetes サービスのアドレス範囲内にすることはできません (例: 172.17.0.1/16)。

   ![Azure Machine Learning:Machine Learning コンピューティングの仮想ネットワークの設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Web サービスとしてのモデルを AKS にデプロイすると、推論要求を処理するスコアリング エンドポイントが作成されます。 仮想ネットワークを制御する NSG グループに、スコアリング エンドポイントの IP アドレスに対して有効になっているインバウンド セキュリティ規則があることを確認します (仮想ネットワークの外部から呼び出す場合)。

    スコアリング エンドポイントの IP アドレスを確認するには、デプロイされたサービスのスコアリング URI を確認します。 スコアリング URI の表示の詳細については、[Web サービスとしてデプロイされたモデルを使用する](how-to-consume-web-service.md#connection-information)ことに関する記事をご覧ください。

   > [!IMPORTANT]
   > NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#default-security-rules)」の既定のセキュリティ規則をご覧ください。

   [![インバウンド セキュリティ規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > スコアリング エンドポイントのイメージに表示される IP アドレスは、デプロイによって異なります。 1 つの AKS クラスターに対して同じ IP がすべてのデプロイで共有されますが、各 AKS クラスターには異なる IP アドレスが割り当てられます。

また、Azure Machine Learning SDK を使用して仮想ネットワークに Azure Kubernetes Service を追加することもできます。 仮想ネットワークに既に AKS クラスターがある場合は、[AKS にデプロイする方法](how-to-deploy-and-where.md)に関するページで説明されているように、ワークスペースにアタッチすることができます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい AKS インスタンスが作成されます。

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

作成プロセスが完了すると、仮想ネットワークの背後にある AKS クラスターで推論 (モデルのスコアリング) を実行できるようになります。 詳細については、[AKS へのデプロイ方法](how-to-deploy-and-where.md)に関するページをご覧ください。

Kubernetes でロールベースのアクセス制御を使用する方法の詳細については、「[Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)」を参照してください。

## <a name="network-contributor-role"></a>ネットワーク共同作成者ロール

> [!IMPORTANT]
> 前に作成した仮想ネットワークを提供して AKS クラスターを作成またはアタッチする場合は、AKS クラスターのサービス プリンシパル (SP) またはマネージド ID に、仮想ネットワークを含むリソース グループに対する _ネットワーク共同作成者_ ロールを付与する必要があります。
>
> ネットワーク共同作成者として ID を追加するには、次の手順に従います。

1. AKS のサービス プリンシパルまたはマネージド ID を検索するには、次の Azure CLI コマンドを使用します。 `<aks-cluster-name>` をクラスターの名前に置き換えます。 `<resource-group-name>` を、_AKS クラスターが含まれている_ リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    このコマンドによって `msi` の値が返された場合は、次のコマンドを使用して、マネージド ID のプリンシパル ID を識別します。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 仮想ネットワークが含まれているリソース グループの ID を検索するには、次のコマンドを使用します。 `<resource-group-name>` を、_仮想ネットワークが含まれている_ リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. ネットワーク共同作成者としてサービス プリンシパルまたはマネージド ID を追加するには、次のコマンドを使用します。 `<SP-or-managed-identity>` を、サービス プリンシパルまたはマネージド ID 用に返された ID に置き換えます。 `<resource-group-id>` を、仮想ネットワークが含まれているリソース グループ用に返された ID に置き換えます。

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS での内部ロードバランサーの使用の詳細については、「[Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する](../aks/internal-lb.md)」を参照してください。

## <a name="secure-vnet-traffic"></a>VNet トラフィックをセキュリティ保護する

AKS クラスターと仮想ネットワークの間のトラフィックを分離するには、次の 2 つの方法があります。

* __プライベート AKS クラスター__: この方法では、Azure Private Link を使用して、デプロイまたは管理操作用のクラスターとの通信をセキュリティで保護します。
* __内部 AKS ロード バランサー__: この方法では、AKS へのデプロイのエンドポイントが、仮想ネットワーク内でプライベート IP を使用するように構成します。

> [!WARNING]
> 内部ロード バランサーは、kubenet を使用する AKS クラスターでは機能しません。 内部ロード バランサーとプライベート AKS クラスターを同時に使用する場合は、Azure Container Networking Interface (CNI) を使用してプライベート AKS クラスターを構成します。 詳細については、「[Azure Kubernetes Service で Azure CNI ネットワークを構成する](../aks/configure-azure-cni.md)」を参照してください。

### <a name="private-aks-cluster"></a>プライベート AKS クラスター

既定では AKS クラスターには、パブリック IP アドレスを持つコントロール プレーンまたは API サーバーがあります。 プライベート AKS クラスターを作成することによって、プライベート コントロール プレーンを使用するように AKS を構成できます。 詳細については、「[プライベート Azure Kubernetes Service クラスターを作成する](../aks/private-clusters.md)」を参照してください。

プライベート AKS クラスターを作成したら、Azure Machine Learning で使用する[仮想ネットワークにクラスターをアタッチします](how-to-create-attach-kubernetes.md)。

> [!IMPORTANT]
> プライベート リンクが有効な AKS クラスターを Azure Machine Learning と共に使用する前に、この機能を有効にするためにサポート インシデントを開始する必要があります。 詳細については、[クォータの管理と増加](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)に関するページを参照してください。

### <a name="internal-aks-load-balancer"></a>内部 AKS ロード バランサー

既定では、AKS のデプロイでは[パブリック ロード バランサー](../aks/load-balancer-standard.md)が使用されます。 このセクションでは、内部ロード バランサーを使用するように AKS を構成する方法について説明します。 内部 (プライベート) ロード バランサーは、プライベート IP のみがフロントエンドとして許可される場合に使用されます。 内部ロード バランサーは、仮想ネットワーク内でトラフィックを負荷分散させるために使用されます

プライベート ロード バランサーを有効にするには、"_内部ロード バランサー_" を使用するように AKS を構成します。 

#### <a name="enable-private-load-balancer"></a>プライベート ロード バランサーを有効にする

> [!IMPORTANT]
> Azure Machine Learning スタジオで Azure Kubernetes Service クラスターを作成しているときに、プライベート IP を有効にすることはできません。 Python SDK を使用する場合、または機械学習用の Azure CLI 拡張機能を使用する場合は、内部ロード バランサーを使用して作成できます。

次の例では、SDK と CLI を使用して、__プライベート IP または内部ロード バランサーで新しい AKS クラスターを作成する__ 方法を示します。

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> CLI を使用して作成できるのは、内部ロード バランサーを使用する AKS クラスターのみです。 内部ロード バランサーを使用するように既存のクラスターをアップグレードする az ml コマンドはありません。

詳細については、[az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) に関するリファレンスを参照してください。

---

ワークスペースに __既存のクラスターを接続する__ 場合は、アタッチ操作が完了するまで待ってから、ロード バランサーを構成する必要があります。 クラスターのアタッチの詳細については、「[既存の AKS クラスターをアタッチする](how-to-create-attach-kubernetes.md)」を参照してください。

既存のクラスターをアタッチした後、内部ロード バランサーまたはプライベート IP を使用するようにクラスターを更新できます。

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances (ACI) を有効にする

Azure Container Instances は、モデルのデプロイ時に動的に作成されます。 Azure Machine Learning で仮想ネットワーク内に ACI を作成できるようにするには、デプロイで使用されるサブネットに対して __サブネットの委任__ を有効にする必要があります。

> [!WARNING]
> 仮想ネットワークで Azure Container Instances を使用する場合、仮想ネットワークには次のことが必要です。
> * Azure Machine Learning ワークスペースと同じリソース グループ内にあること。
> * ワークスペースに __プライベート エンドポイント__ がある場合、Azure Container Instances に使用される仮想ネットワークは、ワークスペースのプライベート エンドポイントで使用されているものと同じである必要があります。
>
> 仮想ネットワーク内で Azure Container Instances を使用する場合、ご使用のワークスペースの Azure Container Registry (ACR) をその仮想ネットワーク内に配置することはできません。

ワークスペースに対する仮想ネットワークで ACI を使用するには、次の手順のようにします。

1. 仮想ネットワークでサブネットの委任を有効にするには、「[サブネットの委任を追加または削除する](../virtual-network/manage-subnet-delegation.md)」の記事に書かれている情報を使用します。 仮想ネットワークを作成するときに委任を有効にすることも、既存のネットワークに委任を追加することもできます。

    > [!IMPORTANT]
    > 委任を有効にする場合は、 __[サブネットをサービスに委任]__ の値として `Microsoft.ContainerInstance/containerGroups` を使用します。

2. [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) を使用してモデルをデプロイします。`vnet_name` パラメーターと `subnet_name` パラメーターを使用します。 これらのパラメーターには、委任を有効にした仮想ネットワークの名前とサブネットを設定します。

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a> 仮想ネットワークからのアウトバウンド接続を制限する

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限する場合は、Azure Container Registry へのアクセスを許可する必要があります。 たとえば、ネットワーク セキュリティ グループ (NSG) に __AzureContainerRegistry.RegionName__ サービス タグへのアクセスを許可する規則が含まれていることを確認してください。ここで、`{RegionName} は Azure リージョンの名前です。

## <a name="next-steps"></a>次のステップ

この記事は、全 5 パートからなる仮想ネットワーク シリーズのパート 4 です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 1: 仮想ネットワークの概要](how-to-network-security-overview.md)
* [パート 2: ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [パート 3: トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [パート 5: スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)

名前の解決については、[カスタム DNS](how-to-custom-dns.md) の使用に関する記事も参照してください。