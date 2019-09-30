---
title: Azure Kubernetes Service (AKS) についてよく寄せられる質問
description: Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 54a95186a297cf3604858341fb8f5aba3702bf5a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241794"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) についてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) についてよく寄せられる質問にお答えします。

## <a name="which-azure-regions-currently-provide-aks"></a>現在はどの Azure リージョンで AKS が提供されていますか?

利用可能なリージョンの完全な一覧については、[AKS の利用可能なリージョン][aks-regions]に関するページを参照してください。

## <a name="does-aks-support-node-autoscaling"></a>AKS はノードの自動スケールをサポートしていますか?

はい、AKS でエージェント ノードを自動的に水平方向にスケーリングする機能は、現在プレビューで利用できます。 手順については、[AKS でアプリケーションの需要を満たすためのクラスターの自動スケーリング][aks-cluster-autoscaler]に関するページを参照してください。 AKS の自動スケーリングは、[Kubernetes autoscaler][auto-scaler] に基づきます。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>既存の仮想ネットワークに AKS をデプロイできますか?

はい、[高度なネットワーク機能][aks-advanced-networking]を使用して、既存の仮想ネットワークに AKS クラスターをデプロイできます。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API サーバーにアクセスできるユーザーを制限できますか?

はい、現在プレビュー中の [API サーバーの許可された IP 範囲][api-server-authorized-ip-ranges]を使用して、Kubernetes API サーバーへのアクセスを制限できます。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kubernetes API サーバーを仮想ネットワーク内でのみアクセスできるようにできますか?

現時点ではできませんが、計画されています。 進捗状況は、[AKS GitHub リポジトリ][private-clusters-github-issue]で追跡できます。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>1 つのクラスター内で、異なる VM サイズを設定できますか?

はい、現在プレビュー中の[複数のノード プール][multi-node-pools]を作成することで、AKS クラスター内で異なる仮想マシン サイズを使用できます。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>AKS エージェント ノードにセキュリティ更新プログラムは適用されますか?

Azure では、セキュリティ更新プログラムが夜間スケジュールでご利用のクラスター内の Linux ノードに自動的に適用されます。 ただし、必要な場合は、それらの Linux ノードが確実に再起動されるようにする必要があります。 ノードを再起動するにはいくつかのオプションがあります。

