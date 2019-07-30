---
title: Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続
description: トラブルシューティングやメンテナンスのタスクのために Azure Kubernetes Service (AKS) クラスター ノードとの SSH 接続を作成する方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 6ddd1b160110e7a751f54f89b387a62d94e9308e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614477"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>メンテナンスまたはトラブルシューティングのために SSH を使用して Azure Kubernetes Service (AKS) クラスター ノードに接続する

Azure Kubernetes Service (AKS) クラスターのライフサイクル全体を通して、AKS ノードへのアクセスが必要になる場合があります。 メンテナンス、ログ収集、その他のトラブルシューティング操作のための接続です。 Windows Server ノードを含め (現在 AKS ではプレビュー段階)、AKS ノードには SSH を使用してアクセスできます。 [リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続する][aks-windows-rdp]こともできます。 セキュリティのため、AKS ノードはインターネットに公開されていません。

この記事では、プライベート IP アドレスを使用して、AKS ノードとの SSH 接続を作成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用][aks-quickstart-cli]するか、Azure portal を使用して AKS のクイックスタートを参照してください。

また、Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="add-your-public-ssh-key"></a>SSH 公開キーを追加する

既定では、SSH キーが取得または生成され、AKS クラスターを作成するときにノードに追加されます。 AKS クラスターを作成したときに使用したものとは異なる SSH キーを指定する必要がある場合は、お使いの SSH 公開キーを Linux AKS ノードに追加します。 必要に応じて、[macOS、Linux][ssh-nix]、または or [Windows][ssh-windows] を使用して、SSH キーを作成できます。 PuTTY を使用してキーの組を作成する場合、そのキーの組は、既定の PuTTy 秘密キー形式 (.ppk ファイル) ではなく、OpenSSH 形式で保存します。

