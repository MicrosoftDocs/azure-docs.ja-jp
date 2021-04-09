---
title: Azure Kubernetes Service (AKS) でのクラスター構成
description: Azure Kubernetes Service (AKS) でクラスターを構成する方法について説明します
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572777"
---
# <a name="configure-an-aks-cluster"></a>AKS クラスターの構成

AKS クラスターの作成の一環として、ニーズに合わせてクラスター構成をカスタマイズすることが必要になる場合があります。 この記事では、AKS クラスターをカスタマイズするためのいくつかのオプションについて説明します。

## <a name="os-configuration"></a>OS 構成

AKS では現在、1.18 以降のバージョンの Kubernetes のクラスターについては、一般提供 (GA) において、既定のノードのオペレーティング システム (OS) として Ubuntu 18.04 をサポートしています。1.18 より前のバージョンの場合、AKS Ubuntu 16.04 は引き続き既定の基本イメージです。 Kubernetes v1.18 以降、既定のベースは AKS Ubuntu 18.04 です。

> [!IMPORTANT]
> Kubernetes v1.18 以降で作成されたノード プールは、`AKS Ubuntu 18.04` ノード イメージに既定で設定されます。 サポートされている 1.18 より前の Kubernetes バージョンのノード プールは、ノード イメージとして `AKS Ubuntu 16.04` を受け取りますが、ノード プールの Kubernetes バージョンが v1.18 以降に更新されると、`AKS Ubuntu 18.04` に更新されます。
> 
> 1\.18 以降でクラスターを使用する前に、AKS Ubuntu 18.04 ノード プールでワークロードをテストすることを強くお勧めします。


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>新しいクラスターで AKS Ubuntu 18.04 (GA) を使用する

Kubernetes v1.18 以降で作成されたクラスターは、`AKS Ubuntu 18.04` ノード イメージに既定で設定されます。 1\.18 より前のサポートされている Kubernetes バージョンのノード プールでは、引き続きノード イメージとして `AKS Ubuntu 16.04` を受け取りますが、クラスターまたはノード プールの Kubernetes バージョンが v1.18 以降に更新されると、`AKS Ubuntu 18.04` に更新されます。

1\.18 以降でクラスターを使用する前に、AKS Ubuntu 18.04 ノード プールでワークロードをテストすることを強くお勧めします。

`AKS Ubuntu 18.04` ノード イメージを使用してクラスターを作成するには、下に示すように、kubernetes v1.18 以降を実行するクラスターを作成するだけです。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>既存のクラスターで AKS Ubuntu 18.04 (GA) を使用する

Kubernetes v1.18 以降で作成されたクラスターは、`AKS Ubuntu 18.04` ノード イメージに既定で設定されます。 1\.18 より前のサポートされている Kubernetes バージョンのノード プールでは、引き続きノード イメージとして `AKS Ubuntu 16.04` を受け取りますが、クラスターまたはノード プールの Kubernetes バージョンが v1.18 以降に更新されると、`AKS Ubuntu 18.04` に更新されます。

1\.18 以降でクラスターを使用する前に、AKS Ubuntu 18.04 ノード プールでワークロードをテストすることを強くお勧めします。

クラスターまたはノード プールで `AKS Ubuntu 18.04` ノード イメージを使用する準備ができている場合は、単純に下のように 1.18 以降にアップグレードできます。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

1 つのノード プールのみをアップグレードする場合は、次のようにします。

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>既存のクラスターで AKS Ubuntu 18.04 (GA) をテストする

Kubernetes v1.18 以降で作成されたノード プールは、`AKS Ubuntu 18.04` ノード イメージに既定で設定されます。 サポートされている 1.18 より前の Kubernetes バージョンのノード プールでは、引き続きノード イメージとして `AKS Ubuntu 16.04` を受け取りますが、ノード プールの Kubernetes バージョンが v1.18 以降に更新されると、`AKS Ubuntu 18.04` に更新されます。

運用ノード プールをアップグレードする前に、AKS Ubuntu 18.04 ノード プールでワークロードをテストすることを強くお勧めします。

`AKS Ubuntu 18.04` ノード イメージを使用してノード プールを作成するには、kubernetes v1.18 以降を実行するノード プールを作成するだけです。 クラスターのコントロール プレーンも、少なくとも v1.18 以降である必要がありますが、他のノード プールは古い kubernetes バージョンのままでも構いません。
下では、まずコントロール プレーンをアップグレードした後、新しいノード イメージ OS バージョンを受け取る新しいノード プールを v1.18 を使用して作成します。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>コンテナー ランタイム構成

