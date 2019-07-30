---
title: Azure 仮想ネットワーク ピアリングの作成 - 異なるデプロイメント モデル - 同じサブスクリプション | Microsoft Docs
description: 同じ Azure サブスクリプションに存在する、異なる Azure デプロイメント モデルで作成された仮想ネットワーク間に、仮想ネットワーク ピアリングを作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 720351463a9f8d5712c76401f3fbba64c3177e84
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871969"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>仮想ネットワーク ピアリングの作成 - 異なるデプロイメント モデル、同じサブスクリプション

このチュートリアルでは、異なるデプロイメント モデルで作成された仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法について説明します。 どちらの仮想ネットワークも同じサブスクリプションに存在する必要があります。 2 つの仮想ネットワークをピアリングすると、別々の仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような帯域幅と待ち時間で相互に通信を行うことができます。 詳しくは、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」をご覧ください。

仮想ネットワーク ピアリングを作成する手順は、サブスクリプションが同じか異なるか、また、どの[Azure デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用して仮想ネットワークが作成されているかによって異なります。 他のシナリオで仮想ネットワークを作成する方法については、次の表で目的のシナリオをクリックしてください。

|Azure デプロイメント モデル  | Azure サブスクリプション  |
|--------- |---------|
|[両方とも Resource Manager](tutorial-connect-virtual-networks-portal.md) |同じ|
|[両方が Resource Manager](create-peering-different-subscriptions.md) |異なる|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models-subscriptions.md) |異なる|

クラシック デプロイ モデルでデプロイされた 2 つの仮想ネットワークの間に、仮想ネットワーク ピアリングを作成することはできません。 どちらもクラシック デプロイ モデルで作成された仮想ネットワークを接続する必要がある場合は、Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使ってそれらの仮想ネットワークを接続できます。

