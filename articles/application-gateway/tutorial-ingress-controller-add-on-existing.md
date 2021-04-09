---
title: チュートリアル:既存の Azure Application Gateway を使用して既存の AKS クラスターのイングレス コントローラー アドオンを有効にする
description: このチュートリアルを使用して、既存の Application Gateway で既存の AKS クラスターのイングレス コントローラー アドオンを有効にします
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: bfff962f6d302f589acc437550fa25f76ec7ce35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040425"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>チュートリアル: 既存の Application Gateway を使用して既存の AKS クラスターで Application Gateway イングレス コントローラー アドオンを有効にする

Azure CLI またはポータルを使用して、既存の [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) クラスターの [Application Gateway イングレス コントローラー (AGIC)](ingress-controller-overview.md) アドオンを有効にすることができます。 このチュートリアルでは、AGIC アドオンを使用して、別の仮想ネットワークにデプロイされている既存の Application Gateway を介して既存の AKS クラスターに Kubernetes アプリケーションを公開する方法について説明します。 まず、1 つの仮想ネットワークに AKS クラスターを作成し、別の仮想ネットワークに Application Gateway を作成して、既存のリソースをシミュレートします。 次に、AGIC アドオンを有効にし、2 つの仮想ネットワークをピアリングして、AGIC アドオンを使用して Application Gateway を通じて公開されるサンプル アプリケーションをデプロイします。 同じ仮想ネットワーク内の既存の Application Gateway と既存の AKS クラスターに対して AGIC アドオンを有効にしている場合は、次のピアリング手順を省略できます。 このアドオンを使用すると、[Helm を使用する以前の方法](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)よりもはるかに高速な方法で AKS クラスターに AGIC をデプロイでき、さらにフル マネージド エクスペリエンスを提供します。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループを作成する 
> * 新しい AKS クラスターを作成する 
> * 新しい Application Gateway の作成 
> * Azure CLI を使用して既存の AKS クラスターの AGIC アドオンを有効にする 
> * ポータルを使用して既存の AKS クラスターの AGIC アドオンを有効にする 
> * AKS クラスター仮想ネットワークを使用して Application Gateway 仮想ネットワークをピアリングする
> * AKS クラスターで AGIC をイングレスに使用してサンプル アプリケーションをデプロイする
> * アプリケーションが Application Gateway を介して到達可能であることを確認する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *canadacentral* の場所 (リージョン) に作成します。 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>新しい AKS クラスターをデプロイする

ここでは、新しい AKS クラスターをデプロイして、AGIC アドオンを有効にする既存の AKS クラスターがあることをシミュレートします。  

次の例では、作成したリソース グループ *myResourceGroup* で [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) および [マネージド ID](../aks/use-managed-identity.md) を使用して、*myCluster* という名前の新しい AKS クラスターをデプロイします。

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

`az aks create` コマンドの追加パラメーターを構成するには、[こちら](/cli/azure/aks#az-aks-create)のレファレンスを参照してください。 

## <a name="deploy-a-new-application-gateway"></a>新しい Application Gateway のデプロイ 

ここでは、*myCluster* という AKS クラスターへのトラフィックの負荷分散に使用する既存の Application Gateway があることをシミュレートするため、新しい Application Gateway をデプロイします。 Application Gateway の名前は *myApplicationGateway* になりますが、最初に、*myPublicIp* という名前のパブリック IP リソースと、アドレス空間が 11.0.0.0/8 の *myVnet* という名前の新しい仮想ネットワークと、アドレス空間が 11.1.0.0/16 の *mySubnet* というサブネットを作成し、*myPublicIp* を使用して Application Gateway を *mySubnet* にデプロイする必要があります。 

AKS クラスターと Application Gateway を別の仮想ネットワークで使用する場合、2 つの仮想ネットワークのアドレス空間が重複しないようにする必要があります。 AKS クラスターがデプロイする既定のアドレス空間は 10.0.0.0/8 であるため、Application Gateway 仮想ネットワークのアドレス プレフィックスを 11.0.0.0/8 に設定します。 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway イングレス コントローラー (AGIC) アドオンは、Application Gateway v2 SKU (Standard および WAF) **のみ** をサポートし、Application Gateway v1 SKU はサポート **しません**。 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Azure CLI を使用して既存の AKS クラスターの AGIC アドオンを有効にする 

Azure CLI を引き続き使用する場合は、作成した AKS クラスター *myCluster* で AGIC アドオンを有効にし、作成した既存の Application Gateway *myApplicationGateway* を使用する AGIC アドオンを指定します。

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>ポータルを使用して既存の AKS クラスターの AGIC アドオンを有効にする 

Azure portal を使用して AGIC アドオンを有効にする場合は、[(https://aka.ms/azure/portal/aks/agic)](https://aka.ms/azure/portal/aks/agic) に移動し、ポータル リンクを使用して対象の AKS クラスターに移動します。 そこから、その AKS クラスター内の [ネットワーク] タブにアクセスします。 [Application Gateway ingress controller]\(Application Gateway イングレス コントローラー\) セクションが表示されます。ここで、ポータルの UI を使用してイングレス コントローラーを有効または無効にできます。 [Enable ingress controller]\(イングレス コントローラーを有効にする\) の横にあるチェック ボックスをオンにして、作成した Application Gateway (*myApplicationGateway*) をドロップダウン メニューから選択します。 

![ポータルの [Application Gateway ingress controller]\(Application Gateway イングレス コントローラー\)](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together"></a>2 つの仮想ネットワークを一緒にピアリングする

AKS クラスターを独自の仮想ネットワークにデプロイし、別の仮想ネットワークに Application Gateway をデプロイしたため、Application Gateway からクラスター内のポッドにトラフィックを流すには、2 つの仮想ネットワークを一緒にピアリングする必要があります。 2 つの仮想ネットワークをピアリングするには、接続が双方向になるよう、Azure CLI コマンドを 2 回個別に実行する必要があります。 最初のコマンドは、Application Gateway 仮想ネットワークから AKS 仮想ネットワークへのピアリング接続を作成します。2 番目のコマンドは、その逆方向にピアリング接続を作成します。

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>AGIC を使用してサンプル アプリケーションをデプロイする 

次に、作成した AKS クラスターにサンプル アプリケーションをデプロイします。このクラスターでは、AGIC アドオンをイングレスに使用し、AKS クラスターに Application Gateway を接続します。 まず、`az aks get-credentials` コマンドを実行して、デプロイした AKS クラスターの資格情報を取得します。 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

作成したクラスターの資格情報を取得したら、次のコマンドを実行して、クラスターへのイングレスに AGIC を使用するサンプル アプリケーションをセットアップします。 AGIC によって、以前に設定した Application Gateway を、対応するルーティング規則で、デプロイした新しいサンプル アプリケーションに更新します。  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>アプリケーションに到達可能であることを確認する

これで、Application Gateway が AKS クラスターへのトラフィックを処理するように設定されたので、アプリケーションが到達可能であることを確認してみましょう。 最初に、イングレスの IP アドレスを取得します。 

```azurecli-interactive
kubectl get ingress
```

先ほど作成したサンプル アプリケーションが稼働していることを確認するには、上記のコマンドを実行して取得した Application Gateway の IP アドレスを参照するか、`curl` で確認します。 Application Gateway で更新を取得するのに 1 分ほどかかる場合があるため、ポータルの Application Gateway の状態が "更新中" のままになっている場合は、IP アドレスに到達する前に完了するようにしてください。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [AGIC アドオンの無効化に関する詳細情報](./ingress-controller-disable-addon.md)
