---
title: Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続
description: トラブルシューティングやメンテナンスのタスクのために Azure Kubernetes Service (AKS) クラスター ノードとの SSH 接続を作成する方法について説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: iainfou
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538801"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>メンテナンスまたはトラブルシューティングのために SSH を使用して Azure Kubernetes Service (AKS) クラスター ノードに接続する

Azure Kubernetes Service (AKS) クラスターのライフサイクル全体を通して、AKS ノードへのアクセスが必要になる場合があります。 メンテナンス、ログ収集、その他のトラブルシューティング操作のための接続です。 AKS ノードは Linux VM なので、SSH を使用してアクセスできます。 セキュリティのため、AKS ノードはインターネットに公開されていません。

この記事では、プライベート IP アドレスを使用して、AKS ノードとの SSH 接続を作成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用して][ aks-quickstart-cli]または[Azure portal を使用して][aks-quickstart-portal] AKS のクイック スタートを参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="add-your-public-ssh-key"></a>SSH 公開キーを追加する

既定では、AKS クラスターを作成するときに SSH キーが生成されます。 AKS クラスターを作成するときに独自の SSH キーを指定しなかった場合は、AKS ノードに SSH の公開キーを追加します。

AKS ノードに SSH キーを追加するには、次の手順のようにします。

1. [az aks show][az-aks-show] を使用して、AKS クラスター リソースに対するリソース グループ名を取得します。 独自のコア リソース グループと AKS クラスター名を指定します。

    ```azurecli-interactive
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. [az vm list][az-vm-list] コマンドを使用して、AKS クラスター リソース グループ内の VM を一覧表示します。 これらの VM はお使いの AKS ノードです。

    ```azurecli-interactive
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
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
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>AKS ノード アドレスを取得する

AKS ノードは、インターネットにパブリックに公開されていません。 AKS ノードに SSH 接続するには、プライベート IP アドレスを使用します。 次の手順では、AKS クラスターでヘルパー ポッドを作成して、ノードのこのプライベート IP アドレスに SSH 接続できるようにします。

[az vm list-ip-addresses][az-vm-list-ip-addresses] コマンドを使用して、AKS クラスター ノードのプライベート IP アドレスを表示します。 前の [az aks show][az-aks-show] のステップで取得した独自の AKS クラスター リソース グループ名を指定します。

```azurecli-interactive
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

次の出力例では AKS ノードのプライベート IP アドレスが示されています。

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>SSH 接続を作成する

AKS ノードへの SSH 接続を作成するには、AKS クラスターでヘルパー ポッドを実行します。 このヘルパー ポッドでは、クラスターへの SSH アクセスが提供され、続いて追加の SSH ノード アクセスが提供されます。 このヘルパー ポッドを作成して使用するには、次の手順のようにします。

1. `debian` コンテナー イメージを実行し、ターミナル セッションをそれにアタッチします。 このコンテナーを使用して、AKS クラスター内の任意のノードとの SSH セッションを作成できます。

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

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

    前のステップで取得した独自の *aks-ssh* ポッド名を指定します。 必要に応じて、*~/.ssh/id_rsa* をお使いの SSH 秘密キーの場所に変更します。

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