コンテナー ランタイムは、ノードでコンテナーを実行し、コンテナー イメージを管理するソフトウェアです。 ランタイムにより、Linux または Windows 上でコンテナーを実行するためのシステム コールやオペレーティング システム (OS) 固有の機能の抽象化が容易になります。 Kubernetes バージョン 1.19 以降のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして `containerd` が使用されます。 Kubernetes v1.19 よりも前のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして [Moby](https://mobyproject.org/) (アップストリーム Docker) が使用されます。

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) は、[OCI](https://opencontainers.org/) (Open Container Initiative) 準拠のコア コンテナー ランタイムです。ノードでコンテナーを実行し、イメージを管理するために必要な最小限の機能セットを提供します。 これは、2017 年 3 月に、Cloud Native Compute Foundation (CNCF) に[寄贈](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/)されました。 AKS で使用されている Moby の最新バージョンでは、上記のように、`containerd` が既に利用されており、その上に構築されています。

`containerd` ベースのノードとノード プールでは、kubelet は `dockershim` と通信するのではなく、CRI (コンテナー ランタイム インターフェイス) プラグインを介して `containerd` と直接通信するので、Docker CRI 実装と比較して、フローの余分なホップが排除されます。 そのため、ポッドの起動時の待ち時間が短縮され、リソース (CPU とメモリ) 使用量が削減されます。

AKS ノードに `containerd` を使用することで、ポッドの起動時の待ち時間が短縮され、コンテナー ランタイムによるノード リソースの使用量が減少します。 これらの改善は、kubelet が CRI プラグインを介して `containerd` と直接通信するこの新しいアーキテクチャによって実現されます。一方、Moby (Docker) アーキテクチャでは、kubelet は `containerd` に到達する前に `dockershim` および Docker エンジンと通信するため、フローの余分なホップが発生します。

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` では、AKS のすべての GA バージョンの Kubernetes と、v1.19 よりも後のすべてのアップストリームの Kubernetes バージョンで動作し、Kubernetes と AKS のすべての機能がサポートされています。

> [!IMPORTANT]
> Kubernetes v1.19 以降で作成されたノード プールを使用するクラスターでは、既定のコンテナー ランタイムとして `containerd` が設定されます。 サポートされている 1.19 よりも前の Kubernetes バージョンのノード プールを使用するクラスターでは、コンテナー ランライムとして `Moby` が受信されますが、ノード プールの Kubernetes バージョンが v1.19 以降に更新されると、`ContainerD` に更新されます。 `Moby` ノード プールおよびクラスターは、サポートされている古いバージョンのサポートが終了するまで、それらのバージョンで引き続き使用できます。
> 
> 1\.19 以降でクラスターを使用する前に、`containerD` を使用する AKS ノード プールでワークロードをテストすることを強くお勧めします。

### <a name="containerd-limitationsdifferences"></a>`Containerd` の制限事項と相違点

* コンテナー ランタイムとして `containerd` を使用するには、ベース OS イメージとして AKS Ubuntu 18.04 を使用する必要があります。
* ノードに Docker ツールセットがまだ存在していても、Kubernetes ではコンテナー ランタイムとして `containerd` を使用します。 したがって、ノード上の Kubernetes で作成されたコンテナーは Moby (Docker) によって管理されないため、Docker コマンド (`docker ps` など) や Docker API を使用してコンテナーを表示したり、操作したりすることはできません。
* `containerd` では、Kubernetes ノード上のポッド、コンテナー、コンテナー イメージの **トラブルシューティング** に、Docker CLI (`crictl ps` など) ではなく、代替 CLI として [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) を使用することをお勧めします。 
   * Docker CLI の完全な機能は提供されません。 トラブルシューティングのみを目的としています。
   * `crictl` では、ポッドなどの概念が存在する、Kubernetes により適したコンテナー ビューが提供されます。
* `Containerd` では、標準化された `cri` ログ形式を使用してログを設定します (これは、Docker の JSON ドライバーから現在取得しているものとは異なります)。 ログ ソリューションでは、([Azure Monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md) のように) `cri` ログ形式をサポートする必要があります。
* Docker エンジン (`/var/run/docker.sock`) にアクセスすることも、Docker-in-Docker (DinD) を使用することもできなくなります。
  * 現在、アプリケーション ログや監視データを Docker エンジンから抽出している場合は、代わりに [Azure Monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md) などを使用してください。 さらに、AKS では、不安定になる可能性のある、エージェント ノードでの帯域外コマンドの実行はサポートされていません。
  * Moby (Docker) を使用している場合でも、上記の方法でイメージをビルドしたり、Docker エンジンを直接利用したりすることは極力避けてください。 Kubernetes では、使用されたリソースが完全に認識されるわけではなく、これらの方法では、たとえば[こちら](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)と[こちら](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)で詳述されている多くの問題が生じます。
* イメージの構築 - AKS クラスター内でイメージを構築する場合を除き、現在の Docker ビルド ワークフローを通常どおりに引き続き使用できます。 この場合は、[ACR タスク](../container-registry/container-registry-quickstart-task-cli.md)を使用してイメージを構築するための推奨される方法に切り替えるか、[docker buildx](https://github.com/docker/buildx) のようなより安全なクラスター内オプションを選択することを検討してください。

## <a name="generation-2-virtual-machines"></a>第 2 世代仮想マシン

Azure では、[第 2 世代 (Gen2) 仮想マシン (VM)](../virtual-machines/generation-2.md) がサポートされています。 第 2 世代 VM では、第 1 世代 (Gen1) VM でサポートされていない重要な機能がサポートされています。 これらの機能には、メモリの増加、Intel ソフトウェア ガード エクステンションズ (Intel SGX)、および仮想化された永続メモリ (vPMEM) が含まれます。

第 2 世代 VM では、第 1 世代 VM で使用されている BIOS ベースのアーキテクチャではなく、新しい UEFI ベースのブート アーキテクチャが使用されます。
Gen2 VM は、特定の SKU とサイズでのみサポートされています。 ご自分の SKU で Gen2 がサポートされているかどうかや、Gen2 が必要かどうかを確認するには、[サポートされているサイズの一覧](../virtual-machines/generation-2.md#generation-2-vm-sizes)をご覧ください。

また、すべての VM イメージで Gen2 がサポートされているわけでありません。AKS の Gen2 VM では、新しい [AKS Ubuntu 18.04 イメージ](#os-configuration)を使用します。 このイメージでは、すべての Gen2 SKU およびサイズがサポートされています。

## <a name="ephemeral-os"></a>エフェメラル OS

既定では、VM を別のホストに再配置する必要がある場合にデータの損失を防ぐために、Azure によって仮想マシンのオペレーティング システム ディスクが Azure Storage に自動的にレプリケートされます。 ただし、コンテナーはローカルの状態を永続化するように設計されていないため、この動作の価値は限定的であり、ノードのプロビジョニング速度の低下や読み取り/書き込みの待機時間の増加など、いくつかの欠点があります。

これに対して、エフェメラル OS ディスクは、一時ディスクと同様に、ホスト マシンにのみ格納されます。 これにより、読み取り/書き込みの待機時間が短縮され、ノードのスケーリングやクラスターのアップグレードが高速になります。

一時ディスクと同様に、エフェメラル OS ディスクは仮想マシンの価格に含まれているため、追加のストレージ コストは発生しません。

> [!IMPORTANT]
>ユーザーが OS のマネージド ディスクを明示的に要求していない場合、AKS は可能であれば指定された nodepool 構成で、既定でエフェメラル OS になります。

エフェメラル OS を使用する場合、OS ディスクは VM キャッシュに格納されている必要があります。 VM キャッシュのサイズは、[Azure のドキュメント](../virtual-machines/dv3-dsv3-series.md)で IO スループットの横の括弧内 ("キャッシュ サイズは GiB 単位") に記載されています。

既定の OS ディスク サイズが 100 GB の AKS の既定の VM サイズである Standard_DS2_v2 を例に取ると、この VM サイズではエフェメラル OS がサポートされますが、キャッシュ サイズはわずか 86 GB になります。 ユーザーが明示的に指定しない場合、この構成はマネージド ディスクに既定で設定されます。 ユーザーがエフェメラル OS を明示的に要求した場合は、検証エラーが発生します。

OS ディスクが 60 GB の同じ Standard_DS2_v2 をユーザーが要求した場合、この構成は既定でエフェメラル OS になります。要求されたサイズである 60 GB は、最大キャッシュ サイズの 86 GB を下回っています。

OS ディスクが 100 GB の Standard_D8s_v3 を使用すると、この VM サイズはエフェメラル OS をサポートし、キャッシュ領域は 200 GB となります。 ユーザーが OS ディスクの種類を指定していない場合、nodepool は既定でエフェメラル OS を受け取ります。 

エフェメラル OS には、バージョン 2.15.0 以上の Azure CLI が必要です。

### <a name="use-ephemeral-os-on-new-clusters"></a>新しいクラスターでエフェメラル OS を使用する

クラスターの作成時に、エフェメラル OS ディスクを使用するようにクラスターを構成します。 `--node-osdisk-type` フラグを使用して、新しいクラスターの OS ディスクの種類としてエフェメラル OS を設定します。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

ネットワークに接続された OS ディスクを使用して通常のクラスターを作成する場合は、`--node-osdisk-type=Managed` を指定することで行うことができます。 次に示すように、エフェメラル OS のノード プールを追加することもできます。

### <a name="use-ephemeral-os-on-existing-clusters"></a>既存のクラスターでエフェメラル OS を使用する
エフェメラル OS ディスクを使用するように新しいノード プールを構成します。 `--node-osdisk-type` フラグを使用して、OS ディスクの種類として、そのノード プールの OS ディスクの種類を設定します。

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> エフェメラル OS を使用する場合、VM およびインスタンス イメージは、最大で VM キャッシュのサイズまでデプロイできます。 AKS の場合、既定のノードの OS ディスク構成では 128 GB が使用されます。つまり、128 GB より大きいキャッシュを持つサイズの VM が必要になります。 既定の Standard_DS2_v2 のキャッシュ サイズは 86 GB であり、十分な大きさではありません。 Standard_DS3_v2 のキャッシュ サイズは 172 GB であり、十分な大きさです。 `--node-osdisk-size` を使用して、OS ディスクの既定のサイズを小さくすることもできます。 AKS イメージの最小サイズは 30 GB です。 

ネットワークに接続された OS ディスクを使用してノード プールを作成する場合は、`--node-osdisk-type Managed` を指定することで行うことができます。

## <a name="custom-resource-group-name"></a>カスタム リソース グループ名

Azure Kubernetes Service クラスターを Azure にデプロイすると、ワーカー ノードに 2 つ目のリソース グループが作成されます。 既定では、AKS によってノード リソース グループに `MC_resourcegroupname_clustername_location` という名前が設定されますが、独自の名前を指定することもできます。

独自のリソース グループ名を指定するには、aks-preview Azure CLI 拡張機能バージョン 0.3.2 以降をインストールします。 Azure CLI で `az aks create` コマンドの `--node-resource-group` パラメーターを使用して、リソース グループのカスタム名を指定します。 Azure Resource Manager テンプレートを使用して AKS クラスターをデプロイする場合は、`nodeResourceGroup` プロパティを使用してリソース グループ名を定義できます。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

セカンダリ リソース グループは、自分のサブスクリプションの Azure リソース プロバイダーによって自動的に作成されます。 クラスターが作成されるときにのみ、カスタムのリソース グループ名を指定できます。 

ノード リソース グループを使うときは、次のことができないので注意してください。

- ノード リソース グループに対して既存のリソース グループを指定すること。
- ノード リソース グループに対して異なるサブスクリプションを指定すること。
- クラスターが作成された後で、ノード リソース グループ名を変更すること。
- ノード リソース グループ内の管理対象リソースに名前を指定すること。
- ノード リソース グループ内の管理対象リソースの Azure で作成されたタグを変更または削除すること。

## <a name="next-steps"></a>次のステップ

- クラスター内の[ノード イメージをアップグレード方法](node-image-upgrade.md)を学習する。
- 「[Azure Kubernetes Service (AKS) クラスターのアップグレード](upgrade-cluster.md)」を参照し、クラスターを最新バージョンの Kubernetes にアップグレードする方法について学習する。
- [`containerd` と Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/) の詳細を確認する。
- [AKS についてよく寄せられる質問](faq.md)に関する記事の一覧を参照し、AKS についての一般的な質問への回答を確認する。
- [エフェメラル OS ディスク](../virtual-machines/ephemeral-os-disks.md)の詳細を確認する。


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
