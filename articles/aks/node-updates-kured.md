---
title: kured を使用して Azure Kubernetes Service (AKS) の Linux ノードを更新および再起動する
description: kured を使用して Azure Kubernetes Service (AKS) の Linux ノードを更新し、自動的に再起動する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 580d1316c2bfc6514a148ed6fba78a8e77bd880e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614900"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の Linux ノードにセキュリティとカーネルの更新を適用します

クラスターを保護するために、AKS のノードにセキュリティ更新プログラムが自動的に適用されます。 これらの更新プログラムには、OS のセキュリティ修正プログラムやカーネルの更新プログラムが含まれています。 これらの更新プログラムの中には、プロセスを完了するためにノードの再起動が必要なものがあります。 AKS は、更新プロセスを完了するためにこれらの Linux ノードを自動的に再起動しません。

(現在 AKS でプレビュー段階) の Windows サーバー ノードを最新に保つためのプロセスは少し異なります。 Windows Server ノードは、毎日の更新を受信しません。 代わりに、最新の Windows Server の基本イメージと修正プログラムを含む新しいノードをデプロイする AKS アップグレードを実行します。 Windows Server ノードを使用する AKS クラスターについては、「[AKS でのノード プールのアップグレード][nodepool-upgrade]」をご覧ください。

この記事では、オープンソースの [kured (KUbernetes REboot Daemon)][kured] を使用して、再起動が必要な Linux ノードを監視し、ポッドの実行やノード再起動プロセスのスケジュール変更を自動的に処理する方法について説明します。

> [!NOTE]
> `Kured` は、Weaveworks によるオープンソースのプロジェクトです。 AKS におけるこのプロジェクトのサポートは、ベスト エフォート ベースで提供されます。 追加のサポートについては、#weave-community Slack チャンネルをご利用ください。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、AKS のクイックスタートの、[Azure CLI を使用][aks-quickstart-cli]に関するページと、Azure portal に関するページor [using the Azure portal][aks-quickstart-portal]を参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="understand-the-aks-node-update-experience"></a>AKS ノードの更新エクスペリエンスを理解する

AKS クラスターでは、お客様の Kubernetes ノードが Azure 仮想マシン (VM) として実行されます。 これらは Ubuntu イメージが使用される Linux ベースの VM です。その OS が、更新プログラムの有無を毎晩自動的にチェックするように構成されています。 セキュリティまたはカーネルの更新プログラムが公開された場合、それらが自動的にダウンロードされてインストールされます。

![kured を使用した AKS ノードの更新と再起動のプロセス](media/node-updates-kured/node-reboot-process.png)

カーネルの更新プログラムなど、一部のセキュリティ更新プログラムでは、プロセスの最後にノードを再起動する必要があります。 再起動が必要な Linux ノードでは、 */var/run/reboot-required* という名前のファイルが作成されます。 この再起動プロセスは自動的には実行されません。

お客様独自のワークフローとプロセスを使用してノードの再起動を管理するか、`kured` を使用してプロセスを調整することができます。 `kured` を使用すると、クラスター内の各 Linux ノードでポッドを実行する [DaemonSet][DaemonSet] がデプロイされます。 DaemonSet 内のこれらのポッドによって */var/run/reboot-required* ファイルの存在が監視され、ノードの再起動プロセスが開始されます。

### <a name="node-upgrades"></a>ノードのアップグレード

AKS には別途、クラスターを "*アップグレード*" するためのプロセスがあります。 通常、アップグレードとは、ノードのセキュリティ更新プログラムを適用するだけでなく、より新しいバージョンの Kubernetes に移行することを言います。 AKS のアップグレードでは、次のアクションが実行されます。

* 最新のセキュリティ更新プログラムと Kubernetes バージョンを適用した新しいノードがデプロイされます。
* 以前のノードが切断およびドレインされます。
* ポッドが新しいノードに対してスケジュールされます。
* 以前のノードが削除されます。

アップグレード イベント中、同じ Kubernetes バージョンを維持することはできません。 より新しいバージョンの Kubernetes を指定する必要があります。 最新バージョンの Kubernetes にアップグレードするために、[AKS クラスターをアップグレード][aks-upgrade]できます。

## <a name="deploy-kured-in-an-aks-cluster"></a>AKS クラスターに kured をデプロイする

`kured` DaemonSet をデプロイするには、その GitHub プロジェクト ページから次のサンプル YAML マニフェストを適用します。 このマニフェストによって、ロールおよびクラスター ロール、バインディング、サービス アカウントが作成された後、AKS クラスター 1.9 以降をサポートする `kured` バージョン 1.1.0 を使用して、DaemonSet がデプロイされます。

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

`kured` には、Prometheus や Slack との統合など、追加のパラメーターを構成することもできます。 追加の構成パラメーターの詳細については、[kured のインストール ドキュメント][kured-install]を参照してください。

## <a name="update-cluster-nodes"></a>クラスター ノードを更新する

既定では、AKS の Linux ノードによって更新プログラムの有無が毎晩チェックされます。 待ちたくない場合は、手動で更新を行うことで、`kured` が正常に実行されていることを確認できます。 まず、[お客様の AKS ノードのいずれかに SSH で接続][aks-ssh]する手順に従ってください。 Linux ノードに SSH で接続したら、次のように更新プログラムの有無をチェックして適用します。

```console
sudo apt-get update && sudo apt-get upgrade -y
```

ノードの再起動を必要とする更新プログラムが適用された場合、 */var/run/reboot-required* にファイルが書き込まれます。 既定では、再起動を必要とするノードの有無が `Kured` によって 60 分おきにチェックされます。

## <a name="monitor-and-review-reboot-process"></a>再起動プロセスを監視、確認する

再起動を必要とするノードが DaemonSet 内のいずれかのレプリカによって検出されると、そのノードには、Kubernetes API を通じてロックが設定されます。 このロックによって、そのノードに追加のポッドをスケジュールすることが禁止されます。 また、このロックによって、一度に再起動するノードを 1 つのみとすることが示されます。 ノードが切断された状態で、実行中のポッドがノードからドレインされて、ノードが再起動されます。

ノードの状態は、[kubectl get nodes][kubectl-get-nodes] コマンドを使用して監視することができます。 次の出力例は、再起動のプロセス待ちであるために *SchedulingDisabled* 状態となっているノードを示しています。

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

更新プロセスが完了したら、[kubectl get nodes][kubectl-get-nodes] コマンドに `--output wide` パラメーターを指定してノードの状態を確認できます。 この追加の出力によって、基になるノードの *KERNEL-VERSION* の違いを確認することができます。次の出力例をご覧ください。 前の手順で *aks-nodepool1-28993262-0* が更新され、カーネル バージョンが *4.15.0-1039-azure* になっていることがわかります。 更新されていないノード *aks-nodepool1-28993262-1* は、カーネル バージョンが *4.15.0-1037-azure* であることを示しています。

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>次の手順

この記事では、`kured` を使用し、セキュリティ更新プロセスの一環として Linux ノードを自動的に再起動する方法について説明しました。 最新バージョンの Kubernetes にアップグレードするために、[AKS クラスターをアップグレード][aks-upgrade]できます。

Windows Server ノードを使用する AKS クラスターについては、「[AKS でのノード プールのアップグレード][nodepool-upgrade]」をご覧ください。

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
