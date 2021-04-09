---
title: Azure Kubernetes Service (AKS) での可用性ゾーンの使用
description: Azure Kubernetes Service (AKS) で複数の可用性ゾーンにノードを分散させるクラスターを作成する方法について説明します。
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4c5b0ceb3f8e0b96f18a67ed0c7dbf1b56ac30da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583549"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する

Azure Kubernetes Service (AKS) クラスターは、ノードやストレージなどのリソースを、基になる Azure インフラストラクチャの複数の論理セクションに分散させます。 このデプロイ モデルでは、可用性ゾーンを使用する場合、特定の可用性ゾーン内のノードが、別の可用性ゾーンで定義されているノードと物理的に分離されていることを確認します。 クラスター全体に構成された複数の可用性ゾーンでデプロイされる AKS クラスターは、ハードウェア障害または計画メンテナンス イベントから保護するための高レベルの可用性を提供します。

クラスター内のノード プールを複数のゾーンにまたがるように定義することで、1 つのゾーンがダウンした場合でも、特定のノード プール内のノードは動作を継続できます。 1 つのデータセンターで物理的な障害が発生した場合でも、ノードのサブセットの障害に耐えられるよう調整できていれば、アプリケーションを引き続き利用できます。

この記事では、AKS クラスターを作成し、複数の可用性ゾーンにノード コンポーネントを分散させる方法について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="limitations-and-region-availability"></a>制限事項とリージョンの可用性

現在、AKS クラスターは、次のリージョンの可用性ゾーンを使用して作成できます。

* オーストラリア東部
* ブラジル南部
* カナダ中部
* 米国中部
* 米国東部 
* 米国東部 2
* フランス中部
* ドイツ中西部
* 東日本
* 北ヨーロッパ
* 東南アジア
* 米国中南部
* 英国南部
* US Gov バージニア州
* 西ヨーロッパ
* 米国西部 2

可用性ゾーンを使用して AKS クラスターを作成する場合、次の制限事項が適用されます。

* 可用性ゾーンは、クラスターまたはノード プールの作成時にのみ定義できます。
* 可用性ゾーンの設定は、クラスターの作成後は更新できません。 また、可用性ゾーンを使用するために、既存の非可用性ゾーン クラスターを更新することはできません。
* 選択したノード サイズ (VM SKU) を、選択したすべての可用性ゾーンで使用できる必要があります。
* 可用性ゾーンが有効になっているクラスターでは、複数のゾーンへの分散のために Azure Standard Load Balancer を使用する必要があります。 このロード バランサーの種類は、クラスターの作成時にのみ定義できます。 Standard Load Balancer の詳細と制限事項については、[Azure Load Balancer Standard SKU の制限事項][standard-lb-limitations]に関する記事をご覧ください。

### <a name="azure-disks-limitations"></a>Azure ディスクの制限事項

Azure マネージド ディスクを使用するボリュームは、現在、ゾーン冗長リソースではありません。 ボリュームをゾーン間で接続することはできず、ターゲット ポッドをホストする特定のノードと同じゾーンに併置する必要があります。

Kubernetes では、バージョン 1.12 以降で、Azure 可用性ゾーンが認識されています。 ユーザーは、複数ゾーンの AKS クラスターで Azure マネージド ディスクを参照して PersistentVolumeClaim オブジェクトをデプロイすることができます。また、適切な可用性ゾーン内にこの PVC を要求するあらゆるポッドの[スケジュール設定が Kubernetes によって管理](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones)されます。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS クラスターの可用性ゾーンの概要

可用性ゾーンとは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 ゾーンは、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するため、ゾーンが有効になっているすべてのリージョンに常に複数のゾーンがあります。 可用性ゾーンはリージョン内で物理的に分離されているため、データセンターで障害が発生してもアプリケーションとデータは保護されます。

詳しくは、「[Azure の可用性ゾーンの概要][az-overview]」をご覧ください。

可用性ゾーンを使用してデプロイされる AKS クラスターでは、1 つのリージョン内の複数のゾーンにノードを分散させることができます。 たとえば、 *米国東部 2*  リージョンのクラスターでは、*米国東部 2* の 3 つすべての可用性ゾーンでノードを作成できます。 この AKS クラスターリソースの分散により、特定のゾーンの障害に対する回復力があるため、クラスターの可用性が向上します。

![複数の可用性ゾーンへの AKS ノードの分散](media/availability-zones/aks-availability-zones.png)

1 つのゾーンが使用できなくなった場合でも、クラスターが複数のゾーンに分散していれば、アプリケーションは引き続き実行されます。

## <a name="create-an-aks-cluster-across-availability-zones"></a>複数の可用性ゾーンでの AKS クラスターの作成

