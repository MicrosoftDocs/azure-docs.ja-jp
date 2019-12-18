---
title: Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続
description: トラブルシューティングやメンテナンスのタスクのために Azure Kubernetes Service (AKS) クラスター ノードとの SSH 接続を作成する方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/31/2019
ms.author: mlearned
ms.openlocfilehash: 5ff79dc597571f4e6ef3d7c2c20bce61c0d061ad
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926370"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>メンテナンスまたはトラブルシューティングのために SSH を使用して Azure Kubernetes Service (AKS) クラスター ノードに接続する

Azure Kubernetes Service (AKS) クラスターのライフサイクル全体を通して、AKS ノードへのアクセスが必要になる場合があります。 メンテナンス、ログ収集、その他のトラブルシューティング操作のための接続です。 Windows Server ノードを含め (現在 AKS ではプレビュー段階)、AKS ノードには SSH を使用してアクセスできます。 [リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続する][aks-windows-rdp]こともできます。 セキュリティのため、AKS ノードはインターネットに公開されません。 AKS ノードに SSH 接続するには、プライベート IP アドレスを使用します。

この記事では、プライベート IP アドレスを使用して、AKS ノードとの SSH 接続を作成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

既定では、SSH キーが取得または生成され、AKS クラスターを作成するときにノードに追加されます。 この記事では、AKS クラスターの作成時に使用した SSH キーとは別の SSH キーを指定する方法について説明します。 この記事では、ノードのプライベート IP アドレスを特定し、SSH を使用してそれに接続する方法についても説明します。 別の SSH キーを指定する必要がない場合は、SSH 公開キーをノードに追加する手順を省略できます。

この記事では、SSH キーを持っていることも前提とします。 [macOS または Linux][ssh-nix]、あるいは [Windows][ssh-windows] を使用して、SSH キーを作成できます。 PuTTY を使用してキーの組を作成する場合、そのキーの組は、既定の PuTTy 秘密キー形式 (.ppk ファイル) ではなく、OpenSSH 形式で保存します。

また、Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>SSH アクセス用の仮想マシン スケール セット ベースの AKS クラスターを構成する

SSH アクセス用の仮想マシン スケール セットを構成するには、クラスターの仮想マシン スケール セットの名前を見つけ、そのスケール セットに SSH 公開キーを追加します。

[az aks show][az-aks-show] コマンドを使用して AKS クラスターのリソース グループ名を取得した後、[az vmss list][az-vmss-list] コマンドを使用してスケール セットの名前を取得します。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

上の例では、*myResourceGroup* 内の *myAKSCluster* のクラスター リソース グループの名前に *CLUSTER_RESOURCE_GROUP* を割り当てています。 次に、例では、*CLUSTER_RESOURCE_GROUP* を使用してスケール セット名を表示し、それを *SCALE_SET_NAME* に割り当てています。

> [!IMPORTANT]
> 現時点では、仮想マシン スケールセットに基づく AKS クラスターについては、Azure CLI を使用して SSH キーを更新する必要があります。
> 
> 現在、Linux ノードについては、Azure CLI を使用した場合にのみ SSH キーを追加できます。 SSH を使用して Windows Server ノードに接続する場合は、AKS クラスターの作成時に指定した SSH キーを使用し、SSH 公開キーを追加するための次の一連のコマンドをスキップします。 このセクションの最後のコマンドに示すように、トラブルシューティングを行うノードの IP アドレスが必要です。 または、SSH を使用する代わりに、[リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続する][aks-windows-rdp]ことができます。

SSH キーを仮想マシン スケール セット内のノードに追加するには、[az vmss extension set][az-vmss-extension-set] コマンドと [az vmss update-instances][az-vmss-update-instances] コマンドを使用します。

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

上の例では、前のコマンドの *CLUSTER_RESOURCE_GROUP* 変数と *SCALE_SET_NAME* 変数が使用されています。 上の例では、SSH 公開キーの場所として *~/.ssh/id_rsa.pub* も使用されています。

> [!NOTE]
> AKS ノードの既定のユーザー名は *azureuser* です。

SSH 公開キーをスケール セットに追加した後、そのスケール セット内のノード仮想マシンに IP アドレスを使用して SSH 接続できます。 [kubectl get コマンド][kubectl-get] を使用して、AKS クラスター ノードのプライベート IP アドレスを表示します。

```console
kubectl get nodes -o wide
```

