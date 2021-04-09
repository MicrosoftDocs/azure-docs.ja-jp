---
title: Azure Kubernetes Service に関する一般的な問題のトラブルシューティング
description: Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 1d3dff19bd75bfa4e7564eb4b188ffe68d605025
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952033"
---
# <a name="aks-troubleshooting"></a>AKS のトラブルシューティング

Azure Kubernetes Service (AKS) クラスターを作成または管理するときに、問題が発生することがあります。 この記事では、よくある問題とトラブルシューティングの手順について詳しく説明します。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常、Kubernetes の問題のデバッグに関する情報はどこにありますか。

[Kubernetes クラスターのトラブルシューティングに関する公式ガイド](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)をご覧ください。
Microsoft のエンジニアによって公開された、ポッド、ノード、クラスター、他の機能のトラブルシューティングに関する[トラブルシューティング ガイド](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)もあります。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>作成時またはアップグレード中に `quota exceeded` エラーが発生します。 どうすればよいですか。 

 [さらに多くのコアを要求します](../azure-portal/supportability/resource-manager-core-quotas-request.md)。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>高度なネットワークを使用して AKS クラスターをデプロイしているときに `insufficientSubnetSize` エラーが発生します。 どうすればよいですか。

このエラーは、クラスターに使用中のサブネットに、正常なリソース割り当てのためにその CIDR 内で使用可能な IP がもうないことを示しています。 Kubenet クラスターでは、クラスター内のノードごとに十分な IP 空間が必要です。 Azure CNI クラスターでは、クラスター内の各ノードとポッドに十分な IP 空間が必要です。
[ポッドに IP を割り当てるための Azure CNI の設計](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)の詳細については、こちらを参照してください。

これらのエラーは、サブネットのサイズが不足しているなどの問題を事前に検出する [AKS 診断](concepts-diagnostics.md)でも検出されます。

次の 3 つのケースでは、サブネットのサイズ不足エラーが発生します。

1. AKS Scale または AKS Node pool scale
   1. Kubenet を使用している場合は、`number of free IPs in the subnet` が `number of new nodes requested` **より小さい** とき。
   1. Azure CNI を使用している場合は、`number of free IPs in the subnet` が `number of nodes requested times (*) the node pool's --max-pod value` **より小さい** とき。

1. AKS Upgrade または AKS Node pool upgrade
   1. Kubenet を使用している場合は、`number of free IPs in the subnet` が `number of buffer nodes needed to upgrade` **より小さい** とき。
   1. Azure CNI を使用している場合は、`number of free IPs in the subnet` が `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` **より小さい** とき。
   
   既定では、AKS クラスターによって最大サージ (アップグレード バッファー) 値が 1 に設定されますが、このアップグレード動作は、アップグレードを完了するために必要な使用可能な IP の数を増やすノード プールの最大サージ値を設定することでカスタマイズできます。

1. AKS create または AKS Node pool add
   1. Kubenet を使用している場合は、`number of free IPs in the subnet` が `number of nodes requested for the node pool` **より小さい** とき。
   1. Azure CNI を使用している場合は、`number of free IPs in the subnet` が `number of nodes requested times (*) the node pool's --max-pod value` **より小さい** とき。

新しいサブネットを作成することによって、次の軽減策を実行できます。 既存のサブネットの CIDR 範囲を更新できないため、リスク軽減には新しいサブネットを作成するためのアクセス許可が必要です。

1. 操作の目標に十分な、より大きな CIDR 範囲を持つ新しいサブネットを再構築します。
   1. 必要な重複しない新しい範囲を使用して、新しいサブネットを作成します。
   1. 新しいサブネットに新しいノード プールを作成します。
   1. 置換する古いサブネットにある古いノード プールからポッドをドレインします。
   1. 古いサブネットと古いノード プールを削除します。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>ポッドが CrashLoopBackOff モードでスタックします。 どうすればよいですか。

このモードでポッドがスタックする理由は複数あります。 以下を確認します。

* ポッド自体。`kubectl describe pod <pod-name>` を使用します。
* ログ。`kubectl logs <pod-name>` を使用します。

