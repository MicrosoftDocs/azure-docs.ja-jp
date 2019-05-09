---
title: Azure Kubernetes Service (AKS) についてよく寄せられる質問
description: Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: ae92a5c894b186a1c8b471c1b446a88299742aec
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004858"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>現在、Azure Kubernetes Service (AKS) は、どの Azure リージョンで提供されますか?

利用可能なリージョンの完全な一覧については、[AKS の利用可能なリージョン][aks-regions]に関するページを参照してください。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか?

はい、自動スケールは、Kubernetes 1.10 以降、[Kubernetes autoscaler][auto-scaler] 経由で使用できます。 クラスターの自動スケーラーを構成して使用する方法の詳細については、[AKS のクラスター自動スケーラー][aks-cluster-autoscale]に関するページを参照してください。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS では Kubernetes のロールベースのアクセス制御 (RBAC) がサポートされますか?

はい、Azure CLI を使用してクラスターが作成されるときに、Kubernetes RBAC は既定で有効になっています。 RBAC は、Azure Portal またはテンプレートを使用して作成されたクラスターに対して、有効化できます。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>既存の仮想ネットワークに AKS をデプロイできますか?

はい、[高度なネットワーク機能][aks-advanced-networking]を使用して、既存の仮想ネットワークに AKS クラスターをデプロイできます。

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>仮想ネットワーク内でのみアクセスできるように Kubernetes API サーバーを制限できますか?

現時点ではありません。 Kubernetes API サーバーは、パブリックの完全修飾ドメイン名 (FQDN) として公開されます。 クラスターへのアクセスは、[Kubernetes のロールベースのアクセス制御 (RBAC) と Azure Active Directory (AAD)][aks-rbac-aad] を使って制御できます。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

はい、Azure では、セキュリティ更新プログラムが夜間スケジュールでクラスター内のノードに自動的に適用されます。 ただし、必要に応じてノードが再起動されることを確認する必要があります。 ノードの再起動の実行にはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターは自動的に [cordon および drain ノード][cordon-drain]をアップグレードして、最新の Ubuntu イメージを備えた各ノードのバックアップと、新しいパッチ バージョンまたは Kubernetes のマイナー バージョンを取得します。 詳細については、「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください。
- Kubernetes 用のオープン ソースの再起動デーモンである [Kured](https://github.com/weaveworks/kured) を使用します。 Kured は [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) として実行され、再起動が必要であることを示すファイルの存在を各ノードで監視します。 OS の再起動は、クラスター アップグレードと同じ [cordon および drain プロセス][cordon-drain]を使用するクラスターで管理されます。

Kured の使用について詳しくは、[AKS 上のノードへのセキュリティおよびカーネルの更新プログラムの適用][node-updates-kured]に関する記事をご覧ください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

各 AKS デプロイは、2 つのリソース グループにまたがります。

- 1 つは自分が作成したリソース グループで、Kubernetes サービス リソースのみが含まれます。 AKS リソース プロバイダーは、*MC_myResourceGroup_myAKSCluster_eastus* のような 2 つ目のリソース グループをデプロイ時に自動的に作成します。
- *MC_myResourceGroup_myAKSCluster_eastus* などのこの 2 つ目のリソース グループには、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これらのリソースには、Kubernetes ノードの VM、仮想ネットワー キング、およびストレージが含まれます。 この別個のリソース グループは、リソースのクリーンアップを簡略化するために作成されます。

ストレージ アカウントや予約済みパブリック IP アドレスなど、AKS クラスターで使用するリソースを作成する場合は、自動的に生成されたリソース グループにそれらを配置します。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>MC_* リソース グループ内の AKS リソースのタグや他のプロパティを変更できますか?

*MC_** リソース グループのリソースの Azure で作成されたタグやその他のプロパティを変更または削除すると、スケーリングやアップグレードのエラーなど、予期しない結果につながる可能性があります。 ビジネス ユニットやコスト センターの割り当てなどの目的で、追加のカスタム タグを作成および変更することができます。 AKS クラスター内の *MC_** でリソースを変更すると、サービス レベル目標 (SLO) が中断されます。 詳細については、「[AKS でサービス レベル アグリーメントは提供されますか。](#does-aks-offer-a-service-level-agreement)」を参照してください。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS ではどのような Kubernetes アドミッション コントローラーがサポートされますか? アドミッション コントローラーの追加や削除はできますか?

AKS では、次の[アドミッション コントローラー][admission-controllers]をサポートします。

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

現時点では、AKS でアドミッション コントローラーの一覧を修正することはできません。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

AKS は現在、Azure Key Vault とネイティブに統合されていません。 ただし、[Kubernetes プロジェクト用の Azure Key Vault FlexVolume][keyvault-flexvolume] を使用して、Kubernetes ポッドから KeyVault シークレットへの直接統合を行うことはできます。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

Windows Server コンテナーを実行するには、Windows Server ベースのノードを実行する必要があります。 Windows Server ベースのノードは、現時点では、AKS では使用できません。 ただし、Virtual Kubelet を使用して、Azure Container Instances で Windows コンテナーをスケジュールし、それらを AKS クラスターの一部として管理できます。 詳細については、[AKS での Virtual Kubelet の使用][virtual-kubelet]に関する記事を参照してください。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS でサービス レベル アグリーメントは提供されますか。

サービス レベル アグリーメント (SLA) では、公開されたサービス レベルを満たしていない場合に、プロバイダーがサービスの費用を顧客に払い戻すことに同意します。 AKS 自体は無料であるため、払い戻せる費用はありません。したがって、これは正式な SLA ではありません。 ただし、AKS では、Kubernetes API サーバーの 99.5% 以上の可用性を維持できるようにしています。

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