次の出力例は、Windows Server ノードなど、クラスター内のすべてのノードの内部 IP アドレスを示しています。

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

トラブルシューティングを行うノードの内部 IP アドレスを記録します。

SSH を使用してノードにアクセスするには、「[SSH 接続を作成する](#create-the-ssh-connection)」の手順に従います。

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>SSH アクセス用の仮想マシン可用性セット ベースの AKS クラスターを構成する

SSH アクセス用の仮想マシン可用性セット ベースの AKS クラスターを構成するには、クラスターの Linux ノードの名前を見つけ、そのスケール セットに SSH 公開キーを追加します。

[az aks show][az-aks-show] コマンドを使用して AKS クラスターのリソース グループ名を取得した後、[az vmss list][az-vm-list] コマンドを使用して、クラスターの Linux ノードの仮想マシンの名前を表示します。

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上の例では、*myResourceGroup* 内の *myAKSCluster* のクラスター リソース グループの名前に *CLUSTER_RESOURCE_GROUP* を割り当てています。 その後、この例では、*CLUSTER_RESOURCE_GROUP* を使用して、仮想マシン名を一覧表示しています。 出力例には、仮想マシンの名前が表示されています。

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

ノードに SSH キーを追加するには、[az vm user update][az-vm-user-update] コマンドを使用します。

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

上の例では、前のコマンドの *CLUSTER_RESOURCE_GROUP* 変数とノードの仮想マシン名が使用されています。 上の例では、SSH 公開キーの場所として *~/.ssh/id_rsa.pub* も使用されています。 パスを指定する代わりに、SSH 公開キーの内容を使用することもできます。

> [!NOTE]
> AKS ノードの既定のユーザー名は *azureuser* です。

SSH 公開キーをノードの仮想マシンに追加した後、その仮想マシンに IP アドレスを使用して SSH 接続できます。 [az vm list-ip-addresses][az-vm-list-ip-addresses] コマンドを使用して、AKS クラスター ノードのプライベート IP アドレスを表示します。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

上の例では、前のコマンドに設定された *CLUSTER_RESOURCE_GROUP* 変数が使用されています。 次の出力例では AKS ノードのプライベート IP アドレスが示されています。

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>SSH 接続を作成する

AKS ノードへの SSH 接続を作成するには、AKS クラスターでヘルパー ポッドを実行します。 このヘルパー ポッドでは、クラスターへの SSH アクセスが提供され、続いて追加の SSH ノード アクセスが提供されます。 このヘルパー ポッドを作成して使用するには、次の手順のようにします。

1. `debian` コンテナー イメージを実行し、ターミナル セッションをそれにアタッチします。 このコンテナーを使用して、AKS クラスター内の任意のノードとの SSH セッションを作成できます。

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Windows Server ノードを使用する場合は (現在 AKS ではプレビュー段階)、ノード セレクターをコマンドに追加して、Linux ノードで Debian コンテナーのスケジュールを設定します。
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. ターミナル セッションがコンテナーに接続された後、`apt-get` を使用して SSH クライアントをインストールします。

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. コンテナーに接続されていない新しいターミナル ウィンドウを開き、プライベート SSH キーをヘルパー ポッドにコピーします。 この秘密キーは、AKS ノードへの SSH を作成するために使用されます。 

   必要に応じて、 *~/.ssh/id_rsa* をお使いの SSH 秘密キーの場所に変更します。

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. コンテナーへのターミナル セッションに戻り、コピーした `id_rsa` SSH 秘密キーに対するアクセス許可を、ユーザーに対して読み取り専用になるように更新します。

    ```console
    chmod 0600 id_rsa
    ```

1. AKS ノードへの SSH 接続を作成します。 前に説明したように、AKS ノードの既定のユーザー名は *azureuser* です。 SSH キーが最初に信頼されるときに接続続行のプロンプトを受け入れます。 次に、AKS ノードの bash プロンプトが提供されます。

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH アクセスの削除

終了したら、SSH セッションを `exit` し、対話型のコンテナー セッションを `exit` します。 このコンテナー セッションを終了すると、AKS クラスターからの SSH アクセスに使用されたポッドが削除されます。

## <a name="next-steps"></a>次の手順

トラブルシューティングのデータがさらに必要な場合は、[kubelet ログを表示][view-kubelet-logs]するか、[Kubernetes マスター ノードのログを表示][view-master-logs]することができます。

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
