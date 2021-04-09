---
title: デプロイのベスト プラクティスを確認する
titleSuffix: Azure Kubernetes Service
description: kube-advisor を使用して Azure Kubernetes Service のご自身のデプロイでベスト プラクティスの実装を確認する方法について説明します
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 7730146f30487eb5d20f0d3138e9e5ba799daa99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94681518"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>クラスターでの Kubernetes のベスト プラクティスを確認する

お使いのアプリケーションで最適なパフォーマンスと回復力を確保するには、Kubernetes のデプロイで従うべきベスト プラクティスがいくつかあります。 kube-advisor ツールを使用すると、これらの推奨事項に従っていないデプロイを探すことができます。

## <a name="about-kube-advisor"></a>kube-advisor について

[kube-advisor ツール][kube-advisor-github]は、お使いのクラスターで動作するように設計された単一のコンテナーです。 このツールを使用すると、ご自身のデプロイに関する情報を Kubernetes API サーバーに照会し、推奨される一連の改善点を取得できます。

kube-advisor ツールは、PodSpecs for Windows アプリケーションおよび Linux アプリケーションに欠けているリソース要求と制限を報告することができますが、kube-advisor ツール自体は Linux ポッドでスケジュールする必要があります。 ポッドの構成で[ノード セレクター][k8s-node-selector]を使用して、特定の OS のノード プールで実行するようにポッドをスケジュールすることができます。

> [!NOTE]
> kube-advisor ツールは、ベストエフォート方式で Microsoft によってサポートされます。 問題と提案は GitHub で提出する必要があります。

## <a name="running-kube-advisor"></a>kube-advisor の実行

[Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC)](./azure-ad-integration-cli.md) 用に構成されたクラスターでこのツールを実行するには、次のコマンドを使用します。 最初のコマンドにより、Kubernetes サービス アカウントが作成されます。 2 番目のコマンドでは、そのサービス アカウントを使用して、ツールがポッドで実行されます。また、そのポッドは、終了後に削除されるように構成されます。 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Kubernetes RBAC を使用していない場合は、次のようにコマンドを実行できます。

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

数秒すると、ご自身のデプロイで改善の可能性がある点を示す表が示されます。

![kube-advisor の出力](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>実行されるチェック

このツールでは、Kubernetes のいくつかのベスト プラクティスについて検証が行われ、それぞれに固有の修正方法が推奨されます。

### <a name="resource-requests-and-limits"></a>リソースの要求と制限

Kubernetes では、[ポッド仕様でのリソースの要求と制限][kube-cpumem]の定義がサポートされます。 要求で定義されるのは、コンテナーの実行に必要な最小 CPU とメモリです。 制限で定義されるのは、許可する必要がある最大 CPU とメモリです。

既定では、ポッド仕様について要求も制限も設定されていません。 これにより負荷の割り当てがノードに集中し、コンテナーの不足が発生する可能性があります。 kube-advisor ツールにより、要求と制限が設定されていないポッドが強調表示されます。

## <a name="cleaning-up"></a>クリーンアップしています

お使いのクラスターで Kubernetes RBAC が有効になっている場合は、ツールを実行した後に次のコマンドを使用して、`ClusterRoleBinding` をクリーンアップできます。

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Kubernetes RBAC が有効になっていないクラスターに対してツールを実行している場合、クリーンアップは不要です。

## <a name="next-steps"></a>次のステップ

- [Azure Kubernetes Service に関する問題のトラブルシューティング](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
