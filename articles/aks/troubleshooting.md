---
title: Azure Kubernetes Service に関する一般的な問題のトラブルシューティング
description: Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 56d91d7801c576064b941ac6089a52e74b4a3b7b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540926"
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

既定のネットワーク セキュリティ グループが変更されていないこと、および API サーバーへの接続用にポート 22 が開いていることを確認します。 `kubectl get pods --namespace kube-system` コマンドを使用して、`tunnelfront` ポッドが *kube-system* 名前空間で実行されているかどうかを確認します。 そうでない場合は、ポッドを強制的に削除すると、再起動されます。

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

アクティブなアップグレード操作の実行中、またはアップグレードが試行されたが失敗した場合、クラスターの操作は制限されます。 問題を診断するには、`az aks show -g myResourceGroup -n myAKSCluster -o table` を実行してクラスターの詳細な状態を取得します。 結果に基づいて、次のことを行います。

* クラスターのアップグレードを実行中の場合は、操作が終了するまで待ちます。 アップグレードに成功した場合は、以前に失敗した操作をもう一度試します。
* クラスターのアップグレードに失敗した場合は、上記の手順に従います。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>自分のクラスターを別のサブスクリプションに移したり、自分のクラスターが含まれる自分のサブスクリプションを新しいテナントに移したりできますか?

AKS クラスターを別のサブスクリプションに移したり、サブスクリプションを所有するクラスターを新しいテナントに移したりした場合は、ロールの割り当てやサービス プリンシパルの権限が失われるため、クラスターが機能を失います。 この制約により、**AKS はサブスクリプションまたはテナント間でのクラスターの移動をサポートしていません**。
