---
title: Azure Kubernetes Service (AKS) クラスターのアップグレード
description: Azure Kubernetes Service (AKS) クラスターをアップグレードして最新の機能とセキュリティ更新プログラムを入手する方法について説明します。
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050620"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターのアップグレード

AKS クラスターのライフサイクルの一環として、最新の Kubernetes バージョンへのアップグレードが必要になることはよくあります。 最新の Kubernetes セキュリティ リリースを適用するか、アップグレードして最新の機能を入手することが重要です。 この記事では、AKS クラスター内のマスター コンポーネントまたは 1 つの既定のノード プールをアップグレードする方法について説明します。

複数のノード プールまたは Windows Server ノードを使用する AKS クラスターについては、[AKS 内のノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

> [!WARNING]
> AKS クラスターのアップグレードで、ノードの切断とドレインがトリガーされます。 使用可能なコンピューティング クォータが少ない場合は、アップグレードが失敗する可能性があります。 詳しくは、「[クォータの増加](../azure-portal/supportability/resource-manager-core-quotas-request.md)」をご覧ください。

## <a name="check-for-available-aks-cluster-upgrades"></a>利用できる AKS クラスターのアップグレードを確認する

ご使用のクラスターに利用できる Kubernetes リリースを確認するには、[az aks get-upgrades][az-aks-get-upgrades] コマンドを使用します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myAKSCluster* という名前のクラスターに対して利用できるアップグレードを確認します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> サポートされている AKS クラスターをアップグレードする際に、Kubernetes マイナー バージョンをスキップすることはできません。 たとえば、*1.12.x* -> *1.13.x* または *1.13.x* -> *1.14.x* の間のアップグレードは許可されていますが、*1.12.x* -> *1.14.x* は許可されていません。
>
> *1.12.x* -> *1.14.x* にアップグレードするには、まず *1.12.x* -> *1.13.x* にアップグレードしてから、*1.13.x* -> *1.14.x* にアップグレードします。
>
> 複数のバージョンは、サポートされていないバージョンからサポートされているバージョンにアップグレードする場合にのみスキップできます。 たとえば、サポートされていない *1.10. x* からサポートされている *1.15.x* へのアップグレードは完了できます。

次の出力例は、クラスターをバージョン *1.13.9* と *1.13.10* にアップグレードできることを示しています。

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
アップグレードが利用できない場合は、次のように表示されます。
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>ノード サージ アップグレードのカスタマイズ (プレビュー)

> [!Important]
> ノード サージには、アップグレード操作ごとに、要求された最大サージ カウントに対するサブスクリプション クォータが必要です。 たとえば、クラスターに 5 つのノード プールがあり、そのそれぞれに 4 つのノードが含まれる場合、合計で 20 個のノードがあります。 各ノード プールの最大サージ値が 50% の場合、アップグレードを完了するには、10 ノード (2 ノード * 5 プール) の追加のコンピューティングおよび IP クォータが必要です。
>
> Azure CNI を使用する場合は、サブネット内に使用可能な IP があることと、[Azure CNI の IP 要件を満たしていること](configure-azure-cni.md)を検証します。

AKS は、既定で、1 つの追加ノードを使用してサージ操作するようにアップグレードを構成します。 最大サージ設定の既定値は 1 です。これにより、AKS は、既存のアプリケーションの切断またはドレインの前に追加のノードを作成して古いバージョンのノードを置き換えることにより、ワークロードの中断を最小限に抑えることができます。 最大サージ値をノード プールごとにカスタマイズすると、アップグレードの速度とアップグレードの中断とのトレードオフが可能になります。 最大サージ値を大きくすることでアップグレード プロセスはより速く完了しますが、最大サージに大きな値を設定するとアップグレード プロセス中に中断が発生する可能性があります。 

たとえば、最大サージ値が 100% の場合、(ノード数が 2 倍になり) 可能な限り最速のアップグレード プロセスが提供されますが、ノード プール内のすべてのノードが同時にドレインされます。 テスト環境では、このようなより大きな値を使用することをお勧めします。 運用ノード プールの場合は、max_surge 設定を 33% にすることをお勧めします。

AKS では、最大サージに対して整数値とパーセント値の両方を受け入れます。 たとえば、整数 "5" は、サージする 5 つの追加ノードを示します。 値 "50%" は、プール内の現在のノード数の半分のサージ値を示します。 最大サージ パーセント値には、最低 1%、最大 100% の値を指定できます。 パーセント値は、最も近いノード数に切り上げられます。 アップグレード時に最大サージ値が現在のノード数より小さい場合、現在のノード数が最大サージ値に使用されます。

アップグレード中、最大サージ値には、最小値を 1 とし、最大値をノード プール内のノード数とする値を指定することができます。 より大きな値を設定することもできますが、最大サージに使用されるノードの最大数は、アップグレード時のプール内のノードの数を超えることはありません。

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>ノード サージ アップグレードをカスタマイズするためのプレビュー機能の設定

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

登録には数分かかります。 次のコマンドを使用して、機能が登録されていることを確認します。

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

プレビュー中、最大サージを使用するには、*aks-preview* CLI 拡張機能が必要です。 [az extension add][az-extension-add] コマンドを使用した後、[az extension update][az-extension-update] コマンドを使用して、使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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

AKS クラスターに利用できるバージョンの一覧を参照し、[az aks upgrade][az-aks-upgrade] コマンドを使用してアップグレードします。 アップグレード プロセス中に、AKS は指定された Kubernetes バージョンを実行する新しいノードをクラスターに追加した後、実行中のアプリケーションへの中断を最小限に抑えるために古いノードのいずれかを慎重に[切断およびドレイン][kubernetes-drain]します。 新しいノードが実行中のアプリケーション ポッドとして確認されたら、その古いノードが削除されます。 このプロセスは、クラスター内のすべてのノードがアップグレードされるまで繰り返されます。

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

ノード数にもよりますが、クラスターのアップグレードには数分かかります。

> [!NOTE]
> クラスターのアップグレードについては、完了までの合計許容時間があります。 この時間は `10 minutes * total number of nodes in the cluster` の積を取得することによって計算されます。 たとえば、20 ノードのクラスターでは、アップグレード操作が 200 分で成功する必要があります。それを超えた場合は、AKS によって操作が失敗します。これは、クラスターが回復不能な状態になるのを回避するためです。 アップグレードの失敗から回復するには、タイムアウトに達した後にアップグレード操作を再試行してください。

アップグレードが成功したことを確認するには、[az aks show][az-aks-show] コマンドを使用します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、クラスターが現在 *1.13.10* で実行されていることを示しています。

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
