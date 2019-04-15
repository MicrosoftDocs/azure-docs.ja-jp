---
title: Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行する
description: Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行する
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 910c96988ec0a8b8aa7b6ac8ce287c4fdc59e177
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649970"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行する

このドキュメントの目的は、Kubernetes を使用する Azure Container Service (ACS) と Azure Kubernetes Service (AKS) の間の移行を正しく計画して実行できるようにすることです。 このガイドでは、ACS と AKS の違いを詳しく説明し、移行プロセスの概要を示し、重要な決定を下せるようにします。

## <a name="differences-between-acs-and-aks"></a>ACS と AKS の違い

ACS と AKS では、移行に影響を与える重要な部分に違いがあります。 移行を行う前に、以下の違いを確認し、対処するように計画する必要があります。

* AKS ノードでは [Managed Disks](../virtual-machines/windows/managed-disks-overview.md) が使用されます
    * アンマネージド ディスクは、AKS ノードに接続する前に変換する必要があります
    * Azure ディスクのカスタム `StorageClass` オブジェクトは、`unmanaged` から `managed` に変更する必要があります
    * すべての `PersistentVolumes` で、`kind: Managed` を使用する必要があります
* 現在、AKS はエージェント プールを 1 つだけサポートしています
* Windows Server ベースのノードは現在で[プライベート プレビュー](https://azure.microsoft.com/blog/kubernetes-on-azure/)段階にあります
* AKS の[サポートされているリージョン](https://docs.microsoft.com/azure/aks/container-service-quotas)の一覧を確認してください
* AKS は、ホストされた Kubernetes コントロール プレーンを使用するマネージド サービスです。 以前に ACS マスターの構成を変更した場合は、アプリケーションの変更が必要な場合があります

### <a name="differences-between-kubernetes-versions"></a>Kubernetes のバージョン間の違い

Kubernetes の新しいバージョンに移行する (例: 1.7.x から 1.9.x に) 場合は、k8s API に対するいくつかの変更に注意する必要があります。

* [ThirdPartyResource を CustomResourceDefinition に移行する](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [バージョン 1.8 および 1.9 でのワークロード API の変更点](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>移行に関する考慮事項

### <a name="agent-pools"></a>エージェント プール

Kubernetes コントロール プレーンは AKS によって管理されますが、新しいクラスターに含めるノードのサイズと数はユーザーがやはり定義します。 ACS から AKS に 1 対 1 でマッピングすると仮定すると、既存の ACS ノードの情報をキャプチャする必要があります。 新しい AKS クラスターを作成するときに、このデータを使用します。

例:

| 名前 | Count | VM サイズ | オペレーティング システム |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 |  Windows |

移行中に追加の仮想マシンがサブスクリプションに展開されるため、クォータと制限がこれらのリソースに十分であることを確認する必要があります。 詳しくは、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)に関するページをご覧ください。 現在のクォータを確認するには、Azure portal で[サブスクリプション ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して、[`Usage + quotas`] を選択します。

### <a name="networking"></a>ネットワーク

複雑なアプリケーションの場合は、通常、一度にではなく時間をかけて段階的に移行します。 つまり、古い環境と新しい環境が、ネットワークを介して通信する必要があります。 これまで `ClusterIP` サービスを使用して通信できたアプリケーションは、`LoadBalancer` タイプとして公開し、適切にセキュリティで保護することが必要な場合があります。

移行を完了するには、AKS で実行されている新しいサービスをクライアントで参照します。 トラフィックをリダイレクトするための推奨される方法は、AKS クラスターの前面に配置された Load Balancer を指すように DNS を更新することです。

### <a name="stateless-applications"></a>ステートレス アプリケーション

ステートレス アプリケーションの移行は、最も簡単なケースです。 YAML 定義を新しいクラスターに適用し、すべてが想定どおりに動作していることを検証してから、トラフィックをリダイレクトして新しいクラスターをアクティブにします。

### <a name="stateful-applications"></a>ステートフル アプリケーション

ステートフル アプリケーションを移行するときは、データの損失や予期しないダウンタイムを回避するために慎重な計画が必要です。

#### <a name="highly-available-applications"></a>高可用性アプリケーション

一部のステートフル アプリケーションは、高可用性構成に展開でき、レプリカの間でデータをコピーすることができます。 現在の展開がそれに該当する場合は、新しい AKS クラスターに新しいメンバーを作成し、下流の呼び出し元に与える影響を最小限にして移行できる可能性があります。 このシナリオでの一般的な移行手順は次のとおりです。

1. AKS に新しいセカンダリ レプリカを作成します
2. データがレプリケートされるのを待ちます
3. フェールオーバーを行ってセカンダリ レプリカを新しいプライマリにします
4. AKS クラスターにトラフィックを送ります

#### <a name="migrating-persistent-volumes"></a>永続ボリュームの移行

既存の永続ボリュームを AKS に移行する場合は、考慮すべき要素がいくつかあります。 一般的な手順は次のとおりです。

1. (省略可能) アプリケーションへの書き込みを停止します (ダウンタイムが必要)
2. ディスクのスナップショットを取得します
3. スナップショットから新しい Managed Disks を作成します
4. AKS で永続ボリュームを作成します
5. PersistentVolumeClaims (静的プロビジョニング) ではなく[既存のボリュームを使用する](https://docs.microsoft.com/azure/aks/azure-disk-volume)ようにポッドの指定を更新します
6. アプリケーションを AKS に展開します
7. 検証
8. AKS クラスターにトラフィックを送ります

> **重要**:書き込みを停止しない場合は、ディスクのスナップショット以降に書き込まれたデータが失われるため、新しいデプロイにデータをレプリケートする必要があります

Managed Disks を作成して Kubernetes クラスター間でボリュームを移行できるオープン ソースのツールが存在します。

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) - リソース グループ間および Azure リージョン間でディスクをコピーおよび変換します
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) - ACS Kubernetes ボリュームを列挙し、それらを AKS クラスターに移行します

#### <a name="azure-files"></a>Azure Files

ディスクとは異なり、Azure Files は複数のホストに同時にマウントできます。 Azure でも Kubernetes でも、ACS クラスターによってまだ使用されている AKS クラスターにポッドを作成できます。 データの損失や予期しない動作を防ぐため、両方のクラスターが同じファイルに同時に書き込まないことを確認する必要があります。

お使いのアプリケーションが、同じファイル共有を指す複数のレプリカをホストできる場合は、ステートレスの移行手順に従って、YAML 定義を新しいクラスターに展開できます。

ホストできない場合に可能な 1 つの移行方法は、次の手順です。

1. レプリカ数を 0 にしてアプリケーションを AKS に展開します
2. ACS でアプリケーションを 0 にスケーリングします (ダウンタイムが必要)
3. AKS でアプリケーションを 1 にスケーリングします
4. 検証
5. AKS クラスターにトラフィックを送ります

共有を空にして開始し、ソース データをコピーしたい場合は、[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) コマンドを使用してデータを移行できます。

### <a name="deployment-strategy"></a>展開戦略

推奨される方法は、既存の CI/CD パイプラインを使用して、既知の正常な構成を AKS に展開することです。 既存の展開タスクを複製し、忘れずに `kubeconfig` が新しい AKS クラスターを指すようにします。

それが不可能な場合は、ACS からリソース定義をエクスポートして、AKS にそれを適用する必要があります。 オブジェクトをエクスポートするには `kubectl` を使用できます。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

ニーズによっては、いくつかのオープン ソース ツールを使用することもできます。

* [heptio/ark](https://github.com/heptio/ark) - k8s 1.7 が必要です
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>移行の手順

### <a name="1-create-an-aks-cluster"></a>1.AKS クラスターの作成

ドキュメントに従って、Azure portal、Azure CLI、または Resource Manager テンプレートを使用して [AKS クラスターを作成する](https://docs.microsoft.com/azure/aks/create-cluster)ことができます。

> GitHub の [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) リポジトリには、AKS 用の Azure Resource Manager テンプレートのサンプルがあります

### <a name="2-modify-applications"></a>2.アプリケーションを変更する

YAML の定義を必要に応じて変更します。 例: `Deployments` の `apps/v1beta1` を `apps/v1` に置き換えます

### <a name="3-optional-migrate-volumes"></a>手順 3.(省略可能) ボリュームを移行する

ACS クラスターから AKS クラスターにボリュームを移行します。 詳しくは、「[永続ボリュームの移行](#migrating-persistent-volumes)」セクションをご覧ください。

### <a name="4-deploy-applications"></a>4.アプリケーションをデプロイする

CI/CD システムを使用してアプリケーションを AKS に展開するか、または kubectl を使用して YAML 定義を適用します。

### <a name="5-validate"></a>5.検証

アプリケーションが意図したとおりに動作していて、移行したデータがコピーされていることを検証します。

### <a name="6-redirect-traffic"></a>6.トラフィックをリダイレクトする

クライアントが AKS の展開を参照するように DNS を更新します。

### <a name="7-post-migration-tasks"></a>7.移行後のタスク

書き込みを停止しないでボリュームを移行した場合は、そのデータを新しいクラスターにコピーする必要があります。