ポッドの問題のトラブルシューティング方法について詳しくは、「[Debug applications (アプリケーションをデバッグする)](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)」をご覧ください。

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>`kubectl` または API サーバーに接続する他のサードパーティ製ツールを使用すると、`TCP timeouts` が発生します
AKS には、サービス レベル目標 (SLO) とサービス レベル アグリーメント (SLA) を保証するために、コア数に応じて垂直方向にスケーリングする HA コントロール プレーンがあります。 接続のタイムアウトが発生している場合は、以下を確認してください。

- **すべての API コマンドが常にタイムアウトしていますか、それとも一部だけですか?** 一部だけの場合は、ノードからコントロール プレーンへの通信を担当する `tunnelfront` ポッドまたは `aks-link` ポッドが実行状態ではない可能性があります。 このポッドをホストしているノードが過剰に使用されていないか、ストレスがかかっていないことを確認してください。 これらを独自の [`system` ノード プール](use-system-pools.md)に移動することを検討してください。
- **[AKS のエグレス トラフィックの制限に関するドキュメント](limit-egress-traffic.md)に記載されているすべての必要なポート、FQDN、および IP を開いていますか?** そうでない場合は、いくつかのコマンドの呼び出しが失敗する可能性があります。
- **現在の IP は [API IP の承認された範囲](api-server-authorized-ip-ranges.md)に含まれていますか?** この機能を使用していて、IP が範囲に含まれていない場合、呼び出しはブロックされます。 
- **API サーバーへの呼び出しをリークしているクライアントまたはアプリケーションはありますか?** 頻繁な get 呼び出の代わりにウォッチを使用し、サードパーティのアプリケーションがそのような呼び出しをリークしていないことを確認してください。 たとえば、Istio Mixer のバグにより、シークレットが内部で読み取られるたびに新しい API サーバー ウォッチ接続が作成されます。 この動作は一定の間隔で発生するため、ウォッチ接続はすぐに蓄積され、最終的に API サーバーがスケーリング パターンに関係なく過負荷になります。 https://github.com/istio/istio/issues/19481
- **helm デプロイに多くのリリースがありますか?** このシナリオでは、両方の tiller がノードで大量のメモリを使用するだけでなく、大量の `configmaps` が発生する可能があるため、API サーバーでの不要な急増が発生する可能性があります。 `helm init` で `--history-max` を構成し、新しい Helm 3 を利用することを検討してください。 次のイシューで詳細を確認してください。 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[ノード間の内部トラフィックがブロックされていますか?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>`dial tcp <Node_IP>:10250: i/o timeout` などの `TCP timeouts` が発生します

こうしたタイムアウトは、ノード間の内部トラフィックがブロックされている場合に発生します。 クラスターのノードのサブネットに適用されている[ネットワーク セキュリティ グループ](concepts-security.md#azure-network-security-groups)などによって、このトラフィックがブロックされていないことを確認します。

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>既存のクラスターで Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) を有効にしようとしています。 どうすればいいですか。

現時点では、既存のクラスターで Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) を有効にすることはサポートされていません。新しいクラスターを作成するときに設定する必要があります。 CLI、ポータル、または `2020-03-01` 以降の API バージョンを使用すると、既定で Kubernetes RBAC が有効になります。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs を使用してログを取得できません。または、API サーバーに接続できません。 "Error from server: error dialing backend: dial tcp…" (サーバーからのエラー: バックエンドへのダイヤルでのエラー: tcp にダイヤル...) と表示されます。 どうすればよいですか。

API サーバーに接続するために、ポート 22、9000、および 1194 が開いていることを確認します。 `kubectl get pods --namespace kube-system` コマンドを使用して、`tunnelfront` または `aks-link` ポッドが *kube-system* 名前空間で実行されているかどうかを確認します。 そうでない場合は、ポッドを強制的に削除すると、再起動されます。

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>AKS API に接続するときに、クライアントから `"tls: client offered only unsupported versions"` を取得しています。 どうすればよいですか。

AKS でサポートされる TLS の最小バージョンは TLS 1.2 です。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>アップグレードまたはスケーリングを行おうとすると、`"Changing property 'imageReference' is not allowed"` エラーが発生します。 この問題を解決するにはどうすればよいですか。

AKS クラスター内のエージェント ノードのタグを変更したことが原因で、このエラーが発生している可能性があります。 MC_* リソース グループのリソースのタグやその他のプロパティを変更または削除すると、予期しない結果につながる可能性があります。 AKS クラスターの MC_* グループでリソースを変更すると、サービス レベル目標 (SLO) が中断されます。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-cluster-failed に基づいています。*