> [!NOTE]
> 現時点では、SSH キーは Azure CLI を使用して Linux ノードにのみ追加できます。 Windows Server ノードを使用する場合は、AKS クラスターを作成したときに指定した SSH キーを使用して、[AKS ノード アドレスを取得する方法](#get-the-aks-node-address)の手順に進みます。 または、[リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続][aks-windows-rdp]します。

AKS ノードのプライベート IP アドレスを取得する手順は、実行する AKS クラスターの種類によって異なります。

* ほとんどの AKS クラスターについて、[通常の AKS クラスターの IP アドレスを取得](#add-ssh-keys-to-regular-aks-clusters)する手順に従います。
* 複数のノード プール、Windows Server コンテナーのサポートなど、仮想マシン スケール セットが使用されている AKS のプレビュー機能を使用する場合は、[仮想マシン スケール セットに基づく AKS クラスターの手順に従います](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>通常の AKS クラスターに SSH キーを追加する

SSH キーを Linux AKS ノードに追加するには、次の手順を実行します。

1. [az aks show][az-aks-show] を使用して、AKS クラスター リソース用のリソース グループ名を取得します。 クラスター名は *CLUSTER_RESOURCE_GROUP* という名前の変数に割り当てられています。 *myResourceGroup* を、AKS クラスターが配置されているリソース グループの名前に置き換えます。

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. [az vm list][az-vm-list] コマンドを使用して、AKS クラスター リソース グループ内の VM を一覧表示します。 これらの VM はお使いの AKS ノードです。

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    次の出力例では AKS ノードが示されています。

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. ノードに SSH キーを追加するには、[az vm user update][az-vm-user-update] コマンドを使用します。 リソース グループ名と、前の手順で取得した AKS ノードのいずれかを指定します。 AKS ノードの既定のユーザー名は *azureuser* です。 独自の SSH 公開キーの場所を入力するか (例: *~/.ssh/id_rsa.pub*)、または SSH 公開キーの内容を貼り付けます。

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>仮想マシン スケール セットに基づく AKS クラスターに SSH キーを追加する

仮想マシン スケール セットに含まれている Linux AKS ノードに SSH キーを追加するには、次の手順を実行します。

1. [az aks show][az-aks-show] を使用して、AKS クラスター リソース用のリソース グループ名を取得します。 クラスター名は *CLUSTER_RESOURCE_GROUP* という名前の変数に割り当てられています。 *myResourceGroup* を、AKS クラスターが配置されているリソース グループの名前に置き換えます。

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. 次に、[az vmss list][az-vmss-list] コマンドを使用して、AKS クラスターの仮想マシン スケール セットを取得します。 仮想マシン スケール セット名は、*SCALE_SET_NAME* という名前の変数に割り当てられています。

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. 仮想マシン スケール セット内のノードに SSH キーを追加するには、[az vmss extension set][az-vmss-extension-set] コマンドを使用します。 クラスター リソース グループと仮想マシン スケール セット名は、前のコマンドから提供されます。 AKS ノードの既定のユーザー名は *azureuser* です。 必要に応じて、独自の SSH 公開キーの場所を更新します (例: *~/.ssh/id_rsa.pub*)。

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. [az vmss update-instances][az-vmss-update-instances] コマンドを使用して、ノードに SSH キーを適用します。

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>AKS ノード アドレスを取得する

AKS ノードは、インターネットにパブリックに公開されていません。 AKS ノードに SSH 接続するには、プライベート IP アドレスを使用します。 次の手順では、AKS クラスターでヘルパー ポッドを作成して、ノードのこのプライベート IP アドレスに SSH 接続できるようにします。 AKS ノードのプライベート IP アドレスを取得する手順は、実行する AKS クラスターの種類によって異なります。

* ほとんどの AKS クラスターについて、[通常の AKS クラスターの IP アドレスを取得](#ssh-to-regular-aks-clusters)する手順に従います。
* 複数のノード プール、Windows Server コンテナーのサポートなど、仮想マシン スケール セットが使用されている AKS のプレビュー機能を使用する場合は、[仮想マシン スケール セットに基づく AKS クラスターの手順に従います](#ssh-to-virtual-machine-scale-set-based-aks-clusters)。

### <a name="ssh-to-regular-aks-clusters"></a>通常の AKS クラスターに SSH 接続する

[az vm list-ip-addresses][az-vm-list-ip-addresses] コマンドを使用して、AKS クラスター ノードのプライベート IP アドレスを表示します。前の az-aks-show を使用した手順で取得された独自の AKS クラスター リソース グループ名を指定します。

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

次の出力例では AKS ノードのプライベート IP アドレスが示されています。

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>仮想マシン スケール セットに基づく AKS クラスターに SSH 接続する

[kubectl get コマンド][kubectl-get]を使用して、ノードの内部 IP アドレスの一覧を表示します。

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

トラブルシューティングを行うノードの内部 IP アドレスを記録します。 このアドレスは、後の手順で使用します。

## <a name="create-the-ssh-connection"></a>SSH 接続を作成する

AKS ノードへの SSH 接続を作成するには、AKS クラスターでヘルパー ポッドを実行します。 このヘルパー ポッドでは、クラスターへの SSH アクセスが提供され、続いて追加の SSH ノード アクセスが提供されます。 このヘルパー ポッドを作成して使用するには、次の手順のようにします。

1. `debian` コンテナー イメージを実行し、ターミナル セッションをそれにアタッチします。 このコンテナーを使用して、AKS クラスター内の任意のノードとの SSH セッションを作成できます。

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Windows Server ノードを使用する場合は (現在 AKS ではプレビュー段階)、次にようにノード セレクターをコマンドに追加して、Linux ノードで Debian コンテナーのスケジュールを設定します。
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. 基本の Debian イメージには、SSH コンポーネントは含まれません。 ターミナル セッションがコンテナーに接続された後、次のように `apt-get` を使用して SSH クライアントをインストールします。

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. コンテナーに接続されていない新しいターミナル ウィンドウで、[kubectl get pods][kubectl-get] コマンドを使用して AKS クラスター上のポッドの一覧を表示します。 次の例に示すように、前のステップで作成したポッドは *aks-ssh* という名前で開始します。

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. この記事の最初のステップで、SSH 公開キーを AKS ノードに追加しました。 ここでは、SSH の秘密キーをポッドにコピーします。 この秘密キーは、AKS ノードへの SSH を作成するために使用されます。

    前のステップで取得した独自の *aks-ssh* ポッド名を指定します。 必要に応じて、 *~/.ssh/id_rsa* をお使いの SSH 秘密キーの場所に変更します。

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. コンテナーへのターミナル セッションに戻り、コピーした `id_rsa` SSH 秘密キーに対するアクセス許可を、ユーザーが読み取り専用であるように更新します。

    ```console
    chmod 0600 id_rsa
    ```

1. ここで、AKS ノードへの SSH 接続を作成します。 前に説明したように、AKS ノードの既定のユーザー名は *azureuser* です。 SSH キーが最初に信頼されるときに接続続行のプロンプトを受け入れます。 次に、AKS ノードの bash プロンプトが提供されます。

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

トラブルシューティングのデータがさらに必要な場合は、[kubelet ログを表示する][view-kubelet-logs]か、Kubernetes マスター ノードのログを表示できます。

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
