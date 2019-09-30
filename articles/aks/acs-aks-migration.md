---
title: Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行する
description: Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行します。
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097894"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Azure Container Service (ACS) から Azure Kubernetes Service (AKS) に移行する

この記事は、Kubernetes を使用する Azure Container Service (ACS) と Azure Kubernetes Service (AKS) の間の移行を正しく計画して実行できるよう支援します。 重要な決定を下せるように、このガイドでは、ACS と AKS の違いを詳しく説明し、移行プロセスの概要を示します。

## <a name="differences-between-acs-and-aks"></a>ACS と AKS の違い

ACS と AKS では、移行に影響を与える重要な部分に違いがあります。 移行を行う前に、以下の違いを確認し、対処するように計画する必要があります。

* AKS ノードでは[マネージド ディスク](../virtual-machines/windows/managed-disks-overview.md)が使用されます。
    * アンマネージド ディスクは、AKS ノードにアタッチする前に変換する必要があります。
    * Azure ディスクのカスタム `StorageClass` オブジェクトは、`unmanaged` から `managed` に変更する必要があります
    * すべての `PersistentVolumes` は `kind: Managed` を使用する必要があります。
* AKS では、[複数のノード プール](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (現在プレビュー段階) がサポートされます。
* Windows Server に基づくノードは、現在 [AKS でプレビュー段階](https://azure.microsoft.com/blog/kubernetes-on-azure/)です。
* AKS では、制限された[リージョン](https://docs.microsoft.com/azure/aks/quotas-skus-regions) セットがサポートされます。
* AKS は、ホストされた Kubernetes コントロール プレーンを使用するマネージド サービスです。 以前に ACS マスターの構成を変更した場合は、アプリケーションの変更が必要になる場合があります

## <a name="differences-between-kubernetes-versions"></a>Kubernetes のバージョン間の違い

Kubernetes の新しいバージョンに移行している場合は、次のリソースで Kubernetes におけるバージョン管理方法を理解してください。

* [Kubernetes のバージョンとバージョン スキューのサポート ポリシー](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>移行に関する考慮事項

### <a name="agent-pools"></a>エージェント プール

Kubernetes コントロール プレーンは AKS によって管理されますが、新しいクラスターに含めるノードのサイズと数は引き続きユーザーが定義します。 ACS から AKS に 1 対 1 でマッピングすると仮定すると、既存の ACS ノードの情報をキャプチャする必要があります。 このデータは、新しい AKS クラスターを作成するときに使用します。

例:

| 名前 | Count | VM サイズ | オペレーティング システム |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

移行中に追加の仮想マシンがサブスクリプションに展開されるため、クォータと制限がこれらのリソースに十分であることを確認する必要があります。 

詳細については、[Azure サブスクリプションとサービスの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits) に関する記事を参照してください。 現在のクォータを確認するには、Azure portal で[サブスクリプション ブレード](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して **[使用量 + クォータ]** を選択します。

### <a name="networking"></a>ネットワーク

複雑なアプリケーションの場合は、通常、一度にではなく時間をかけて段階的に移行します。 つまり、古い環境と新しい環境が、ネットワークを介して通信する必要がある可能性があります。 これまで `ClusterIP` サービスを使用して通信していたアプリケーションは、`LoadBalancer` タイプとして公開し、適切にセキュリティで保護することが必要な場合があります。

移行を完了するには、AKS で実行されている新しいサービスをクライアントで参照します。 AKS クラスターの前面に配置された Load Balancer を指すように DNS を更新して、トラフィックをリダイレクトすることをお勧めします。

### <a name="stateless-applications"></a>ステートレス アプリケーション

ステートレス アプリケーションの移行は、最も簡単なケースです。 YAML 定義を新しいクラスターに適用し、すべてが想定どおりに動作していることを確認してから、トラフィックをリダイレクトして新しいクラスターをアクティブにします。

### <a name="stateful-applications"></a>ステートフル アプリケーション

データの損失や予期しないダウンタイムを回避するために、ステートフル アプリケーションの移行は慎重に計画してください。

#### <a name="highly-available-applications"></a>高可用性アプリケーション

一部のステートフル アプリケーションを高可用性構成にデプロイすることができます。 これらのアプリケーションは、レプリカ間でデータをコピーすることができます。 現在このような種類のデプロイを使用している場合は、新しい AKS クラスターに新規メンバーを作成し、下流の呼び出し元に対する影響を最小限にして移行できる可能性があります。 一般に、このシナリオでの一般的な移行手順は次のとおりです。

1. AKS に新しいセカンダリ レプリカを作成します。
2. データがレプリケートされるのを待ちます。
3. フェールオーバーを行ってセカンダリ レプリカを新しいプライマリにします。
4. AKS クラスターにトラフィックを送ります。

#### <a name="migrating-persistent-volumes"></a>永続ボリュームの移行

既存の永続ボリュームを AKS に移行する場合は、通常、次の手順に従います。

1. アプリケーションの書き込みを停止します。 (この手順は省略可能であり、ダウンタイムが必要になります。)
2. ディスクのスナップショットを作成します。
3. スナップショットから新しいマネージド ディスクを作成します。
4. AKS に永続ボリュームを作成します。
5. PersistentVolumeClaims (静的プロビジョニング) ではなく[既存のボリュームを使用する](https://docs.microsoft.com/azure/aks/azure-disk-volume)ようにポッドの指定を更新します。
6. AKS にアプリケーションをデプロイします。
7. 検証します。
8. AKS クラスターにトラフィックを送ります。

> [!IMPORTANT]
> 書き込みを停止しないことを選択した場合は、データを新しいデプロイにレプリケートする必要があります。 そうしないと、ディスクのスナップショットを作成した後に書き込まれたデータが消失します。

マネージド ディスクを作成して Kubernetes クラスター間でのボリュームの移行に役立つオープン ソースのツールが存在します。

* [Azure CLI ディスク コピー拡張機能](https://github.com/noelbundick/azure-cli-disk-copy-extension)は、リソース グループ間および Azure リージョン間でディスクをコピーおよび変換します。
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)は、ACS Kubernetes ボリュームを列挙し、それらを AKS クラスターに移行します。

#### <a name="azure-files"></a>Azure Files

ディスクとは異なり、Azure Files は複数のホストに同時にマウントできます。 AKS クラスターで、Azure と Kubernetes は、ACS クラスターでまだ使用されているポッドが作成されるのを阻止しません。 データの損失や予期しない動作を防ぐために、クラスターが同じファイルに同時に書き込まないようにしてください。

お使いのアプリケーションが、同じファイル共有を指す複数のレプリカをホストできる場合は、ステートレスの移行手順に従って、YAML 定義を新しいクラスターにデプロイしてください。 ホストできない場合に可能な 1 つの移行方法は、次の手順です。

1. レプリカ数を 0 にしてアプリケーションを AKS にデプロイします。
2. AKS でアプリケーションを 0 にスケーリングします。 (この手順にはダウンタイムが必要になります。)
3. AKS でアプリケーションを 1 にスケーリングします。
4. 検証します。
5. AKS クラスターにトラフィックを送ります。

共有を空にして開始し、ソース データのコピーを作成したい場合は、[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) コマンドを使用してデータを移行できます。

### <a name="deployment-strategy"></a>展開戦略

既存の CI/CD パイプラインを使用して、既知の正常な構成を AKS にデプロイすることをお勧めします。 既存のデプロイ タスクを複製し、`kubeconfig` が新しい AKS クラスターを指すようにします。

それが不可能な場合は、ACS からリソース定義をエクスポートして、AKS にそれを適用してください。 オブジェクトをエクスポートするには `kubectl` を使用できます。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

デプロイのニーズに応じて、いくつかのオープン ソース ツールを使用できます。

* [Velero](https://github.com/heptio/ark) (このツールには、Kubernetes 1.7 が必要です。)
* [Azure Kube CLI 拡張機能](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>移行の手順

1. Azure portal、Azure CLI、または Azure Resource Manager テンプレートを使用して [AKS クラスターを作成](https://docs.microsoft.com/azure/aks/create-cluster)します。

   > [!NOTE]
   > GitHub の [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) リポジトリには、AKS 用の Azure Resource Manager テンプレートのサンプルがあります。

2. YAML の定義を必要に応じて変更します。 たとえば、`Deployments` で `apps/v1beta1` を `apps/v1` に置き換えます。

3. ACS クラスターから AKS クラスターに[ボリュームを移行](#migrating-persistent-volumes)します (省略可能)。

4. CI/CD システムを使用して、アプリケーションを AKS にデプロイします。 または、kubectl を使用して YAML 定義を適用します。

5. 検証します。 アプリケーションが意図したとおりに動作していて、移行したデータがコピーされていることを確認します。

6. トラフィックをリダイレクトします。 クライアントが AKS の展開を参照するように DNS を更新します。

7. 移行後のタスクを終了します。 書き込みを停止しないことを選択してボリュームを移行した場合は、そのデータを新しいクラスターにコピーします。
