---
title: チュートリアル:NAT ゲートウェイの作成 - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Azure CLI を使用した NAT ゲートウェイの作成を開始します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636798"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>チュートリアル: Azure CLI を使用した NAT ゲートウェイの作成

このチュートリアルでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成しましょう。 

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークを作成します。
> * 仮想マシンを作成します。
> * NAT ゲートウェイを作成し、仮想ネットワークに関連付けます。
> * 仮想マシンに接続し、NAT IP アドレスを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

このセクションでは、NAT ゲートウェイとサポート リソースを作成します。

### <a name="create-public-ip-address"></a>パブリック IP アドレスの作成

インターネットにアクセスするには、NAT ゲートウェイに 1 つ以上のパブリック IP アドレスが必要です。 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**myResourceGroupNAT** に **myPublicIP** というパブリック IP アドレス リソースを作成します。 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

[az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) を使用して、グローバル Azure NAT ゲートウェイを作成します。 このコマンドの結果、パブリック IP アドレス **myPublicIP** を使用する **myNATgateway** という名前のゲートウェイ リソースが作成されます。 アイドル タイムアウトは 10 分に設定されています。  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Create virtual network

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して、リソース グループ **myResourceGroup** に、**mySubnet** という名前のサブネットがある **myVnet** という名前の仮想ネットワークを作成します。 この仮想ネットワークの IP アドレス空間は **10.1.0.0/16** です。 この仮想ネットワーク内のサブネットは **10.1.0.0/24** です。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>bastion ホストを作成する

**myBastionHost** という名前の Azure Bastion ホストを作成して、仮想マシンにアクセスします。 

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、Azure Bastion サブネットを作成します。

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、bastion ホストのパブリック IP アドレスを作成します。 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、bastion ホストを作成します。 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>送信元サブネット用の NAT サービスの構成

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使用して、仮想ネットワーク **myVnet** 内の送信元サブネット **mySubnet** を、特定の NAT ゲートウェイ リソース **myNATgateway** を使用するように構成しましょう。 このコマンドにより、指定したサブネット上で NAT サービスがアクティブになります。

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

以降、インターネットを宛先とするすべてのアウトバウンド トラフィックがこの NAT ゲートウェイを使用するようになります。  UDR を構成する必要はありません。


## <a name="virtual-machine"></a>仮想マシン

このセクションでは、NAT ゲートウェイをテストする仮想マシンを作成し、アウトバウンド接続のパブリック IP アドレスを確認します。

[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

仮想マシンの作成が完了するのを待って次のセクションに進みます。

## <a name="test-nat-gateway"></a>NAT ゲートウェイをテストする

このセクションでは、NAT ゲートウェイをテストします。 まず、NAT ゲートウェイのパブリック IP を検出します。 次に、テスト仮想マシンに接続し、NAT ゲートウェイ経由のアウトバウンド接続を確認します。
    
1. [Azure ポータル](https://portal.azure.com)

1. **[概要]** 画面で、NAT ゲートウェイのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** 、 **[myPublicIP]** の順に選択します。

2. パブリック IP アドレスを書き留めておきます。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ゲートウェイのパブリック IP アドレスを検出する" border="true":::

3. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myResourceGroupNAT]** リソース グループにある **[myVM]** を選択します。

4. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

5. 青色の **[Bastion を使用する]** ボタンを選択します。

6. VM 作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** 開きます。

8. アドレス バーに「 **https://whatsmyip.com** 」と入力します。

9. 表示された IP アドレスが前の手順でメモしておいた NAT ゲートウェイのアドレスと一致していることを確認します。

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer に表示された外部アウトバウンド IP" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使う予定がなければ、次の手順を使用して、仮想ネットワーク、仮想マシン、および NAT ゲートウェイを削除します。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>次のステップ

Azure Virtual Network NAT の詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Virtual Network NAT の概要](nat-overview.md)
