---
title: Azure Kubernetes Service を作成してアタッチする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して新しい Azure Kubernetes Service クラスターを作成する方法、または既存の AKS クラスターをワークスペースに接続する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.openlocfilehash: bc8f7aa6827ce251799acd0673d43344c0833c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149326"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成してアタッチする

Azure Machine Learning では、トレーニング済みの機械学習モデルを Azure Kubernetes Service にデプロイできます。 ただし、最初に Azure ML ワークスペースから Azure Kubernetes Service (AKS) クラスターを __作成__ するか、既存の AKS クラスターを __アタッチ__ する必要があります。 この記事では、クラスターの作成とアタッチの両方について説明します。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

- Azure ML ワークスペースと AKS クラスター間の通信をセキュリティで保護するために Azure Virtual Network の使用を計画している場合は、[トレーニング中や推論中のネットワークの分離](./how-to-network-security-overview.md)に関する記事をご覧ください。

## <a name="limitations"></a>制限事項

- Basic Load Balancer (BLB) ではなく **Standard Load Balancer (SLB)** をクラスターにデプロイする必要がある場合は、AKS ポータル、CLI、または SDK でクラスターを作成し、AML ワークスペースに **アタッチ** してください。

- パブリック IP アドレスの作成を制限する Azure Policy がある場合、AKS クラスターの作成は失敗します。 AKS では、[エグレス トラフィック](../aks/limit-egress-traffic.md)にパブリック IP が必要です。 エグレス トラフィックに関する記事では、いくつかの完全修飾ドメイン名を除き、パブリック IP を介したクラスターからのエグレス トラフィックをロックダウンする方法についても説明されています。 パブリック IP を有効にするには、次の 2 つの方法があります。
    - クラスターで、BLB または SLB で既定で作成されるパブリック IP を使用する
    - クラスターをパブリック IP なしで作成し、パブリック IP をユーザ定義のルートを使用したファイアウォールで設定する。 詳細については、「[ユーザー定義ルートを使用してクラスターのエグレスをカスタマイズする](../aks/egress-outboundtype.md)」を参照してください。
    
    AML コントロール プレーンからこのパブリック IP への通信はありません。 これはデプロイのため、AKS コントロール プレーンと通信します。 

- [API サーバーへのアクセスが有効な承認済みの IP 範囲](../aks/api-server-authorized-ip-ranges.md)を持つ AKS クラスターを **アタッチ** する場合は、AKS クラスターの AML コントロール プレーンの IP 範囲を有効にします。 AML コントロール プレーンは、ペアになっているリージョンにまたがってデプロイされ、AKS クラスター上に推論ポッドをデプロイします。 API サーバーにアクセスできない場合、推論ポッドをデプロイすることはできません。 AKS クラスターで IP 範囲を有効にする場合、[ペアになっているリージョン](../best-practices-availability-paired-regions.md)の両方に対して [IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=56519)を使用します。

    承認済みの IP 範囲は、Standard Load Balancer でのみ機能します。

- AKS クラスターを **アタッチする** 場合、これは Azure Machine Learning ワークスペースと同じ Azure サブスクリプションに含まれている必要があります。

- (Azure Private Link を使用して) プライベート AKS クラスターを使用する場合は、最初にクラスターを作成してから、ワークスペースにそれを **アタッチ** する必要があります。 詳細については、「[プライベート Azure Kubernetes Service クラスターを作成する](../aks/private-clusters.md)」を参照してください。

- AKS クラスターのコンピューティング名は、Azure ML ワークスペース内で一意である必要があります。
    - 名前は必須であり、3 文字から 24 文字の長さにする必要があります。
    - 有効な文字は、大文字、小文字、数字、- 文字です。
    - 名前の先頭は文字である必要があります。
    - 名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます。
   
 - **GPU** ノードまたは **FPGA** ノード (または特定の SKU) にモデルをデプロイする場合は、特定の SKU でクラスターを作成する必要があります。 既存のクラスターにセカンダリ ノード プールを作成し、そのセカンダリ ノード プールにモデルをデプロイすることはサポートされていません。
 
