---
title: 仮想ノードの使用
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Services (AKS) での仮想ノードの使用方法の概要
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634449"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Azure Kubernetes Service (AKS) クラスターを作成し、仮想ノードを使用できるように構成する

AKS クラスターでアプリケーション ワークロードをすばやくスケーリングするには、仮想ノードを使用します。 仮想ノードを使用すると、ポッドを短時間でプロビジョニングできるため、ポッドの実行時間に対して秒単位の支払いだけで済みます。 Kubernetes クラスターのオートスケーラーが VM コンピューティング ノードをデプロイして追加のポッドを実行するのを待つ必要はありません。 仮想ノードは、Linux のポッドとノードでのみサポートされます。

AKS 用の仮想ノード アドオンは、オープン ソース プロジェクト [Virtual Kubelet][virtual-kubelet-repo] に基づいています。

この記事では、仮想ノードを使用する上でのリージョンの可用性およびネットワークの要件の概要と、既知の制限事項について説明します。

## <a name="regional-availability"></a>リージョン別の提供状況

ACI によって VNET SKU がサポートされているリージョンはすべて、仮想ノードのデプロイに対応しています。

各リージョンで使用可能な CPU およびメモリ SKU については、[Azure リージョン内の Azure Container Instances 用の Azure Container Instances リソースの可用性 (Linux コンテナー グループ)](../container-instances/container-instances-region-availability.md#linux-container-groups) に関するページを参照してください

## <a name="network-requirements"></a>ネットワークの要件

仮想ノードを使用すると、Azure Container Instances (ACI) および AKS クラスターで実行されているポッド間でのネットワーク通信が可能になります。 この通信を可能にするために、仮想ネットワーク サブネットが作成され、委任されたアクセス許可が割り当てられます。 仮想ノードは、"*高度*" ネットワーク (Azure CNI) を使用して作成された AKS クラスターに対してのみ機能します。 既定では、AKS クラスターは "*基本*" ネットワーク (kubenet) を使用して作成されます。

Azure Container Instances (ACI) で実行されているポッドは、ネットワークを構成するために、AKS API サーバー エンドポイントにアクセスする必要があります。

## <a name="known-limitations"></a>既知の制限事項

仮想ノードの機能は、ACI の機能セットに大きく依存します。 [Azure Container Instances のクォータと制限](../container-instances/container-instances-quotas.md)に加えて、次のシナリオは仮想ノードではまだサポートされていません。

* サービス プリンシパルを使用した ACR イメージのプル。 [対処法](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry)は、[Kubernetes シークレット](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)を使用することです
* [仮想ネットワークの制限事項](../container-instances/container-instances-vnet.md) (VNet ピアリング、Kubernetes ネットワーク ポリシー、およびネットワーク セキュリティ グループを使用したインターネットへの送信トラフィックなど)。
* Init コンテナー
* [ホストのエイリアス](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI の exec の[引数](../container-instances/container-instances-exec.md#restrictions)
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) を使用すると、ポッドは仮想ノードにデプロイされません
* 仮想ノードでは、Linux ポッドのスケジュール設定がサポートされています。 オープンソースの [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) プロバイダーを手動でインストールして、Windows Server のコンテナーを ACI にスケジュールすることができます。
* 仮想ノードには、Azure CNI ネットワークを使用した AKS クラスターが必要です。
* AKS に対する API サーバーの許可された IP 範囲の使用。
* Azure Files 共有のサポート[汎用 v1](../storage/common/storage-account-overview.md#types-of-storage-accounts) をマウントするボリューム。 [Azure Files 共有を含むボリューム](azure-files-volume.md)をマウントする手順に従います。
* IPv6 の使用はサポートされていません。

## <a name="next-steps"></a>次のステップ

ご利用のクラスターに対して仮想ノードを構成します。

- [Azure CLI を使用して仮想ノードを作成する](virtual-nodes-cli.md)
- [Azure Kubernetes Service (AKS) でポータルを使用して仮想ノードを作成する](virtual-nodes-portal.md)

多くの場合、仮想ノードは AKS のスケーリング ソリューションの 1 つのコンポーネントです。 スケーリング ソリューションの詳細については、次の記事を参照してください。

- [Kubernetes のポッドの水平オートスケーラーを使用する][aks-hpa]
- [Kubernetes クラスター オートスケーラーを使用する][aks-cluster-autoscaler]
- [仮想ノード用の自動スケーリング サンプルをチェックアウトする][virtual-node-autoscale]
- [Virtual Kubelet のオープン ソース ライブラリの詳細を確認する][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