このエラーは、複数の理由でクラスターがエラー状態になったときに発生します。 以前に失敗した操作を再試行する前に、次の手順に従ってクラスターのエラー状態を解決してください。

1. クラスターが `failed` 状態から回復するまで、`upgrade` 操作と `scale` 操作は成功しません。 一般的な根本問題と解決策は次のとおりです。
    * **計算 (CRP) クォータが不足** している状態でのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-portal/supportability/resource-manager-core-quotas-request.md)に従って計算クォータの引き上げを依頼します。
    * 高度なネットワーク リソースと **不十分なサブネット (ネットワーク) リソース** を使用したクラスターのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-resource-manager/templates/error-resource-quota.md#solution)に従ってリソース クォータの引き上げを依頼します。
2. アップグレードの失敗の根本原因が解決されると、クラスターは成功状態になるはずです。 成功状態が確認されたら、元の操作を再試行します。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>クラスターをアップグレードまたはスケーリングしようとしたときに、クラスターがアップグレード中であるか、アップグレードに失敗したというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-pending-upgrade に基づいています。*

 クラスターまたはノード プールを同時にアップグレードしたり、スケーリングしたりすることはできません。 その代わりに、ターゲット リソースに対する次の要求を行う前に、それぞれの種類の操作をその同じリソースで完了させる必要があります。 その結果、アクティブなアップグレード操作またはスケール操作を実行中、または試行中の場合、操作は制限されます。 

問題の診断に役立てるには、`az aks show -g myResourceGroup -n myAKSCluster -o table` を実行してクラスターの詳細な状態を取得します。 結果に基づいて、次のことを行います。

* クラスターのアップグレードがアクティブに実行中の場合は、操作が終了するまで待ちます。 成功した場合は、もう一度、以前に失敗した操作を再試行します。
* クラスターのアップグレードに失敗した場合は、上記のセクションで概説された手順に従います。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>自分のクラスターを別のサブスクリプションに移したり、自分のクラスターが含まれる自分のサブスクリプションを新しいテナントに移したりできますか?

AKS クラスターを別のサブスクリプションに移動した場合、またはクラスターのサブスクリプションを新しいテナントに移動した場合、クラスター ID のアクセス許可がないため、クラスターは機能しません。 この制約により、**AKS はサブスクリプションまたはテナント間でのクラスターの移動をサポートしていません**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>仮想マシン スケール セットを必要とする機能を使用しようとするとエラーが発生します

*このトラブルシューティングの支援は、aka.ms/aks-vmss-enablement に基づいています*

次の例のように、AKS クラスターが仮想マシン スケール セット上にないことを示すエラーが表示される場合があります。

**AgentPool `<agentpoolname>` は自動スケールが有効に設定されていますが、Virtual Machine Scale Sets 上にありません**

クラスターのオートスケーラーや複数のノード プールなどの機能を利用するには、`vm-set-type` として仮想マシン スケール セットが必要となります。

適切なドキュメントの「*開始する前に*」のステップに従い、AKS クラスターを正しく作成します。

