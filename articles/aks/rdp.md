---
title: Azure Kubernetes Service (AKS) クラスターの Windows Server ノードへの RDP 接続
description: トラブルシューティングやメンテナンスのタスクのために Azure Kubernetes Service (AKS) クラスターの Windows Server ノードとの RDP 接続を作成する方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614576"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>メンテナンスまたはトラブルシューティングのために RDP を使用して Azure Kubernetes Service (AKS) クラスターの Windows Server ノードに接続する

Azure Kubernetes Service (AKS) クラスターのライフサイクル全体を通して、AKS の Windows Server ノードへのアクセスが必要になる場合があります。 メンテナンス、ログ収集、その他のトラブルシューティング操作のための接続です。 RDP を使用して AKS の Windows Server ノードにアクセスできます。 または、SSH を使用して AKS の Windows Server ノードにアクセスする場合、クラスターの作成時に使用したのと同じキー ペアにアクセス可能であれば、[Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続][ssh-steps]に関する記事の手順に従います。 セキュリティのため、AKS ノードはインターネットに公開されていません。

現在、AKS で Windows Server ノードのサポートはプレビュー段階にあります。

この記事では、プライベート IP アドレスを使用して、AKS ノードとの RDP 接続を作成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、Windows Server ノードを含む AKS クラスターが既に存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用して Windows コンテナーと共に AKS クラスターを作成する][aks-windows-cli]方法に関する記事を参照してください。. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac]。

また、Azure CLI バージョン 2.0.61 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>クラスターと同じサブネットに仮想マシンをデプロイする

AKS クラスターの Windows Server ノードには、外部からアクセスできる IP アドレスはありません。 RDP 接続を作成するには、Windows Server ノードと同じサブネットに、パブリックにアクセス可能な IP アドレスを持つ仮想マシンをデプロイします。

次の例では、リソース グループ *myResourceGroup*に *myVM* という名前の仮想マシンを作成します。

最初に、Windows Server ノード プールで使用されるサブネットを取得します。 サブネット ID を取得するには、サブネットの名前が必要です。 サブネットの名前を取得するには、vnet の名前が必要です。 クラスターでネットワークの一覧のクエリを実行して、vnet の名前を取得します。 クラスターのクエリを実行するには、その名前が必要です。 Azure Cloud Shell で次を実行することで、これらをすべて取得できます。

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

SUBNET_ID を取得したので、同じ Azure Cloud Shell ウィンドウで次のコマンドを実行して VM を作成します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

次の出力例では、VM が正常に作成されたことを示し、仮想マシンのパブリック IP アドレスが表示されています。

```console
13.62.204.18
```

仮想マシンのパブリック IP アドレスを書き留めます。 このアドレスは、後の手順で使用します。

## <a name="get-the-node-address"></a>ノード アドレスを取得する

Kubernetes クラスターを管理するには、Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使用します。 Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 `kubectl` をローカルにインストールするには、[az aks install-cli][az-aks-install-cli] コマンドを使用します。
    
```azurecli-interactive
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

[kubectl get][kubectl-get] コマンドを使用して、Windows Server ノードの内部 IP アドレスの一覧を表示します。

```console
kubectl get nodes -o wide
```

次の出力例では、Windows Server ノードなど、クラスター内のすべてのノードの内部 IP アドレスを示します。

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

トラブルシューティングを行う Windows Server ノードの内部 IP アドレスを書き留めます。 このアドレスは、後の手順で使用します。

## <a name="connect-to-the-virtual-machine-and-node"></a>仮想マシンとノードに接続する

[Microsoft リモート デスクトップ][rdp-mac]などの RDP クライアントを使用して前に作成した仮想マシンのパブリック IP アドレスに接続します。

![RDP クライアントを使用した仮想マシンへの接続のイメージ](media/rdp/vm-rdp.png)

仮想マシンに接続したら、仮想マシン内から、RDP クライアントを使用してトラブルシューティングを行う Windows Server ノードの*内部 IP アドレス*に接続します。

![RDP クライアントを使用した Windows Server ノードへの接続のイメージ](media/rdp/node-rdp.png)

これで Windows Server ノードに接続されました。

![Windows Server ノード内の cmd ウィンドウのイメージ](media/rdp/node-session.png)

これで、*cmd* ウィンドウで任意のトラブルシューティング コマンドを実行できるようになりました。 Windows Server ノードでは Windows Server Core が使用されるため、RDP 経由で Windows Server ノードに接続するときに、完全な GUI またはその他の GUI ツールはありません。

## <a name="remove-rdp-access"></a>RDP アクセスを削除する

作業が完了したら、Windows Server ノードへの RDP 接続を終了し、仮想マシンへの RDP セッションを終了します。 両方の RDP セッションを終了したら、[az vm delete][az-vm-delete] コマンドを使用して仮想マシンを削除します。

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>次の手順

トラブルシューティング データがさらに必要な場合は、[Kubernetes マスター ノードのログ][view-master-logs]or [Azure Monitor][azure-monitor-containers] を表示できます。

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