- Azure Portal または Azure CLI から手動で行います。
- AKS クラスターをアップグレードします。 クラスターでは、[cordon ノードと drain ノード][cordon-drain]が自動的にアップグレードされてから、最新の Ubuntu イメージと、新しいパッチ バージョンまたは Kubernetes のマイナー バージョンで、新しいノードがオンラインにされます。 詳細については、「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください。
- Kubernetes 用のオープン ソースの再起動デーモンである [Kured](https://github.com/weaveworks/kured) を使用します。 Kured は [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) として実行され、再起動が必要であることを示すファイルの存在が各ノードで監視されます。 クラスター全体で、クラスターのアップグレードと同じ [cordon および drain プロセス][cordon-drain]によって OS の再起動が管理されます。

Kured の使用の詳細については、[AKS 上のノードへのセキュリティおよびカーネルの更新プログラムの適用][node-updates-kured]に関する記事を参照してください。

### <a name="windows-server-nodes"></a>Windows Server ノード

Windows Server ノードでは (現在、AKS ではプレビュー中)、Windows Update が自動的に実行されたり、最新の更新プログラムが適用されたりすることはありません。 Windows Update のリリース サイクルと独自の検証プロセス前後の定期的スケジュールで、AKS クラスター内のクラスターと Windows Server ノード プールでアップグレードを実行する必要があります。 このアップグレード プロセスでは、最新の Windows Server イメージと修正プログラムを実行するノードが作成されて、古いノードが削除されます。 このプロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS と一緒にリソース グループが 2 つ作成されるのはなぜでしょうか?

各 AKS デプロイは、2 つのリソース グループにまたがります。

1. 最初のリソース グループを作成します。 このグループには、Kubernetes サービスのリソースのみが含まれます。 AKS リソース プロバイダーにより、デプロイの間に 2 番目のリソース グループが自動的に作成されます。 2 番目のリソース グループの例は、*MC_myResourceGroup_myAKSCluster_eastus* です。 この 2 つ目のリソース グループの名前を指定する方法については、次のセクションをご覧ください。
1. *ノード リソース グループ*と呼ばれる 2 つ目のリソース グループには、クラスターに関連付けられたインフラストラクチャ リソースがすべて含まれます。 これらのリソースには、Kubernetes ノードの VM、仮想ネットワー キング、およびストレージが含まれます。 既定では、ノード リソース グループには *MC_myResourceGroup_myAKSCluster_eastus* のような名前が付いています。 AKS は、クラスターが削除されるたびにノード リソースを自動的に削除するため、クラスターのライフサイクルを共有するリソースにのみ使用する必要があります。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS ノード リソース グループに独自の名前を指定できますか?

はい。 既定では、AKS によってノード リソース グループに *MC_resourcegroupname_clustername_location* という名前が設定されますが、独自の名前を指定することもできます。

独自のリソース グループ名を指定するには、[aks-preview][aks-preview-cli] Azure CLI 拡張機能バージョン *0.3.2* 以降をインストールします。 [az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときに、 *--node-resource-group* パラメーターを使用してリソース グループの名前を指定します。 [Azure Resource Manager テンプレートを使用][aks-rm-template]して AKS クラスターをデプロイする場合は、*nodeResourceGroup* プロパティを使用してリソース グループ名を定義できます。

* セカンダリ リソース グループは、自分のサブスクリプションの Azure リソース プロバイダーによって自動的に作成されます。
* カスタム リソース グループの名前を指定できるのは、クラスターを作成するときだけです。

ノード リソース グループを使うときは、次のことができないので注意してください。

* ノード リソース グループに対して既存のリソース グループを指定すること。
* ノード リソース グループに対して異なるサブスクリプションを指定すること。
* クラスターが作成された後で、ノード リソース グループ名を変更すること。
* ノード リソース グループ内の管理対象リソースに名前を指定すること。
* ノード リソース グループ内の管理対象リソースのタグを変更または削除すること。 (詳しくは、次のセクションで追加の情報を参照してください。)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>ノード リソース グループ内の AKS リソースのタグや他のプロパティを変更できますか?

ノード リソース グループ内の Azure で作成されたタグや他のリソース プロパティを変更または削除する場合、スケーリングやアップグレードのエラーなど、予期しない結果になる可能性があります。 AKS では、カスタム タグを作成および変更できます。 たとえばビジネス単位やコスト センターを割り当てるために、カスタム タグを作成または変更することがあります。 AKS クラスター内のノード リソース グループの下にあるリソースを変更すると、サービス レベル目標 (SLO) が中断されます。 詳細については、「[AKS でサービス レベル アグリーメントは提供されますか?](#does-aks-offer-a-service-level-agreement)」を参照してください。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS ではどのような Kubernetes アドミッション コントローラーがサポートされますか? アドミッション コントローラーの追加や削除はできますか?

AKS では、以下の[アドミッション コントローラー][admission-controllers]がサポートされています。

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

現在は、AKS でアドミッション コントローラーの一覧を変更することはできません。

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS には Azure Key Vault が統合されているのですか?

AKS は現在、Azure Key Vault とネイティブに統合されていません。 ただし、[Kubernetes プロジェクト用の Azure Key Vault FlexVolume][keyvault-flexvolume] を使用して、Kubernetes ポッドから Key Vault のシークレットに直接統合することはできます。

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS で Windows Server コンテナーを実行できますか?

はい、Windows Server コンテナーはプレビューでご利用になれます。 AKS で Windows Server コンテナーを実行するには、Windows Server をゲスト OS として実行するノード プールを作成します。 Windows Server コンテナーでは、Windows Server 2019 のみを使用できます。 開始するには、[Windows Server ノード プールでの AKS クラスターの作成][aks-windows-cli]に関するページを参照してください。

Windows Server によるノード プールのサポートには、Kubernetes プロジェクトの上流の Windows Server の一部であるいくつかの制限が含まれます。 これらの制限の詳細については、[AKS での Windows Server コンテナーの制限事項][aks-windows-limitations]に関するページを参照してください。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS でサービス レベル アグリーメントは提供されますか?

サービス レベル アグリーメント (SLA) では、プロバイダーは、公開されたサービス レベルが満たされない場合に、サービスの費用を顧客に払い戻すことに同意します。 AKS は無料であり、払い戻す費用がないため、AKS には正式な SLA はありません。 ただし、AKS では、Kubernetes API サーバーの 99.5 パーセント以上の可用性を維持できるようにしています。

Kubernetes コントロール プレーンのアップタイムを意味する AKS サービスの可用性と、Azure Virtual Machines 上で実行されている特定のワークロードの可用性の違いを把握することが重要です。 コントロール プレーンの準備ができていない場合はコントロール プレーンを使用できない場合がありますが、Azure VM 上で実行されているクラスターのワークロードは引き続き機能します。 すべての Azure VM は、財務 SLA でサポートされる有料リソースです。 Azure VM の SLA と、[可用性ゾーン][availability-zones]などの機能を使用して可用性を向上させる方法の[詳細についてはこちら](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/)を参照してください。

## <a name="why-cant-i-set-maxpods-below-30"></a>maxPods を 30 未満に設定できないのはなぜですか?

AKS では、Azure CLI および Azure Resource Manager テンプレートを使ってクラスターを作成するときに、`maxPods` の値を設定することができます。 ただし、Kubenet と Azure CNI の両方で、"*最小限の値*" が必要です(作成時に検証されます)。

| ネットワーク | 最小値 | 最大値 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

AKS はマネージド サービスであるため、クラスターの一部としてアドオンとポッドをデプロイして管理します。 以前は、ユーザーはマネージド ポッドが実行するために必要な値より小さい値に `maxPods` を定義できました (たとえば 30)。 現在は、AKS では "((maxPods または (maxPods * vm_count)) > マネージド アドオン ポッドの最小値" という式を使って、ポッドの最小数が計算されるようになりました。

ユーザーは、最小の `maxPods` の検証をオーバーライドできません。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>自分の AKS エージェント ノードに Azure の予約割引を適用できますか?

AKS エージェント ノードは、標準の Azure 仮想マシンとして課金されます。したがって、AKS で使用している VM サイズに対して [Azure の予約][reservation-discounts]を購入している場合、それらの割引が自動的に適用されます。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Azure テナント間でクラスターを移動/移行することはできますか?

`az aks update-credentials` コマンドを使用して、Azure テナント間で AKS クラスターを移動できます。 「[サービス プリンシパルの更新または作成の選択](https://docs.microsoft.com/azure/aks/update-credentials)」の指示に従って、[AKS クラスターを新しい資格情報で更新します](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>サブスクリプション間でクラスターを移動/移行することはできますか?

サブスクリプション間でのクラスターの移動は現在サポートされていません。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS クラスターを現在の Azure サブスクリプションから別のサブスクリプションへ移動することはできますか? 

Azure サブスクリプション間での AKS クラスターおよびそれに関連付けられているリソースの移動はサポートされていません。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>クラスターの削除に時間がかかるのはなぜですか? 

ほとんどのクラスターはユーザーの要求に応じて削除されます。場合によっては、特に顧客が独自のリソース グループを持ち込んでいたり、リソース グループ間のタスクの削除を行っている場合に、さらに時間がかかったり、失敗することがあります。 削除に関する問題が発生した場合は、リソース グループをロックしていないこと、リソース グループ外のリソースのリソース グループとの関連付けが解除されていることなどを再確認してください。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>'NodeLost' または 'Unknown' の状態のポッドまたはデプロイがある場合でも、クラスターをアップグレードできますか?

可能ですが、AKS ではお勧めしていません。 アップグレードは、クラスターの状態がわかっており正常な場合に実行することが理想的です。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>異常な状態のノードやシャットダウンしているノードを 1 つ以上含むクラスターがある場合に、アップグレードを実行できますか?

できません。エラー状態や、それ以外にクラスターから取り除かれているノードはすべて、アップグレード前に削除/除去してください。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>クラスターの削除を実行しましたが、`[Errno 11001] getaddrinfo failed` のエラーが表示されます 

最も一般的な原因は、まだ使用中でクラスターに関連付けられている 1 つ以上のネットワーク セキュリティ グループ (NSG) をユーザーが保持していることです。  これらのグループを取り除いてから、もう一度削除を試してください。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>アップグレードを実行しましたが、ポッドがクラッシュ ループの状態になりました。準備プローブが失敗していますか?

サービス プリンシパルの有効期限が切れていないことを確認してください。  詳細については、[AKS サービス プリンシパル](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)と [AKS の資格情報の更新](https://docs.microsoft.com/azure/aks/update-credentials)に関するページを参照してください。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>クラスターは動作していましたが、突然、LoadBalancers のプロビジョニングや PVC のマウントなどができなくなりました。 

サービス プリンシパルの有効期限が切れていないことを確認してください。  詳細については、[AKS サービス プリンシパル](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)と [AKS の資格情報の更新](https://docs.microsoft.com/azure/aks/update-credentials)に関するページを参照してください。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>仮想マシン スケール セット API を使用して手動でスケーリングできますか?

いいえ、仮想マシン スケール セット API を使用したスケール操作はサポートされていません。 AKS API (`az aks scale`) を使用してください。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>仮想マシン スケール セットを使用して、0 ノードに手動でスケーリングできますか?

いいえ、仮想マシン スケール セット API を使用したスケール操作はサポートされていません。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>すべての VM を停止したり、その割り当てを解除したりできますか?

AKS には、このような構成に耐え、そこから復旧するための回復性メカニズムがありますが、これは推奨する構成ではありません。

## <a name="can-i-use-custom-vm-extensions"></a>カスタム VM 拡張機能を使用できますか?

マネージド サービスである AKS はなく、IaaS リソースの操作はサポートされていません。 カスタム コンポーネントなどをインストールするには、 Kubernetes の API およびメカニズムを活用してください。 たとえば、必要なコンポーネントをインストールするには、DaemonSet を活用します。

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