- クラスターを作成またはアタッチするときに、__開発テスト__ または __運用__ のためにクラスターを作成するかどうかを選択できます。 運用のためではなく、__開発__、__検証__、__テスト__ のための AKS クラスターを作成する場合は、__クラスターの目的__ を __開発テスト__ に設定します。 クラスターの目的を指定しない場合は、__運用__ クラスターが作成されます。 

    > [!IMPORTANT]
    > __開発テスト__ クラスターは、運用レベルのトラフィックに適していないため、推論時間が長くなる可能性があります。 開発/テスト クラスターでは、フォールト トレランスも保証されません。

- クラスターを __運用__ に使用する場合は、クラスターを作成またはアタッチするときに、少なくとも 12 個の __仮想 CPU__ が含まれている必要があります。 仮想 CPU の数は、クラスター内の __ノード数__ に、選択した VM サイズで提供される __コア数__ を掛けることで計算できます。 たとえば、4 個の仮想コアを持つ "Standard_D3_v2" の VM サイズを使用する場合は、ノードの数として 3 以上を選択する必要があります。

    __開発テスト__ クラスターの場合は、少なくとも 2 つの仮想 CPU を使用することをお勧めします。

- Azure Machine Learning SDK では、AKS クラスターのスケーリングのサポートは提供されません。 クラスター内のノードをスケーリングするには、Azure Machine Learning Studio 内でご自分の AKS クラスターの UI を使用します。 クラスターの VM サイズではなく、ノード数のみを変更できます。 AKS クラスターにおけるノードのスケーリングの詳細については、次の記事を参照してください。

    - [AKS クラスターでノードの数を手動でスケールする](../aks/scale-cluster.md)
    - [AKS でクラスター オートスケーラーを設定する](../aks/cluster-autoscaler.md)

- __YAML 構成を使用してクラスターを直接更新しないでください__。 Azure Kubernetes Services では YAML 構成による更新がサポートされていますが、Azure Machine Learning のデプロイでは変更が上書きされます。 上書きされない YAML フィールドは、"__要求の制限__" と "__CPU とメモリ__" の 2 つだけです。

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service のバージョン

Azure Kubernetes Service では、さまざまな Kubernetes バージョンを使用してクラスターを作成できます。 使用可能なバージョンの詳細については、「[Azure Kubernetes Service (AKS) でサポートされている Kubernetes のバージョン](../aks/supported-kubernetes-versions.md)」を参照してください。

次のいずれかの方法を使用して Azure Kubernetes Service クラスターを **作成する** 場合、作成されるクラスターの"*バージョンを選択することはできません*"。

* Azure Machine Learning Studio、または Azure portal の Azure Machine Learning セクション。
* Azure CLI 向けの Machine Learning 拡張機能。
* Azure Machine Learning SDK。

これらの方法で AKS クラスターを作成するには、クラスターの __既定の__ バージョンを使用します。 Kubernetes の新しいバージョンが利用可能になると、"*既定のバージョンは時間とともに変化します*"。

既存の AKS クラスターを **アタッチする** 場合、現在サポートされているすべての AKS バージョンがサポートされています。

> [!NOTE]
> サポートが終了した古いクラスターがあるエッジ ケースがある場合があります。 この場合、アタッチ操作を行うとエラーが返され、現在サポートされているバージョンが一覧表示されます。
>
> **プレビュー** バージョンをアタッチできます。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 プレビュー バージョンの使用のサポートは制限される場合があります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

### <a name="available-and-default-versions"></a>使用可能なバージョンと既定のバージョン