* [クラスター オートスケーラーを使用する](cluster-autoscaler.md)
* [複数のノード プールを作成し使用する](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS リソースおよびパラメーターにはどのような名前付けの制約が適用されますか。

*このトラブルシューティングの支援は、aka.ms/aks-naming-rules に基づいています*

名前付けの制約は、Azure プラットフォームと AKS の両方によって実装されます。 リソース名またはパラメーターがこれらのいずれかの制約に違反した場合、別の入力を行うように求めるエラーが返されます。 次の一般的な名前付けのガイドラインが適用されます。

* クラスター名は 1 ～ 63 文字にする必要があります。 使用できる文字は、英字、数字、ダッシュ、およびアンダースコアのみです。 先頭と末尾の文字は、文字または数字にしてください。
* AKS Node/*MC_* リソース グループ名は、リソース グループ名とリソース名を結合します。 `MC_resourceGroupName_resourceName_AzureRegion` の自動生成された構文は、80 文字以内にする必要があります。 必要な場合は、リソース グループ名または AKS クラスター名の長さを短くします。 また、[ノード リソース グループ名をカスタマイズする](cluster-configuration.md#custom-resource-group-name)こともできます。
* *dnsPrefix* の最初と最後は英数字の値にする必要があり、1 から 54 文字の間にする必要があります。 有効な文字には英数字の値とハイフン (-) が含まれます。 *dnsPrefix* にはピリオド (.) などの特殊文字を含めることはできません。
* AKS ノード プール名はすべて小文字にする必要があり、linux ノード プールの場合は 1 - 11 文字、windows ノード プールの場合は 1 - 6 文字にする必要があります。 名前は英字で始める必要があり、使用できる文字は英数字のみです。
* Linux ノードに管理者のユーザー名を設定する *admin-username* は、文字で始まる必要があります。文字、数字、ハイフン、アンダースコアのみを含めることができ、最大文字数は 64 文字です。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>クラスターを作成、更新、スケーリング、削除、またはアップグレードしようとすると、別の操作が進行中のためその操作は許可されませんというエラーを受け取ります。

*このトラブルシューティングの支援は、aka.ms/aks-pending-operation に基づいています*

クラスター操作は、前の操作がまだ進行中のときには制限されます。 クラスターの詳細な状態を取得するには、`az aks show -g myResourceGroup -n myAKSCluster -o table` コマンドを使用します。 必要に応じて、独自のリソース グループと AKS クラスター名を使用します。

次のクラスター状態の出力に基づきます。

* クラスターが、 *[成功]* または *[失敗]* 以外のプロビジョニング状態の場合は、操作 (*アップグレード / 更新 / 作成 / スケーリング / 削除 / 移行*) が終了するまで待ってください。 前の操作が完了したら、最新のクラスターの操作を再試行します。

* クラスターでアップグレードが失敗している場合は、 「[クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)」で概説されている手順に従ってください。

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>新しいクラスターを作成しようとしたときに、サービス プリンシパルが見つからないか無効であるというエラーが発生しました。

AKS クラスターの作成時には、ユーザーに代わってリソースを作成するためのサービス プリンシパルまたはマネージド ID が必要です。 AKS では、クラスターの作成時に新しいサービス プリンシパルを自動的に作成したり、既存のサービス プリンシパルを受け取ったりすることができます。 自動的に作成されたものを使用する場合、作成が成功するように、Azure Active Directory によってすべてのリージョンに伝達される必要があります。 この伝達に時間がかかりすぎると、クラスターは使用可能なサービス プリンシパルを見つけることができないため、作成のための検証に失敗します。 

この問題に対しては、次の回避策を使用してください。
* リージョン間で既に伝達されていて、クラスターの作成時に AKS に渡すために存在する既存のサービス プリンシパルを使用します。
* 自動化スクリプトを使用する場合は、サービス プリンシパルの作成と AKS クラスターの作成の間に遅延時間を追加します。
* Azure portal を使用する場合は、作成中にクラスター設定に戻り、数分後に検証ページを再試行します。

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>AKS API を使用するときに、`"AADSTS7000215: Invalid client secret is provided."` を取得しています。   どうすればいいですか。

この問題の原因は、サービス プリンシパルの資格情報の有効期限が切れていることにあります。 [AKS クラスターの資格情報を更新してください。](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>API サーバーの許可された IP 範囲を使用しているときに、オートメーション/開発用コンピューター/ツールからクラスター API にアクセスできません。 この問題を解決するにはどうすればよいですか。

この問題を解決するには、使用されているオートメーション、開発、ツールの各システムの IP または IP 範囲を `--api-server-authorized-ip-ranges` に確実に含めます。 「[許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する](api-server-authorized-ip-ranges.md)」の「IP を見つける方法」のセクションを参照してください。

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>API サーバーの許可された IP 範囲で構成されているクラスターについてのリソースを、Azure portal の Kubernetes リソース ビューアーで表示できません。 この問題を解決するにはどうすればよいですか。

[Kubernetes リソース ビューアー](kubernetes-portal.md) には、ローカル クライアント コンピューターや (ポータルが閲覧されている) IP アドレスの範囲へのアクセスを含めるための `--api-server-authorized-ip-ranges` が必要です。 「[許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する](api-server-authorized-ip-ranges.md)」の「IP を見つける方法」のセクションを参照してください。

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>エグレス トラフィックを制限した後、エラーが表示されました

AKS クラスターからのエグレス トラフィックを制限するときには、AKS 向けの[必須および任意の推奨される](limit-egress-traffic.md)送信ポート、ネットワーク規則と FQDN、アプリケーション規則があります。 設定がこれらの規則のいずれかと競合している場合、特定の `kubectl` コマンドが正しく機能しません。 また、AKS クラスターの作成時にエラーが表示されることがあります。

必須または任意の推奨される送信ポート、ネットワーク規則と FQDN、およびアプリケーション規則のいずれとも、ご利用の設定が競合していないことを確認します。

## <a name="im-receiving-429---too-many-requests-errors"></a>"429 - 要求が多すぎます" エラーが表示されました

Azure 上の kubernetes クラスター (AKS または no) でスケールアップまたはスケールダウンが頻繁に行われる場合、またはクラスター オートスケーラー (CA) が使用される場合、これらの操作によって多数の HTTP 呼び出しが発生し、その結果、割り当てられたサブスクリプションのクォータを超えてエラーが発生する可能性があります。 エラーは次のようになります。

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

これらの調整エラーの詳細については、[こちら](../azure-resource-manager/management/request-limits-and-throttling.md)と[こちら](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors)を参照してください。

多くの機能強化が含まれている 1.18.x 以上のバージョンを確実に実行することを、AKS エンジニアリング チームは推奨しています。 これらの機能強化の詳細については、[こちら](https://github.com/Azure/AKS/issues/1413)と[こちら](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)を参照してください。

これらの調整エラーはサブスクリプション レベルで測定されることを考慮すると、これらは次の場合にも発生する可能性があります。
- GET 要求を行うサードパーティ製のアプリケーション (たとえば、監視アプリケーションなど) がある。 これらの呼び出しの頻度を減らすことをお勧めします。
- 仮想マシン スケール セットを使用した AKS クラスター、ノード プールが多数ある。 ご利用のクラスターの数を、特に、それらが非常にアクティブである (たとえば、アクティブなクラスター オートスケーラー) またはそれらに複数のクライアント (rancher、terraform など) が含まれると予想される場合に別々のサブスクリプションに分割してみてください。

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>操作を実行しているかどうかにかかわらず、クラスターのプロビジョニングの状態が "準備完了" から "失敗" に変更されました。 どうすればよいですか。

操作を実行しているかどうかにかかわらず、クラスターのプロビジョニングの状態が *準備完了* から *失敗* に変更され、クラスター上のアプリケーションの実行は続行している場合、この問題はサービスによって自動的に解決される可能性があり、アプリケーションには影響しません。

クラスターのプロビジョニングの状態が *失敗* のままである場合、またはクラスター上のアプリケーションが動作しなくなった場合は、[サポート リクエストを送信](https://azure.microsoft.com/support/options/#submit)してください。

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>ウォッチが古くなっているか、または Azure AD Pod Identity NMI からステータス 500 が返されている

この[例](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)のように Azure Firewall を使用している場合は、この問題が発生する可能性があります。その理由は、アプリケーション ルールを使用したファイアウォール経由の有効期間の長い TCP 接続に、ファイアウォール上で Go `keepalives` を終了させるバグ (Q1CY21 で解決される) が存在することにあります。 この問題が解決されるまでは、(アプリケーション ルールではなく) ネットワーク ルールを AKS API サーバー IP に追加することで軽減できます。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage および ASK のトラブルシューティング

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Azure Disk の MountOptions で UID と `GID` を設定するときにエラーが発生する

既定では、Azure ディスクは ext4、xfs ファイル システムを使用します。uid=x、gid=x などの mountOptions はマウント時に設定できません。 たとえば、mountOptions uid=999、gid=999 を設定しようとすると、次のようなエラーが表示されます。

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

問題を軽減するには、次のいずれかの方法を行います。

* fsGroup で GID、runAsUser で UID を設定して、[ポッドのセキュリティ コンテキストを構成](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)します。 たとえば、次の設定ではポッドを root として実行するよう設定し、任意のファイルからアクセスできるようにします。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > 既定では、GID と UID は root または 0 としてマウントされるためです。 GID または UID が非 root (1000 など) として設定されている場合、Kubernetes は `chown` を使用して、そのディスクにあるすべてのディレクトリとファイルを変更します。 この操作には時間がかかることがあり、ディスクのマウントが非常に遅くなる可能性があります。

* initContainers で `chown` を使用して、`GID` と `UID` を設定します。 次に例を示します。

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>競合状態の問題および無効なデータ ディスク リストにつながるAzure ディスクのデタッチの失敗

Azure Disk のデタッチに失敗すると、エクスポネンシャル バックオフを使用してディスクのデタッチを最大 6 回再試行します。 また、データ ディスク リストにノード レベルのロックを約 3 分間保持します。 ディスク リストがその間に手動で更新された場合、ノード レベルのロックによって保持されていたディスク リストが古くなり、ノードが不安定になる可能性があります。

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
|--|:--:|
| 1.12 | 1.12.9 以上 |
| 1.13 | 1.13.6 以上 |
| 1.14 | 1.14.2 以上 |
| 1.15 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用していて、ノードのディスク リストが古くなっている場合は、一括操作として VM からすべての存在しないディスクをデタッチすることで、軽減することが可能です。 **存在しないディスクを個別にデタッチすると失敗する場合があります。**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>多数の Azure ディスクによってアタッチ/デタッチが遅くなる

単一のノード VM を対象とする Azure ディスクのアタッチ/デタッチ操作の数が 10 を超える場合、またはその数が 3 を超え、単一の仮想マシン スケール セット プールを対象とする場合には、操作が順次実行されるために、予想よりも低速になることがあります。 この問題は既知の制限であり、現時点では回避策はありません。 [数を超える並列アタッチ/デタッチをサポートするための、ユーザーの声の項目はこちらです](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>エラー状態のノード VM につながる Azure Disk デタッチ エラー

一部のエッジ ケースでは、Azure Disk のデタッチが部分的に失敗し、ノード VM がエラー状態のままになる場合があります。

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
|--|:--:|
| 1.12 | 1.12.10 以上 |
| 1.13 | 1.13.8 以上 |
| 1.14 | 1.14.4 以上 |
| 1.15 以上 | 該当なし |

この問題の修正プログラムがインストールされていないバージョンの Kubernetes を使用していて、ノードがエラー状態である場合は、次のいずれかを使用して VM の状態を手動で更新することで、軽減が可能です。

* 可用性セットベースのクラスターの場合:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS ベースのクラスターの場合:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files および AKS のトラブルシューティング

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>推奨される Kubernetes for Azure File の安定したバージョンは何ですか。
 
| Kubernetes バージョン | 推奨されるバージョン |
|--|:--:|
| 1.12 | 1.12.6 以上 |
| 1.13 | 1.13.4 以上 |
| 1.14 | 1.14.0 以上 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure Files を使用する場合の既定の mountOptions は何ですか。

推奨設定

| Kubernetes バージョン | fileMode および dirMode の値|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 以上 | 0777 |

ストレージ クラス オブジェクトでマウント オプションを指定できます。 次の例では、*0777* が設定されます。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

その他の便利な *mountOptions* 設定を次に示します。

* `mfsymlinks` は、Azure Files mount (cifs) をサポートするシンボリック リンクを作成します
* `nobrl` は、サーバーにバイト範囲のロック要求が送信されるのを防止します。 この設定は、cifs 形式の必須のバイト範囲のロックにより中断する特定のアプリケーションに必要です。 ほとんどの cifs サーバーでは、アドバイザリ バイト範囲のロック要求はまだサポートされていません。 *nobrl* を使用しない場合、cifs 形式の必須なバイト範囲のロックで中断されたアプリケーションでは、次のようなエラー メッセージが発生する可能性があります。
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Azure Files を使用すると、"could not change permissions" (アクセス許可を変更できませんでした) というエラーが発生する

Azure Files プラグインで PostgreSQL を実行すると、次のようなエラーが表示されることがあります。

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

このエラーは、cifs/SMB プロトコルを使用する Azure Files プラグインにより発生します。 cifs/SMB プロトコルを使用している場合、ファイルとディレクトリのアクセス許可をマウント後に変更することはできません。

この問題を解決するには、`subPath` を Azure Disk プラグインと一緒に使用します。 

> [!NOTE] 
> ext3/4 ディスクの種類の場合、ディスクのフォーマット後に lost+found ディレクトリがあります。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>多数の小さなファイルを処理する際、Azure ディスクと比べて Azure Files の待機時間が長くなる

多数の小さなファイルを処理する場合など、Azure ディスクと比較して Azure Files を使用すると待機時間が長くなることがあります。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>ストレージアカウントの [Allow access from selected network]/(選択したネットワークからのアクセスを許可する/) 設定を有効にすると、エラーが発生する

AKS で動的プロビジョニングに使用されるストレージ アカウントの *[Allow access from selected network]/(選択したネットワークからのアクセスを許可する/)* 設定を有効にすると、AKS がファイル共有を作成するときに次のエラーが表示されます。

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

このエラーは、 *[Allow access from selected network]/(選択したネットワークからのアクセスを許可する/)* の設定時に *Kubernetes persistentvolume-controller* が選択したネットワーク上に存在しないことが原因で発生します。

[Azure Files で静的プロビジョニング](azure-files-volume.md)を使用することによって、この問題を軽減できます。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Windows ポッドで Azure Files を再マウントできない

Azure Files がマウントされている Windows ポッドが削除され、同じノードで再作成されるようにスケジュールされている場合、マウントは失敗します。 このエラーは、Azure Files マウントが既にノードにマウントされているために `New-SmbGlobalMapping` コマンドが失敗することが原因で発生します。

例えば、次のようなエラー メッセージが表示される場合があります。

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

この問題は、次のバージョンの Kubernetes で修正されました。

| Kubernetes バージョン | 修正済みのバージョン |
|--|:--:|
| 1.12 | 1.12.6 以上 |
| 1.13 | 1.13.4 以上 |
| 1.14 以上 | 該当なし |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>ストレージ アカウント キーが変更されたため Azure Files マウントが失敗する

ストレージ アカウント キーが変更されている場合、Azure Files マウント エラーが発生することがあります。

Base64 でエンコードされたストレージ アカウント キーを使用して、Azure ファイル シークレット内の `azurestorageaccountkey` フィールドを手動で更新することで軽減できます。

Base64 でストレージ アカウント キーをエンコードするには、`base64` を使用します。 次に例を示します。

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure シークレット ファイルを更新するには、`kubectl edit secret` を使用します。 次に例を示します。

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

数分後、エージェント ノードは更新されたストレージ キーを使用して Azure File のマウントを再試行します。


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>ノード グループ サイズの修正に失敗したため、クラスター オートスケーラーのスケールに失敗しました

クラスター オートスケーラーによってスケールアップまたはスケールダウンされない場合、次のようなエラーが[クラスター オートスケーラー ログ][view-master-logs]に表示されます。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

このエラーは、上流クラスターのオートスケーラー競合状態が原因で発生します。 このような場合、クラスター オートスケーラーは実際にクラスター内にあるものとは異なる値で終了します。 この状態を修正するには、[クラスター オートスケーラー][cluster-autoscaler]を無効にしてから再度有効にします。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>低速のディスクの接続では、`GetAzureDiskLun` の実行に 10 分から 15 分かかり、エラーが発生します

Kubernetes の **1.15.0 より古いバージョン** では、**WaitForAttach でディスクの LUN を見つけることができない** などのエラーが発生する場合があります。  この問題を回避するには、約 15 分間待機してから再試行します。


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>kubernetes.io プレフィックスでノード ラベルを使用すると、Kubernetes 1.16 へのアップグレードが失敗するのはなぜですか

Kubernetes [1.16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) では、kubelet でノードに適用できるのは、[kubernetes.io プレフィックスで定義されているラベルのサブセットのみ](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/)です。 AKS では、影響を受けるワークロードにダウンタイムが発生する可能性があるため、ユーザーの同意なしにアクティブなラベルを自動的に削除することはできません。

そのため、次のようにしてこの問題を回避できます。

1. クラスター コントロール プレーンを 1.16 以降にアップグレードします
2. サポートされていない kubernetes.io ラベルを使用せずに、1.16 以降に新しい nodepoool を追加します
3. 古いノード プールを削除する

AKS により、この軽減策を改善するために、ノード プールでアクティブなラベルを変化させる機能が調査されています。



<!-- LINKS - internal -->
[view-master-logs]: ./view-control-plane-logs.md
[cluster-autoscaler]: cluster-autoscaler.md