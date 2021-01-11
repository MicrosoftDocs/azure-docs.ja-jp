---
title: Azure Kubernetes Service (AKS) でのクラスター構成
description: Azure Kubernetes Service (AKS) でクラスターを構成する方法について説明します
services: container-service
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: daffcbf0a2ceb6f28cbb539906d4c6387840aa20
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752106"
---
# <a name="configure-an-aks-cluster"></a>AKS クラスターの構成

AKS クラスターの作成の一環として、ニーズに合わせてクラスター構成をカスタマイズすることが必要になる場合があります。 この記事では、AKS クラスターをカスタマイズするためのいくつかのオプションについて説明します。

## <a name="os-configuration-preview"></a>OS 構成 (プレビュー)

AKS では、ノード オペレーティング システム (OS) として Ubuntu 18.04 をプレビュー段階でサポートするようになりました。 プレビュー期間中は、Ubuntu 16.04 と Ubuntu 18.04 の両方を使用できます。

> [!IMPORTANT]
> Kubernetes v1.18 以降で作成されたノード プールは、必要な `AKS Ubuntu 18.04` ノード イメージに既定で設定されます。 サポートされている 1.18 より前の Kubernetes バージョンのノード プールは、ノード イメージとして `AKS Ubuntu 16.04` を受け取りますが、ノード プールの Kubernetes バージョンが v1.18 以降に更新されると、`AKS Ubuntu 18.04` に更新されます。
> 
> 1\.18 以降でクラスターを使用する前に、AKS Ubuntu 18.04 ノード プールでワークロードをテストすることを強くお勧めします。 [Ubuntu 18.04 ノード プールをテストする](#use-aks-ubuntu-1804-existing-clusters-preview)方法を参照してください。

次のリソースがインストールされている必要があります。

- [Azure CLI][azure-cli-install] バージョン 2.2.0 以降
- aks-preview 0.4.35 拡張機能

aks-preview 0.4.35 以降の拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

`UseCustomizedUbuntuPreview` 機能を登録します。

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>新しいクラスターで AKS Ubuntu 18.04 を使用する (プレビュー)

クラスターが作成されるときに Ubuntu 18.04 を使用するようにクラスターを構成します。 `--aks-custom-headers` フラグを使用して、Ubuntu 18.04 を既定の OS として設定します。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

AKS Ubuntu 16.04 イメージを使用してクラスターを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>既存の AKS Ubuntu 18.04 クラスターを使用する (プレビュー)

Ubuntu 18.04 を使用するように新しいノード プールを構成します。 `--aks-custom-headers` フラグを使用して、Ubuntu 18.04 をそのノード プールの既定の OS として設定します。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

AKS Ubuntu 16.04 イメージを使用してノード プールを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。


## <a name="container-runtime-configuration-preview"></a>コンテナー ランタイム構成 (プレビュー)

コンテナー ランタイムは、ノードでコンテナーを実行し、コンテナー イメージを管理するソフトウェアです。 ランタイムにより、Linux または Windows 上でコンテナーを実行するためのシステム コールやオペレーティング システム (OS) 固有の機能の抽象化が容易になります。 現在、AKS では、コンテナー ランタイムとして [Moby](https://mobyproject.org/) (アップストリームの Docker) を使用しています。 
    
![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) は、[OCI](https://opencontainers.org/) (Open Container Initiative) 準拠のコア コンテナー ランタイムです。ノードでコンテナーを実行し、イメージを管理するために必要な最小限の機能セットを提供します。 これは、2017 年 3 月に、Cloud Native Compute Foundation (CNCF) に[寄贈](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/)されました。 AKS で現在使用されている Moby の最新バージョンでは、上記のように、`containerd` が既に利用されており、その上に構築されています。 

containerd ベースのノードとノード プールでは、kubelet は `dockershim` と通信するのではなく、CRI (コンテナー ランタイム インターフェイス) プラグインを介して `containerd` と直接通信するので、Docker CRI 実装と比較して、フローの余分なホップが排除されます。 そのため、ポッドの起動時の待ち時間が短縮され、リソース (CPU とメモリ) 使用量が削減されます。

AKS ノードに `containerd` を使用することで、ポッドの起動時の待ち時間が短縮され、コンテナー ランタイムによるノード リソースの使用量が減少します。 これらの改善は、kubelet が CRI プラグインを介して `containerd` と直接通信するこの新しいアーキテクチャによって実現されます。一方、Moby (Docker) アーキテクチャでは、kubelet は `containerd` に到達する前に `dockershim` および Docker エンジンと通信するため、フローの余分なホップが発生します。

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` は、AKS のすべての GA バージョンの Kubernetes と、v1.10 より後のすべてのアップストリームの Kubernetes バージョンで動作し、Kubernetes と AKS のすべての機能をサポートします。

> [!IMPORTANT]
> AKS で `containerd` の一般提供が開始されたら、これが既定になり、新しいクラスターで使用できるコンテナー ランタイムの唯一のオプションになります。 Moby ノード プールおよびクラスターは、サポートされている古いバージョンのサポートが終了するまで、それらのバージョンで引き続き使用できます。 
> 
> `containerd` でアップグレードしたり、新しいクラスターを作成したりする前に、このコンテナー ランタイムのノード プールでワークロードをテストすることをお勧めします。

### <a name="use-containerd-as-your-container-runtime-preview"></a>コンテナー ランタイムとして `containerd` を使用する (プレビュー)

次の前提条件が必要です。

- [Azure CLI][azure-cli-install] バージョン 2.8.0 以降がインストールされていること
- aks-preview 拡張機能バージョン 0.4.53 以降
- `UseCustomizedContainerRuntime` 機能フラグが登録されていること
- `UseCustomizedUbuntuPreview` 機能フラグが登録されていること

aks-preview 拡張機能 0.4.53 以降をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
az extension list
```

`UseCustomizedContainerRuntime` および `UseCustomizedUbuntuPreview` 機能を登録します。

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>新しいクラスターで `containerd` を使用する (プレビュー)

クラスターの作成時に、`containerd` を使用するようにクラスターを構成します。 `--aks-custom-headers` フラグを使用して、`containerd` をコンテナー ランタイムとして設定します。

> [!NOTE]
> `containerd` ランタイムは、AKS Ubuntu 18.04 イメージを使用するノードおよびノード プールでのみサポートされています。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Moby (Docker) ランタイムを使用してクラスターを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。

### <a name="use-containerd-on-existing-clusters-preview"></a>既存のクラスターで `containerd` を使用する (プレビュー)

`containerd` を使用するように新しいノード プールを構成します。 `--aks-custom-headers` フラグを使用して、`containerd` をそのノード プールのランタイムとして設定します。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Moby (Docker) ランタイムを使用してノード プールを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。


### <a name="containerd-limitationsdifferences"></a>`Containerd` の制限事項と相違点

* コンテナー ランタイムとして `containerd` を使用するには、ベース OS イメージとして AKS Ubuntu 18.04 を使用する必要があります。
* ノードに Docker ツールセットがまだ存在していても、Kubernetes ではコンテナー ランタイムとして `containerd` を使用します。 したがって、ノード上の Kubernetes で作成されたコンテナーは Moby (Docker) によって管理されないため、Docker コマンド (`docker ps` など) や Docker API を使用してコンテナーを表示したり、操作したりすることはできません。
* `containerd` では、Kubernetes ノード上のポッド、コンテナー、コンテナー イメージの**トラブルシューティング**に、Docker CLI ではなく、代替 CLI として [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) を使用することをお勧めします (`crictl ps` など)。 
   * Docker CLI の完全な機能は提供されません。 トラブルシューティングのみを目的としています。
   * `crictl` では、ポッドなどの概念が存在する、Kubernetes により適したコンテナー ビューが提供されます。
* `Containerd` では、標準化された `cri` ログ形式を使用してログを設定します (これは、Docker の JSON ドライバーから現在取得しているものとは異なります)。 ログ ソリューションでは、([Azure Monitor for Containers](../azure-monitor/insights/container-insights-enable-new-cluster.md) のように) `cri` ログ形式をサポートする必要があります。
* Docker エンジン (`/var/run/docker.sock`) にアクセスすることも、Docker-in-Docker (DinD) を使用することもできなくなります。
  * 現在、アプリケーション ログや監視データを Docker エンジンから抽出している場合は、代わりに [Azure Monitor for Containers](../azure-monitor/insights/container-insights-enable-new-cluster.md) などを使用してください。 さらに、AKS では、不安定になる可能性のある、エージェント ノードでの帯域外コマンドの実行はサポートされていません。
  * Moby (Docker) を使用している場合でも、上記の方法でイメージをビルドしたり、Docker エンジンを直接利用したりすることは極力避けてください。 Kubernetes では、使用されたリソースが完全に認識されるわけではなく、これらの方法では、たとえば[こちら](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)と[こちら](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)で詳述されている多くの問題が生じます。
* イメージのビルド - イメージをビルドする際に推奨される方法は、[ACR タスク](../container-registry/container-registry-quickstart-task-cli.md)を使用することです。 別の方法として、[docker buildx](https://github.com/docker/buildx) などのより安全なクラスター内オプションを使用します。

## <a name="generation-2-virtual-machines-preview"></a>第 2 世代仮想マシン (プレビュー)

Azure では、[第 2 世代 (Gen2) 仮想マシン (VM)](../virtual-machines/windows/generation-2.md) がサポートされています。 第 2 世代 VM では、第 1 世代 (Gen1) VM でサポートされていない重要な機能がサポートされています。 これらの機能には、メモリの増加、Intel ソフトウェア ガード エクステンションズ (Intel SGX)、および仮想化された永続メモリ (vPMEM) が含まれます。

第 2 世代 VM では、第 1 世代 VM で使用されている BIOS ベースのアーキテクチャではなく、新しい UEFI ベースのブート アーキテクチャが使用されます。
Gen2 VM は、特定の SKU とサイズでのみサポートされています。 ご自分の SKU で Gen2 がサポートされているかどうかや、Gen2 が必要かどうかを確認するには、[サポートされているサイズの一覧](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)をご覧ください。

また、すべての VM イメージで Gen2 がサポートされているわけでありません。AKS の Gen2 VM では、新しい [AKS Ubuntu 18.04 イメージ](#os-configuration-preview)を使用します。 このイメージでは、すべての Gen2 SKU およびサイズがサポートされています。

プレビュー期間中に Gen2 VM を使用するには、次の要件があります。
- `aks-preview` CLI 拡張機能をインストールする。
- `Gen2VMPreview` 機能フラグを登録する。

`Gen2VMPreview` 機能を登録します。

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

aks-preview CLI 拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
```

aks-preview CLI 拡張機能を更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>新しいクラスターで Gen2 VM を使用する (プレビュー)
クラスターの作成時に、選択した SKU で Gen2 VM を使用するようにクラスターを構成します。 `--aks-custom-headers` フラグを使用して、新しいクラスターの VM の世代として Gen2 を設定します。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

第 1 世代 (Gen1) VM を使用して通常のクラスターを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。 以下のように、Gen1 または Gen2 VM を追加することもできます。

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>既存のクラスターで Gen2 VM を使用する (プレビュー)
Gen2 VM を使用するように新しいノード プールを構成します。 `--aks-custom-headers` フラグを使用して、そのノード プールの VM の世代として Gen2 を設定します。

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

通常の Gen1 ノード プールを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。


## <a name="ephemeral-os-preview"></a>エフェメラル OS (プレビュー)

既定では、Azure 仮想マシンのオペレーティング システム ディスクは、VM を別のホストに再配置する必要がある場合にデータの損失を防ぐために、Azure Storage に自動的にレプリケートされます。 ただし、コンテナーはローカルの状態を永続化するように設計されていないため、この動作の価値は限定的であり、ノードのプロビジョニング速度の低下や読み取り/書き込みの待機時間の増加など、いくつかの欠点があります。

これに対して、エフェメラル OS ディスクは、一時ディスクと同様に、ホスト マシンにのみ格納されます。 これにより、読み取り/書き込みの待機時間が短縮され、ノードのスケーリングやクラスターのアップグレードが高速になります。

一時ディスクと同様に、エフェメラル OS ディスクは仮想マシンの価格に含まれているため、追加のストレージ コストは発生しません。

`EnableEphemeralOSDiskPreview` 機能を登録します。

```azurecli
az feature register --name EnableEphemeralOSDiskPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEphemeralOSDiskPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

aks-preview CLI 拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
```

aks-preview CLI 拡張機能を更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension update --name aks-preview
```

### <a name="use-ephemeral-os-on-new-clusters-preview"></a>新しいクラスターでエフェメラル OS を使用する (プレビュー)

クラスターの作成時に、エフェメラル OS ディスクを使用するようにクラスターを構成します。 `--aks-custom-headers` フラグを使用して、新しいクラスターの OS ディスクの種類としてエフェメラル OS を設定します。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

ネットワークに接続された OS ディスクを使用して通常のクラスターを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。 次に示すように、エフェメラル OS のノード プールを追加することもできます。

### <a name="use-ephemeral-os-on-existing-clusters-preview"></a>既存のクラスターでエフェメラル OS を使用する (プレビュー)
エフェメラル OS ディスクを使用するように新しいノード プールを構成します。 `--aks-custom-headers` フラグを使用して、OS ディスクの種類として、そのノード プールの OS ディスクの種類を設定します。

```azure-cli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

> [!IMPORTANT]
> エフェメラル OS を使用する場合、VM およびインスタンス イメージは、最大で VM キャッシュのサイズまでデプロイできます。 AKS の場合、既定のノードの OS ディスク構成では 100 GiB が使用されます。つまり、100 GiB より大きいキャッシュを持つサイズの VM が必要になります。 既定の Standard_DS2_v2 のキャッシュ サイズは 86 GiB であり、十分な大きさではありません。 Standard_DS3_v2 のキャッシュ サイズは 172 GiB であり、十分な大きさです。 `--node-osdisk-size` を使用して、OS ディスクの既定のサイズを小さくすることもできます。 AKS イメージの最小サイズは 30 GiB です。 

ネットワークに接続された OS ディスクを使用してノード プールを作成する場合は、カスタムの `--aks-custom-headers` タグを省略できます。

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

- `Kured` を使用して、クラスター内の [Linux ノードにセキュリティとカーネルの更新を適用する](node-updates-kured.md)方法について学習する。
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