AKS の使用可能なバージョンと既定のバージョンを確認するには、[Azure CLI](/cli/azure/install-azure-cli) コマンド [az aks get-versions](/cli/azure/aks#az_aks_get_versions) を使用します。 たとえば、次のコマンドは、米国西部リージョンで使用可能なバージョンを返します。

```azurecli-interactive
az aks get-versions -l westus -o table
```

このコマンドの出力は次のテキストのようになります。

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Azure Machine Learning でクラスターを **作成する** ときに使用される既定のバージョンを確認するには、`--query` パラメーターを使用して既定のバージョンを選択します。

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

このコマンドの出力は次のテキストのようになります。

```text
Result
--------
1.16.13
```

**利用可能なバージョンをプログラムで確認する** には、[Container Service Client - List Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) REST API を使用します。 使用可能なバージョンを見つけるには、`orchestratorType` が `Kubernetes` になっているエントリを確認します。 関連付けられている `orchestrationVersion` エントリには、ワークスペースに **アタッチ** できるバージョンが含まれています。

Azure Machine Learning を使用してクラスターを **作成** するときに使用される既定のバージョンを見つけるには、`orchestratorType` が `Kubernetes`、`default` が `true` になっているエントリを見つけます。 関連付けられている `orchestratorVersion` 値が既定のバージョンです。 次の JSON スニペットは、エントリの例を示しています。

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>新しい AKS クラスターを作成する

**推定所要時間**: 約 10 分。

AKS クラスターの作成またはアタッチは、お使いのワークスペースでの 1 回限りのプロセスです。 複数のデプロイでこのクラスターを再利用できます。 クラスターまたはそれを含むリソース グループを削除した場合、次回デプロイする必要があるときに、新しいクラスターを作成する必要があります。 複数の AKS クラスターをワークスペースに接続できます。

次の例では、SDK と CLI を使用して新しい AKS クラスターを作成する方法を示します。

# <a name="python"></a>[Python](#tab/python)

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

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

詳細については、[az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) に関するリファレンスを参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルで AKS クラスターを作成する方法の詳細については、[Azure Machine Learning Studio でのコンピューティング先の作成](how-to-create-attach-compute-studio.md#inference-clusters)に関するページを参照してください。

---

## <a name="attach-an-existing-aks-cluster"></a>既存の AKS クラスターをアタッチする

**推定所要時間:** 約 5 分です。

Azure サブスクリプションに AKS クラスターが既にある場合は、ワークスペースで使用できます。

> [!TIP]
> 既存の AKS クラスターは、Azure Machine Learning ワークスペースと異なる Azure リージョンに存在してもかまいません。


> [!WARNING]
> ワークスペースから同じ AKS クラスターに対して複数のアタッチメントを同時に作成することは避けてください。 たとえば、2 つの異なる名前を使用して 1 つの AKS クラスターをワークスペースにアタッチすることが該当します。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。
>
> TLS やその他のクラスター構成設定を変更するためなど、AKS クラスターを再度アタッチしたい場合は、[AksCompute.detach()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--) を使用して既存のアタッチメントを先に削除しておいてください。

Azure CLI または portal を使用した AKS クラスターの作成の詳細については、次の記事をご覧ください。

* [AKS クラスターの作成 (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az-aks-create)
* [AKS クラスターの作成 (ポータル)](../aks/kubernetes-walkthrough-portal.md)
* [AKS クラスターの作成 (Azure クイックスタート テンプレートでの ARM テンプレート)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

次の例では、既存の AKS クラスターをワークスペースにアタッチする方法を示します。

# <a name="python"></a>[Python](#tab/python)

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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

詳細については、[az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) に関するリファレンスをご覧ください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルで AKS クラスターをアタッチする方法の詳細については、[Azure Machine Learning Studio でのコンピューティング先の作成](how-to-create-attach-compute-studio.md#inference-clusters)に関するページを参照してください。

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>TLS 終端を使用した AKS クラスターの作成またはアタッチ
[AKS クラスターを作成またはアタッチする](how-to-create-attach-kubernetes.md)ときに、 **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** および **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** の構成オブジェクトを使用して TLS 終端を有効にすることができます。 どちらのメソッドからも、**enable_ssl** メソッドを持つ構成オブジェクトが返されます。また、**enable_ssl** メソッドを使用して TLS を有効にすることができます。

次の例は、内部で Microsoft 証明書を使用して、TLS 証明書の自動生成と構成で TLS 終端を有効にする方法を示しています。
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
次の例は、カスタム証明書とカスタム ドメイン名を使用して TLS 終端を有効にする方法を示しています。 カスタム ドメインと証明書を使用する場合は、スコアリング エンドポイントの IP アドレスを指すように DNS レコードを更新する必要があります。「[DNS を更新する](how-to-secure-web-service.md#update-your-dns)」を参照してください。

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> AKS クラスター上のモデルのデプロイをセキュリティで保護する方法の詳細については、「[TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)」を参照してください。

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>プライベート IP で内部ロード バランサーを使用する AKS クラスターの作成または接続
AKS クラスターを作成または接続するときに、内部ロード バランサーを使用するようにクラスターを構成できます。 内部ロード バランサーを使用すると、AKS へのデプロイに関するスコア付けエンドポイントには、仮想ネットワーク内でプライベート IP が使用されます。 次のコード スニペットは、AKS クラスターの内部ロード バランサーを構成する方法を示しています。
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning は、内部ロード バランサーによる TLS 終端をサポートしていません。 内部ロード バランサーにはプライベート IP があり、そのプライベート IP は別のネットワーク上にある可能性があるため、証明書が拒否される可能性があります。 

>[!NOTE]
> 推論環境をセキュリティで保護する方法の詳細については、[Azure Machine Learning 推論環境のセキュリティ保護](how-to-secure-inferencing-vnet.md)に関する記事を参照してください。

## <a name="detach-an-aks-cluster"></a>AKS クラスターをデタッチする

ワークスペースからクラスターをデタッチするには、次のいずれかの方法を使用します。

> [!WARNING]
> Azure Machine Learning スタジオ、SDK、または機械学習のための Azure CLI 拡張機能を使用して AKS クラスターをデタッチしても、**AKS クラスターは削除されません**。 クラスターを削除する方法については、[AKS と共に Azure CLI を使用する](../aks/kubernetes-walkthrough.md#delete-the-cluster)方法に関するページをご覧ください。

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

既存のクラスターをワークスペースからデタッチするには、次のコマンドを使います。 `myaks` を、ワークスペースにアタッチされている AKS クラスターの名前に置き換えます。 `myresourcegroup` は、ワークスペースが含まれているリソース グループに置き換えます。 `myworkspace` は、ワークスペースの名前に置き換えます。

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure Machine Learning スタジオで、 __[コンピューティング]__ 、 __[推論クラスター]__ 、削除するクラスターの順に選択します。 __[デタッチ]__ リンクを使用して、クラスターをデタッチします。

---

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="update-the-cluster"></a>クラスターの更新

Azure Kubernetes Service クラスターにインストールされている Azure Machine Learning コンポーネントの更新プログラムは、手動で適用する必要があります。 

これらの更新プログラムを適用するには、Azure Machine Learning ワークスペースからクラスターをデタッチしてから、クラスターをワークスペースに再アタッチします。 クラスターで TLS が有効な場合は、クラスターを再アタッチするときに TLS/SSL 証明書と秘密キーを指定する必要があります。 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

TLS/SSL 証明書と秘密キーがなくなった場合、または Azure Machine Learning によって生成された証明書を使用している場合は、`kubectl` を使用してクラスターに接続し、シークレット `azuremlfessl` を取得することで、クラスターをデタッチする前にファイルを取得できます。

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes では、base-64 でエンコードされた形式でシークレットが格納されます。 シークレットの `cert.pem` コンポーネントと `key.pem` コンポーネントを `attach_config.enable_ssl` に提供する前に、base-64 でデ コードする必要があります。 

### <a name="webservice-failures"></a>Webservice のエラー

AKS の多くの Webservice エラーは、`kubectl` を使用してクラスターに接続することでデバッグできます。 AKS クラスターの `kubeconfig.json` を取得するには、以下を実行します

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>次のステップ

* [Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)
* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)
* [Azure Kubernetes Service クラスターにモデルをデプロイする](how-to-deploy-azure-kubernetes-service.md)