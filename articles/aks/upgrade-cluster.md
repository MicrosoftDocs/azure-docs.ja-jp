---
title: Azure Kubernetes Service (AKS) クラスターのアップグレード
description: Azure Kubernetes Service (AKS) クラスターをアップグレードして最新の機能とセキュリティ更新プログラムを入手する方法について説明します。
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 11218fc0cd754e9793067c449fdcb7589688dc2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176350"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターのアップグレード

AKS クラスター ライフサイクルの一部には、最新の Kubernetes バージョンへの定期的なアップグレードの実行が含まれます。 最新のセキュリティ リリースを適用するか、アップグレードして最新の機能を入手することが重要です。 この記事では、AKS クラスター内のマスター コンポーネントまたは 1 つの既定のノード プールをアップグレードする方法について説明します。

複数のノード プールまたは Windows Server ノードを使用する AKS クラスターについては、[AKS 内のノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

> [!WARNING]
> AKS クラスターのアップグレードで、ノードの切断とドレインがトリガーされます。 使用可能なコンピューティング クォータが少ない場合は、アップグレードが失敗する可能性があります。 詳しくは、「[クォータの増加](../azure-portal/supportability/resource-manager-core-quotas-request.md)」をご覧ください。

## <a name="check-for-available-aks-cluster-upgrades"></a>利用できる AKS クラスターのアップグレードを確認する

ご使用のクラスターに利用できる Kubernetes リリースを確認するには、[az aks get-upgrades][az-aks-get-upgrades] コマンドを使用します。 次の例では、*myResourceGroup* で *myAKSCluster* への使用可能なアップグレードを確認します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> サポートされている AKS クラスターをアップグレードする際に、Kubernetes マイナー バージョンをスキップすることはできません。 すべてのアップグレードは、メジャー バージョン番号で順番に実行する必要があります。 たとえば、*1.14.x* -> *1.15.x* または *1.15.x* -> *1.16.x* の間のアップグレードは許可されていますが、*1.14.x* -> *1.16.x* は許可されていません。 
> > 複数のバージョンのスキップは、"_サポートされていないバージョン_" から "_サポートされているバージョン_" にアップグレードする場合にのみ可能です。 たとえば、サポートされていない *1.10.x* からサポートされている *1.15.x* へのアップグレードは実行することができます。

次の出力例は、クラスターをバージョン *1.19.1* と *1.19.3* にアップグレードできることを示しています。

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

アップグレードが利用できない場合は、次のメッセージが表示されます。

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>ノード サージ アップグレードのカスタマイズ

> [!Important]
> ノード サージには、アップグレード操作ごとに、要求された最大サージ カウントに対するサブスクリプション クォータが必要です。 たとえば、クラスターに 5 つのノード プールがあり、そのそれぞれに 4 つのノードが含まれる場合、合計で 20 個のノードがあります。 各ノード プールの最大サージ値が 50% の場合、アップグレードを完了するには、10 ノード (2 ノード * 5 プール) の追加のコンピューティングおよび IP クォータが必要です。
>
> Azure CNI を使用する場合は、サブネット内に使用可能な IP があることと、[Azure CNI の IP 要件を満たしていること](configure-azure-cni.md)を検証します。

AKS は、既定で、1 つの追加ノードを使用してサージ操作するようにアップグレードを構成します。 最大サージ設定の既定値は 1 です。これにより、AKS は、既存のアプリケーションの切断またはドレインの前に追加のノードを作成して古いバージョンのノードを置き換えることにより、ワークロードの中断を最小限に抑えることができます。 最大サージ値をノード プールごとにカスタマイズすると、アップグレードの速度とアップグレードの中断とのトレードオフが可能になります。 最大サージ値を大きくすることでアップグレード プロセスはより速く完了しますが、最大サージに大きな値を設定するとアップグレード プロセス中に中断が発生する可能性があります。 

たとえば、最大サージ値が 100% の場合、(ノード数が 2 倍になり) 可能な限り最速のアップグレード プロセスが提供されますが、ノード プール内のすべてのノードが同時にドレインされます。 テスト環境では、このようなより大きな値を使用することをお勧めします。 運用ノード プールの場合は、max_surge 設定を 33% にすることをお勧めします。

AKS では、最大サージに対して整数値とパーセント値の両方を受け入れます。 たとえば、整数 "5" は、サージする 5 つの追加ノードを示します。 値 "50%" は、プール内の現在のノード数の半分のサージ値を示します。 最大サージ パーセント値には、最低 1%、最大 100% の値を指定できます。 パーセント値は、最も近いノード数に切り上げられます。 アップグレード時に最大サージ値が現在のノード数より小さい場合、現在のノード数が最大サージ値に使用されます。

アップグレード中、最大サージ値には、最小値を 1 とし、最大値をノード プール内のノード数とする値を指定することができます。 より大きな値を設定することもできますが、最大サージに使用されるノードの最大数は、アップグレード時のプール内のノードの数を超えることはありません。

> [!Important]
> ノード プールの最大サージ設定は永続的です。  以降の Kubernetes アップグレードまたはノード バージョンのアップグレードでは、この設定が使用されます。 ノード プールの最大サージ値はいつでも変更できます。 運用ノード プールの場合は、最大サージ設定を 33% にすることをお勧めします。

新規または既存のノード プールの最大サージ値を設定するには、次のコマンドを使用します。

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS クラスターのアップグレード

AKS クラスターに利用できるバージョンの一覧を参照し、[az aks upgrade][az-aks-upgrade] コマンドを使用してアップグレードします。 アップグレード プロセス中、AKS によって次のことが行われます。 
- 指定された Kubernetes バージョンを実行するクラスターに 1 つの新しいバッファー ノード (または[最大サージ](#customize-node-surge-upgrade)に構成されている数のノード) が追加されます。 
- 実行中のアプリケーションの中断を最小限に抑えるために、古いノードのいずれかの[切断とドレイン][kubernetes-drain]が実行されます (最大サージを使用している場合は、指定されたバッファー ノードの数と同じ数のノードの[切断とドレイン][kubernetes-drain]が同時に実行されます)。 
- 古いノードが完全にドレインされると、新しいバージョンを受け取るための再イメージ化が実行され、次にアップグレードされるノード用のバッファー ノードになります。 
- このプロセスは、クラスター内のすべてのノードがアップグレードされるまで繰り返されます。 
- プロセスの最後に、最後にバッファー ノードが削除され、既存のエージェント ノードの数とゾーン バランスが維持されます。

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

ノード数にもよりますが、クラスターのアップグレードには数分かかります。

> [!IMPORTANT]
> `PodDisruptionBudgets` (PDB) で一度に少なくとも 1 つのポッド レプリカを確実に移動できるようにします。そうしない場合、ドレインまたは強制削除操作は失敗します。
> ドレイン操作が失敗した場合、アプリケーションが中断されないように、アップグレード操作は設計によって失敗します。 操作を停止させる原因 (間違った PDB やクォータの不足など) を解消し、操作をやり直してください。

アップグレードが成功したことを確認するには、[az aks show][az-aks-show] コマンドを使用します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、クラスターが現在 *1.18.10* で実行されていることを示しています。

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>自動アップグレード チャネルを設定する

クラスターの手動アップグレードに加え、クラスターに自動アップグレード チャネルを設定できます。 次のアップグレード チャネルを使用できます。

|チャネル| アクション | 例
|---|---|---|
| `none`| 自動アップグレードを無効にし、クラスターをその現行バージョンの Kubernetes で維持します。| 既定の設定 (変更されていない場合)|
| `patch`| サポートされる最新版のパッチが利用できるようになったとき、それにクラスターを自動アップグレードします。マイナー バージョンはそのまま維持されます。| たとえば、クラスターでバージョン *1.17.7* を実行しているとき、バージョン *1.17.9*、*1.18.4*、*1.18.6*、*1.19.1* が利用できる場合、クラスターは *1.17.9* にアップグレードされます。|
| `stable`| マイナー バージョン *N-1* でサポートされる最新のパッチ リリースにクラスターが自動アップグレードされます。*N* はサポートされる最新のマイナー バージョンです。| たとえば、クラスターでバージョン *1.17.7* を実行しているとき、バージョン *1.17.9*、*1.18.4*、*1.18.6*、*1.19.1* が利用できる場合、クラスターは *1.18.6* にアップグレードされます。
| `rapid`| サポートされる最新のマイナー バージョンでサポートされる最新のパッチ リリースにクラスターが自動アップグレードされます。| クラスターの Kubernetes バージョンが *N-2* マイナー バージョンの位置にある場合 (*N* はサポートされる最新のマイナー バージョン)、クラスターはまず、*N-1* マイナー バージョンでサポートされる最新のパッチ バージョンにアップグレードされます。 たとえば、クラスターでバージョン *1.17.7* を実行しているとき、バージョン *1.17.9*、*1.18.4*、*1.18.6*、*1.19.1* が利用できる場合、クラスターはまず *1.18.6* にアップグレードされ、その後、*1.19.1* にアップグレードされます。

> [!NOTE]
> クラスターの自動アップグレードは Kubernetes の GA バージョンにのみアップグレードされ、プレビュー バージョンに更新されることはありません。

クラスターの自動アップグレードは、クラスターの手動アップグレードを同じプロセスに従います。 詳細については、「[AKS クラスターのアップグレード][upgrade-cluster]」を参照してください。

AKS クラスターのクラスター自動アップグレードはプレビュー機能です。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

`AutoUpgradePreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

クラスターの作成時に自動アップグレード チャネルを設定するには、次の例のように、*auto-upgrade-channel* パラメーターを使用します。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

既存のクラスターに自動アップグレード チャネルを設定するには、次の例のように、*auto-upgrade-channel* パラメーターを更新します。

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>次のステップ

この記事では、既存の AKS クラスターをアップグレードする方法について説明しました。 AKS クラスターのデプロイと管理の詳細については、一連のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [AKS のチュートリアル][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider#az-provider-register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
