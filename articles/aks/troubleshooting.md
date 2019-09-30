---
title: Azure Kubernetes Service に関する一般的な問題のトラブルシューティング
description: Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 6ff273236f9f8465de9ec0cda89ed3ff8996ecec
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932660"
---
# <a name="aks-troubleshooting"></a>AKS のトラブルシューティング

Azure Kubernetes Service (AKS) クラスターを作成または管理するときに、問題が発生することがあります。 この記事では、よくある問題とトラブルシューティングの手順について詳しく説明します。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常、Kubernetes の問題のデバッグに関する情報はどこにありますか。

[Kubernetes クラスターのトラブルシューティングに関する公式ガイド](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)をご覧ください。
Microsoft のエンジニアによって公開された、ポッド、ノード、クラスター、他の機能のトラブルシューティングに関する[トラブルシューティング ガイド](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)もあります。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>作成またはアップグレード中に、"クォータ超過" エラーが発生します。 どうすればよいですか。 

[コアを要求する](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)必要があります。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS におけるノードあたりの最大ポッド数はいくつに設定されていますか。

Azure portal で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 30 に設定されます。
Azure CLI で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 110 に設定されます。 (最新バージョンの Azure CLI を使用していることを確認してください)。 この既定の設定は、`az aks create` コマンドで `–-max-pods` フラグを使用して変更することができます。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>高度なネットワークで AKS クラスターをデプロイしているときに、insufficientSubnetSize エラーが発生します。 どうすればよいですか。

Azure CNI (高度なネットワーク) を使用した場合、構成された 1 ノードあたりの "最大ポッド数" に基づいて、AKS によって IP アドレスが事前に割り当てられます。 AKS クラスター内では、1 個から 110 個までの範囲で、いくつでもノードを使用できます。 ノードあたりの構成された最大ポッド数に基づくと、サブネットのサイズは、"ノード数とノードあたりの最大ポッド数の積" より大きくする必要があります。 これを表した基本的な式は次のようになります。

サブネットのサイズ > クラスター内のノードの数 (今後のスケーリングの要件を考慮して) * ノードあたりの最大ポッド数。

詳しくは、「[クラスターの IP アドレス指定を計画する](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)」を参照してください。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>ポッドが CrashLoopBackOff モードでスタックします。 どうすればよいですか。

このモードでポッドがスタックする理由は複数あります。 以下を確認します。

* ポッド自体。`kubectl describe pod <pod-name>` を使用します。
* ログ。`kubectl log <pod-name>` を使用します。

ポッドの問題のトラブルシューティング方法について詳しくは、「[Debug applications (アプリケーションをデバッグする)](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)」をご覧ください。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>既存のクラスターで RBAC を有効にしようとしています。 どうすればいいですか。

残念ながら、現時点では、既存のクラスターでロールベースのアクセス制御 (RBAC) を有効にすることはできません。 新しいクラスターを明示的に作成する必要があります。 CLI を使用する場合、RBAC は既定で有効になります。 AKS ポータルを使用する場合は、RBAC を有効にするトグル ボタンを作成ワークフローで使用できます。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Azure CLI で既定の設定を使用して、または Azure portal を使用して、RBAC を有効にしたクラスターを作成しましたが、Kubernetes ダッシュボードに多数の警告が表示されます。 以前ダッシュボードは警告なしで動作していました。 どうすればよいですか。

ダッシュボードに警告が表示されるのは、クラスターで RBAC が有効になっており、クラスターへのアクセスが既定で無効になっているためです。 ダッシュボードを、クラスターのすべてのユーザーに既定で公開すると、セキュリティの脅威につながる可能性があるため、一般的にこのアプローチは適切な方法です。 それでもダッシュボードを有効にする場合は、[こちらのブログ投稿](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)の手順に従ってください。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>ダッシュボードに接続できません。 どうすればよいですか。

クラスター外にあるサービスにアクセスする最も簡単な方法は、`kubectl proxy` を実行することです。これにより、自分の localhost ポート 8001 に送信された要求が Kubernetes API サーバーにプロキシされます。 そこからは、API サーバーがユーザーのサービス `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default` にプロキシできます。

Kubernetes ダッシュボードが表示されない場合は、`kube-proxy` ポッドが `kube-system` 名前空間で実行されているかどうか確認します。 実行状態でない場合は、ポッドを削除します。これにより再起動されます。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs を使用してログを取得できません。または、API サーバーに接続できません。 "Error from server: error dialing backend: dial tcp…" (サーバーからのエラー: バックエンドへのダイヤルでのエラー: tcp にダイヤル...) と表示されます。 どうすればよいですか。