[az aks create][az-aks-create] コマンドを使用してクラスターを作成する場合、`--zones` パラメーターで、エージェント ノードをデプロイする先のゾーンを定義します。 クラスターの作成時に `--zones` パラメーターを定義すると、etcd や API などのコントロール プレーン コンポーネントがリージョン内の使用可能なゾーンに分散されます。 コントロール プレーン コンポーネントが分散される特定のゾーンは、初期ノード プールに対して選択されている明示的なゾーンに依存しません。

AKS クラスターの作成時に既定のエージェント プールのゾーンを定義しなかった場合、コントロール プレーン コンポーネントが可用性ゾーン間で分散されることは保証されません。 [az aks nodepool add][az-aks-nodepool-add] コマンドを使用して別のノード プールを追加し、新しいノードに `--zones` を指定することはできますが、コントロール プレーンがゾーン間でどのように分散されているかは変わりません。 可用性ゾーンの設定は、クラスターまたはノード プールの作成時にのみ定義できます。

次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前の AKS クラスターを作成しています。 合計 *3* つのノードが作成されます (ゾーン *1* にエージェント 1 つ、ゾーン *2* にエージェント 1 つ、ゾーン *3* にエージェント 1 つ)。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

AKS クラスターの作成には数分かかります。

新しいノードが属するゾーンを決定するとき、指定された AKS node プールは、[基になる Azure Virtual Machine Scale Sets によって提供されるベスト エフォートのゾーン分散][vmss-zone-balancing]を使用します。 特定の AKS ノード プールが "バランスが取れている" ものと見なされるのは、スケール セットの各ゾーンの VM 数が同じであるか差が \-1 つ以内の場合です。

## <a name="verify-node-distribution-across-zones"></a>複数のゾーンへのノードの分散を確認する

クラスターの準備ができたら、スケール セット内のエージェント ノードを一覧表示して、それらがデプロイされている可用性ゾーンを確認します。

最初に、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

次に、[kubectl describe][kubectl-describe] コマンドを使用して、クラスター内のノードを一覧表示し、*failure-domain.beta.kubernetes.io/zone* 値でフィルター処理します。 Bash シェルの例を次に示します。

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

次の出力例では、指定のリージョンの複数の可用性ゾーンに分散した 3 つのノードが示されています (1 番目の可用性ゾーンは *eastus2-1*、2 番目の可用性ゾーンは *eastus2-2* など)。

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

エージェント プールにさらにノードを追加すると、Azure Platform は、基になる VM を指定の複数の可用性ゾーンに自動的に分散させます。

新しい Kubernetes バージョン (1.17.0 以降以降) では、AKS では非推奨の `failure-domain.beta.kubernetes.io/zone` に加えて新しいラベル `topology.kubernetes.io/zone` が使用されていることに注意してください。 次のスクリプトを実行すると、上記と同じ結果を得ることができます。

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

これにより、さらに簡潔な出力が得られます。

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>複数のゾーンへのポッドの分散を確認する

「[有名なラベル、注釈、およびテイント][kubectl-well_known_labels]」に記載されているように、Kubernetes は `failure-domain.beta.kubernetes.io/zone` ラベルを使用して、使用可能なさまざまなゾーンにわたってレプリケーション コントローラーまたはサービス内のポッドを自動的に分散します。 これをテストするには、クラスターを 3 ノードから 5 ノードにスケールアップして、正しいポッド拡散を確認します。

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

数分後にスケール操作が完了すると、Bash シェルのコマンド `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` によって、次の例のような出力が得られます。

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

ゾーン 1 とゾーン 2 に 2 つのノードが追加されました。 3 つのレプリカで構成されるアプリケーションをデプロイできます。 例として NGINX を使用します。

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

ポッドが実行されているノードを表示すると、3 つの異なる可用性ゾーンに対応するノードでポッドが実行されていることがわかります。 たとえば、Bash シェルでコマンド `kubectl describe pod | grep -e "^Name:" -e "^Node:"` を使用すると、次のような出力が表示されます。

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

前の出力からわかるように、最初のポッドは、可用性ゾーン `eastus2-1` にあるノード 0 で実行されています。 2 つ目のポッドは、`eastus2-3` に対応するノード 2 で実行されています。また、3 つ目のポッドは、`eastus2-2` のノード 4 で実行されています。 追加の構成を行うことなく、Kubernetes は 3 つの可用性ゾーンすべてにわたってポッドを正しく分散します。

## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーンを使用する AKS クラスターの作成方法について詳しく説明します。 高可用性クラスターの考慮事項について詳しくは、「[AKS での事業継続とディザスター リカバリーに関するベスト プラクティス][best-practices-bc-dr]」をご覧ください。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
