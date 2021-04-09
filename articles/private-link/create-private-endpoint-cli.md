---
title: クイックスタート - Azure CLI を使用して Azure プライベート エンドポイントを作成する
description: このクイックスタートでは、Azure CLI を使用してプライベート エンドポイントを作成する方法について説明します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889231"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>クイックスタート: Azure CLI を使用してプライベート エンドポイントを作成する

プライベート エンドポイントを使用して Azure Web アプリに安全に接続することにより、Azure Private Link の使用を開始します。

このクイックスタートでは、Azure Web アプリのプライベート エンドポイントを作成し、仮想マシンをデプロイしてプライベート接続をテストします。  

プライベート エンドポイントは、Azure SQL や Azure Storage などのさまざまな種類の Azure サービスに対して作成できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 対象の Azure サブスクリプションにデプロイされている **PremiumV2 レベル** 以上のアプリ サービス プランを持つ Azure Web アプリ。  
    * 詳細および例については、「[クイックスタート: Azure に ASP.NET Core Web アプリを作成する](../app-service/quickstart-dotnetcore.md)」を参照してください。 
    * Web アプリとエンドポイントの作成に関する詳細なチュートリアルについては「[チュートリアル: Azure プライベート エンドポイントを使用して Web アプリに接続する](tutorial-private-endpoint-webapp-portal.md)」を参照してください。
* Azure portal にサインインし、`az login` を実行して、ご利用のサブスクリプションがアクティブであることを確認します。
* `az --version` を実行して、ターミナルまたはコマンド ウィンドウの Azure CLI のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。
  * 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、次のようにリソース グループを作成します。

* 名前は **CreatePrivateEndpointQS-rg** にします。 
* 場所は **eastus** にします。

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>仮想ネットワークと bastion ホストの作成

このセクションでは、仮想ネットワーク、サブネット、bastion ホストを作成します。 

bastion ホストは、プライベート エンドポイントをテストする目的で、仮想マシンに安全に接続するために使用されます。

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.0.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.0.0.0/24** にします。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。
* 場所は **eastus** にします。

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) を使用してサブネットを更新し、プライベート エンドポイントのプライベート エンドポイント ネットワーク ポリシーを無効にします。

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、bastion サブネットを作成します。

* 名前は **AzureBastionSubnet** にします。
* アドレス プレフィックスは **10.0.1.0/24** にします。
* 仮想ネットワークは **myVNet** にします。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、bastion ホストを作成します。

* 名前: **myBastionHost**
* リソース グループは **CreatePrivateEndpointQS-rg** にします。
* パブリック IP **myBastionIP** に関連付けます。
* 仮想ネットワーク **myVNet** に関連付けます。
* 場所は **eastus** にします。

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

## <a name="create-test-virtual-machine"></a>テスト用の仮想マシンを作成する

このセクションでは、プライベート エンドポイントのテストに使用する仮想マシンを作成します。

 [az vm create](/cli/azure/vm#az_vm_create) を使用して VM を作成します。 メッセージが表示されたら、VM の資格情報として使用するパスワードを入力します。

* 名前は **myVM** にします。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。
* ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* サーバー イメージは **Win2019Datacenter** にします。

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、プライベート エンドポイントを作成します。

[az webapp list](/cli/azure/webapp#az_webapp_list) を使用して、以前に作成した Web アプリのリソース ID をシェル変数に配置します。

[az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) を使用して、エンドポイントと接続を作成します。

* 名前は **myPrivateEndpoint** にします。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* 接続名は **myConnection** にします。
* Web アプリは **\<webapp-resource-group-name>** です。

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

このセクションでは、[az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create) を使用してプライベート DNS ゾーンを作成し、構成します。  

[az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) を使用して、DNS ゾーンへの仮想ネットワーク リンクを作成します。

[az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create) を使用して、DNS ゾーン グループを作成します。

* ゾーン名は **privatelink.azurewebsites.net** にします。
* 仮想ネットワークは **myVNet** にします。
* リソース グループは **CreatePrivateEndpointQS-rg** にします。
* DNS リンク名は **myDNSLink** にします。
* **myPrivateEndpoint** に関連付けます。
* ゾーン グループ名は **MyZoneGroup** にします。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>プライベート エンドポイントへの接続のテスト

このセクションでは、前の手順で作成した仮想マシンを使用し、プライベート エンドポイントを通じて SQL サーバーに接続します。

1. [Azure ポータル](https://portal.azure.com) 
 
2. 左側のナビゲーション ペインで **[リソース グループ]** を選択します。

3. **[CreatePrivateEndpointQS-rg]** を選択します。

4. **[myVM]** を選択します。

5. **myVM** の [概要] ページで **[接続]** 、 **[Bastion]** の順に選択します。

6. 青色の **[Bastion を使用する]** ボタンを選択します。

7. 仮想マシンの作成時に入力したユーザー名とパスワードを入力します。

8. 接続後にサーバーで Windows PowerShell を開きます。

9. 「`nslookup <your-webapp-name>.azurewebsites.net`」と入力します。 **\<your-webapp-name>** を、前の手順で作成した Web アプリの名前に置き換えます。  以下に表示されるようなメッセージが返されます。

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web アプリ名に対応する **10.0.0.5** というプライベート IP アドレスが返されます。  このアドレスは、先ほど作成した仮想ネットワークのサブネット内に存在します。

10. **myVM** への bastion 接続で、Internet Explorer を開きます。

11. Web アプリの URL **https://\<your-webapp-name>.azurewebsites.net** を入力します。

12. アプリケーションがデプロイされていない場合は、既定の Web アプリ ページが表示されます。

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="既定の Web アプリ ページ。" border="true":::

13. **myVM** への接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
プライベート エンドポイントと VM を使い終わったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* 仮想ネットワークと bastion ホスト。
* 仮想マシン。
* Azure Web アプリのプライベート エンドポイント。

仮想マシンを使用して、プライベート エンドポイントを介した Web アプリへの接続を安全にテストしました。

プライベート エンドポイントをサポートするサービスの詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Private Link の可用性](private-link-overview.md#availability)