既定のネットワーク セキュリティ グループが変更されていないこと、および API サーバーへの接続用にポート 22 とポート 9000 の両方が開放されていることを確認します。 `kubectl get pods --namespace kube-system` コマンドを使用して、`tunnelfront` ポッドが *kube-system* 名前空間で実行されているかどうかを確認します。 そうでない場合は、ポッドを強制的に削除すると、再起動されます。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>アップグレードまたはスケーリングを行おうとすると、"メッセージ:プロパティ 'imageReference' は変更できませ" というエラーが発生します。 この問題を解決するにはどうすればよいですか。

AKS クラスター内のエージェント ノードのタグを変更したことが原因で、このエラーが発生している可能性があります。 MC_* リソース グループのリソースのタグやその他のプロパティを変更または削除すると、予期しない結果につながる可能性があります。 AKS クラスターの MC_* グループでリソースを変更すると、サービス レベル目標 (SLO) が中断されます。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-cluster-failed に基づいています。*

このエラーは、複数の理由でクラスターがエラー状態になったときに発生します。 以前に失敗した操作を再試行する前に、次の手順に従ってクラスターのエラー状態を解決してください。

1. クラスターが `failed` 状態から回復するまで、`upgrade` 操作と `scale` 操作は成功しません。 一般的な根本問題と解決策は次のとおりです。
    * **計算 (CRP) クォータが不足**している状態でのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-supportability/resource-manager-core-quotas-request.md)に従って計算クォータの引き上げを依頼します。
    * 高度なネットワーク リソースと**不十分なサブネット (ネットワーク) リソース**を使用したクラスターのスケーリング。 これを解決するには、まず、クォータの範囲内で安定した目標状態にクラスターをスケールバックします。 次に、最初のクォータ制限を超えて再度スケールアップを試みる前に、[こちらの手順](../azure-resource-manager/resource-manager-quota-errors.md#solution)に従ってリソース クォータの引き上げを依頼します。
2. アップグレードの失敗の根本原因が解決されると、クラスターは成功状態になるはずです。 成功状態が確認されたら、元の操作を再試行します。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>クラスターをアップグレードまたはスケーリングしようとしたときに、クラスターが現在アップグレード中であるか、アップグレードに失敗したというエラーが表示されます。

*このトラブルシューティングの支援は、 https://aka.ms/aks-pending-upgrade に基づいています。*

1 つのノード プールを持つクラスターまたは[複数のノード プール](use-multiple-node-pools.md)を持つクラスターでのアップグレード操作とスケール操作は、相互に排他的です。 クラスターまたはノード プールを同時にアップグレードやスケーリングすることはできません。 代わりに、ターゲット リソースに対する次の要求を行う前に、各操作の種類をその同じリソースで完了する必要があります。 その結果、アクティブなアップグレード操作またはスケール操作の実行中、または操作が試行されたが失敗した場合、操作は制限されます。 

問題の診断に役立てるには、`az aks show -g myResourceGroup -n myAKSCluster -o table` を実行してクラスターの詳細な状態を取得します。 結果に基づいて、次のことを行います。

* クラスターのアップグレードを実行中の場合は、操作が終了するまで待ちます。 成功した場合は、もう一度、以前に失敗した操作を再試行します。
* クラスターのアップグレードに失敗した場合は、上記のセクションで概説された手順に従います。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>自分のクラスターを別のサブスクリプションに移したり、自分のクラスターが含まれる自分のサブスクリプションを新しいテナントに移したりできますか?

AKS クラスターを別のサブスクリプションに移したり、サブスクリプションを所有するクラスターを新しいテナントに移したりした場合は、ロールの割り当てやサービス プリンシパルの権限が失われるため、クラスターが機能を失います。 この制約により、**AKS はサブスクリプションまたはテナント間でのクラスターの移動をサポートしていません**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>仮想マシン スケール セットを必要とする機能を使用しようとするとエラーが発生します

*このトラブルシューティングの支援は、aka.ms/aks-vmss-enablement に基づいています*

次の例など、AKS クラスターが仮想マシン スケール セット上にないことを示すエラーが表示されます。

**AgentPool 'agentpool' は自動スケールが有効に設定されていますが、Microsoft Azure Virtual Machine Scale Sets 上にありません**

クラスター オートスケーラーや複数ノード プールなどの機能を使用するには、仮想マシン スケール セットを使用する AKS クラスターを作成する必要があります。 仮想マシン スケール セットに依存する機能を使用しようとして、通常の非仮想マシン スケール セットの AKS クラスターを対象とする場合、エラーが返されます。 仮想マシン スケール セットのサポートは現在 AKS でプレビューの段階です。

適切なドキュメントの「*開始する前に*」に従って、仮想マシン スケール セット機能のプレビューに正しく登録し、AKS クラスターを作成します。

* [クラスター オートスケーラーを使用する](cluster-autoscaler.md)
* [複数のノード プールを作成し使用する](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS リソースおよびパラメーターにはどのような名前付けの制約が適用されますか。

*このトラブルシューティングの支援は、aka.ms/aks-naming-rules に基づいています*

名前付けの制約は、Azure プラットフォームと AKS の両方によって実装されます。 リソース名またはパラメーターがこれらのいずれかの制約に違反した場合、別の入力を行うように求めるエラーが返されます。 次の一般的な名前付けのガイドラインが適用されます。

* AKS *MC_* リソース グループ名は、リソース グループ名とリソース名を結合します。 `MC_resourceGroupName_resourceName_AzureRegion` の自動生成された構文は、80 文字以内にする必要があります。 必要な場合は、リソース グループ名または AKS クラスター名の長さを短くします。
* *dnsPrefix* の最初と最後は英数字の値にする必要があります。 有効な文字には英数字の値とハイフン (-) が含まれます。 *dnsPrefix* にはピリオド (.) などの特殊文字を含めることはできません。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>クラスターを作成、更新、スケーリング、削除、またはアップグレードしようとすると、別の操作が進行中のためその操作は許可されませんというエラーを受け取ります。

*このトラブルシューティングの支援は、aka.ms/aks-pending-operation に基づいています*

クラスター操作は、前の操作がまだ進行中のときには制限されます。 クラスターの詳細な状態を取得するには、`az aks show -g myResourceGroup -n myAKSCluster -o table` コマンドを使用します。 必要に応じて、独自のリソース グループと AKS クラスター名を使用します。

次のクラスター状態の出力に基づきます。

* クラスターが、 *[成功]* または *[失敗]* 以外のプロビジョニング状態の場合は、操作 (*アップグレード / 更新 / 作成 / スケーリング / 削除 / 移行*) が終了するまで待ってください。 前の操作が完了したら、最新のクラスターの操作を再試行します。

* クラスターでアップグレードが失敗している場合は、 「[クラスターがエラー状態であり、状態が修正されるまで、アップグレードもスケーリングも機能しないというエラーが表示されます](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)」で概説されている手順に従ってください。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>既存のクラスターを渡さずに新しいクラスターを作成しようとすると、サービス プリンシパルが見つからなかったというエラーが発生します。

AKS クラスターの作成時には、ユーザーに代わってリソースを作成するためのサービス プリンシパルが必要です。 AKS は、クラスターの作成時に新しいクラスターを作成する機能を提供しますが、クラスターの作成を成功させるには、Azure Active Directory で新しいサービス プリンシパルを適切な時間内に完全に伝達する必要があります。 この伝達に時間がかかりすぎると、クラスターは使用可能なサービス プリンシパルを見つけることができないため、作成の検証に失敗します。 

これについては次の回避策を使用してください。
1. リージョン間で既に伝達されていて、クラスターの作成時に AKS に渡すために存在する既存のサービス プリンシパルを使用します。
2. 自動化スクリプトを使用する場合は、サービス プリンシパルの作成と AKS クラスターの作成の間に遅延時間を追加します。
3. Azure portal を使用する場合は、作成中にクラスター設定に戻り、数分後に検証ページを再試行します。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>エグレス トラフィックを制限した後、エラーが表示されました

AKS クラスターからのエグレス トラフィックを制限するときには、AKS 向けの[必須および任意の推奨される](limit-egress-traffic.md)送信ポート、ネットワーク規則と FQDN、アプリケーション規則があります。 ご利用の設定がこのような規則と競合するとき、一部の `kubectl` コマンドを実行できないことがあります。 また、AKS クラスターの作成時にエラーが表示されることがあります。

必須または任意の推奨される送信ポート、ネットワーク規則と FQDN、およびアプリケーション規則のいずれにもご利用の設定が競合していないことを確認します。