このチュートリアルでは同じリージョンで複数の仮想ネットワークをピアリングします。 異なる[サポート対象リージョン](virtual-network-manage-peering.md#cross-region)間での仮想ネットワーク ピアリングも可能です。 仮想ネットワークのピアリングの前に、[ピアリングの要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)をよく理解しておくことをお勧めします。

仮想ネットワーク ピアリングは、Azure portal、Azure [コマンド ライン インターフェイス](#cli) (CLI)、Azure [PowerShell](#powershell)、Azure Resource Manager テンプレートのいずれかを使って作成できます。 いずれかのリンクをクリックすると、そのツールを使って仮想ネットワーク ピアリングを作成するための手順に直接移動します。

## <a name="create-peering---azure-portal"></a>ピアリングの作成 - Azure Portal

1. [Azure Portal](https://portal.azure.com) にサインインします。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#requirements-and-constraints)に関するページをご覧ください。
2. **[+ 新規]** 、 **[ネットワーキング]** 、 **[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワークの作成]** ブレードで、次の設定の値を入力するか選択し、 **[作成]** をクリックします。
    - **[名前]** : *myVnet1*
    - **[アドレス空間]** : *10.0.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.0.0.0/24*
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroup*」と入力します
    - **[場所]** :*米国東部*
4. **[+ 新規]** をクリックします。 **[Marketplace を検索]** ボックスに「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
5. **[仮想ネットワーク]** ブレードの **[デプロイ モデルの選択]** ボックスで、 **[クラシック]** を選択し、 **[作成]** をクリックします。
6. **[仮想ネットワークの作成]** ブレードで、次の設定の値を入力するか選択し、 **[作成]** をクリックします。
    - **[名前]** : *myVnet2*
    - **[アドレス空間]** : *10.1.0.0/16*
    - **[サブネット名]** : "*既定値*"
    - **[サブネットのアドレス範囲]** : *10.1.0.0/24*
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** : **[既存のものを使用]** を選択し、 *[myResourceGroup]* を選択します。
    - **[場所]** :*米国東部*
7. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
8. **[myVNet1]** をクリックします。
9. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
10. 表示された **[myVnet1 - ピアリング]** ブレードで **[+ 追加]** をクリックします。
11. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、 **[OK]** をクリックします。
     - **[名前]** : *myVnet1ToMyVnet2*
     - **仮想ネットワークのデプロイ モデル**: **[クラシック]** を選択します。
     - **サブスクリプション**:サブスクリプションを選択します。
     - **仮想ネットワーク**: **[仮想ネットワークの選択]** をクリックし、 **[myVnet2]** をクリックします。
     - **[仮想ネットワーク アクセスを許可する]** : **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-a-peering)に関するページを参照してください。
12. 前の手順で **[OK]** をクリックすると、 **[ピアリングの追加]** ブレードが閉じて、 **[myVnet1 - ピアリング]** ブレードが再度表示されます。 数秒後、作成したピアリングがブレードに表示されます。 作成した **myVnet1ToMyVnet2** ピアリングの **[ピアリング状態]** 列に **[接続済み]** と表示されます。

    これでピアリングが確立されました。 2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。
13. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
14. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

## <a name="cli"></a>ピアリングの作成 - Azure CLI

Azure クラシック CLI と Azure CLI を使用して、次の手順を完了します。 次の手順のいずれかで **[試してみる]** ボタンを選択するだけで、または[クラシック CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) と [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) をインストールし、ローカル コンピューター上のコマンドを実行すると、Azure Cloud Shell からの手順を行うことができます。

1. Cloud Shell を使用する場合は、Cloud Shell が Azure に自動的にサインインするため、手順 2 に進みます。 コマンド セッションを開き、`azure login` コマンドを使用して Azure にサインインします。
2. `azure config mode asm` コマンドを入力して、サービス管理モードで CLI を実行します。
3. 次のコマンドを入力して、仮想ネットワーク (クラシック) を作成します。

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. クラッシック CLI ではない、CLI を使用して、次の bash CLI スクリプトを実行します。 bash CLI スクリプトを Windows コンピュータで実行する方法については、「[Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)」をご覧ください。

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. CLI を使用して、異なるデプロイメント モデルで作成された 2 つの仮想ネットワークの間で仮想ネットワーク ピアリングを作成します。 PC で次のスクリプトをテキスト エディターにコピーします。 `<subscription id>` は、サブスクリプション ID で置き換えてください。 サブスクリプション ID がわからない場合は、`az account show` コマンドを入力します。 出力された **id** の値がサブスクリプション ID です。 変更後のスクリプトを CLI セッションに貼り付けて、`Enter` を押します。

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. スクリプトの実行後、仮想ネットワーク (Resource Manager) のピアリングを確認します。 次のコマンドをコピーし、CLI セッションに貼り付けて、`Enter` を押します。

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   **PeeringState** 列に **Connected** と出力されます。

   2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。
7. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
8. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

## <a name="powershell"></a>ピアリングの作成 - PowerShell

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) および [Az](https://www.powershellgallery.com/packages/Az/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始します。
3. PowerShell で、`Add-AzureAccount` コマンドを入力して Azure にサインインします。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#requirements-and-constraints)に関するページをご覧ください。
4. PowerShell で仮想ネットワーク (クラシック) を作成するには、ネットワーク構成ファイルを新しく作成するか、既存のものを変更する必要があります。 [ネットワーク構成ファイルをエクスポート、更新、およびインポートする](virtual-networks-using-network-configuration-file.md)方法を確認してください。 ファイルには、このチュートリアルで使用されている仮想ネットワークの次の **VirtualNetworkSite** 要素を含める必要があります。

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 変更したネットワーク構成ファイルをインポートすると、サブスクリプションの既存の仮想ネットワーク (クラシック) が変更される可能性があります。 前の仮想ネットワークのみを追加し、サブスクリプションの既存の仮想ネットワークは変更または削除しないようにしてください。
5. `Connect-AzAccount` コマンドを入力し、Azure にサインインして仮想ネットワーク (Resource Manager) を作成します。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 アクセス許可の一覧については、[仮想ネットワークのピアリングのアクセス許可](virtual-network-manage-peering.md#requirements-and-constraints)に関するページをご覧ください。
6. リソース グループと仮想ネットワーク (Resource Manager) を作成します。 スクリプトをコピーして PowerShell に貼り付け、`Enter` を押します。

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. 異なるデプロイメント モデルで作成された 2 つの仮想ネットワークの間で仮想ネットワーク ピアリングを作成します。 PC で次のスクリプトをテキスト エディターにコピーします。 `<subscription id>` は、サブスクリプション ID で置き換えてください。 サブスクリプション ID がわからない場合は、`Get-AzSubscription` コマンドを入力して表示します。 返された出力の **id** の値がサブスクリプション ID です。 スクリプトを実行するには、お使いのテキスト エディターから変更後のスクリプトをコピーし、PowerShell セッションを右クリックして、`Enter` を押します。

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. スクリプトの実行後、仮想ネットワーク (Resource Manager) のピアリングを確認します。 次のコマンドをコピーし、PowerShell セッションに貼り付けて、`Enter` を押します。

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    出力の **PeeringState** 列に **Connected** と表示されます。

    2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

9. **省略可能**: このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
10. **省略可能**: このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

## <a name="delete"></a>リソースの削除

このチュートリアルが完了したら、使用料がかからないように、チュートリアルで作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、 **[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、 **[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、 **[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

1. 次のコマンドで、Azure CLI を使用して仮想ネットワーク (Resource Manager) を削除します。

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. 次のコマンドで、クラシック CLI を使用して仮想ネットワーク (クラシック) を削除します。

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. 次のコマンドを入力して、仮想ネットワーク (Resource Manager) を削除します。

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. PowerShell で仮想ネットワーク (クラシック) を削除するには、既存のネットワーク構成ファイルを変更する必要があります。 [ネットワーク構成ファイルをエクスポート、更新、およびインポートする](virtual-networks-using-network-configuration-file.md)方法を確認してください。 このチュートリアルで使用されている仮想ネットワークの次の VirtualNetworkSite 要素を削除します。

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 変更したネットワーク構成ファイルをインポートすると、ご使用のサブスクリプションの既存の仮想ネットワーク (クラシック) が変更される可能性があります。 前の仮想ネットワークのみを削除し、サブスクリプションの他の既存の仮想ネットワークは変更または削除しないようにしてください。

## <a name="next-steps"></a>次の手順

- 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](virtual-network-manage-peering.md#requirements-and-constraints)の要点をしっかりと理解します。
- [仮想ネットワーク ピアリングのさまざまな設定](virtual-network-manage-peering.md#create-a-peering)について理解を深めます。
- 仮想ネットワーク ピアリングで[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)する方法を学習します。
