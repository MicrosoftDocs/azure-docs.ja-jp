---
title: "Azure 仮想ネットワーク ピアリングを作成する - Resource Manager - 同じサブスクリプション | Microsoft Docs"
description: "Resource Manager で作成されて同じ Azure サブスクリプションに存在する仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: jdial;narayan;annahar
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: a32a6b33e04c603325ab3612f61e5852682eac7d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>仮想ネットワーク ピアリングを作成する - Resource Manager、同じサブスクリプション

このチュートリアルでは、Resource Manager で作成された仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法について説明します。 どちらの仮想ネットワークも同じサブスクリプションに存在する必要があります。 2 つの仮想ネットワークをピアリングすると、別々の仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような帯域幅と待ち時間で相互に通信を行うことができます。 詳しくは、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」をご覧ください。 

仮想ネットワーク ピアリングを作成する手順は、サブスクリプションが同じか異なるか、また、どの[Azure デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用して仮想ネットワークが作成されているかによって異なります。 他のシナリオで仮想ネットワークを作成する方法については、次の表で目的のシナリオをクリックしてください。

|Azure デプロイメント モデル  | Azure サブスクリプション  |
|--------- |---------|
|[両方とも Resource Manager](create-peering-different-subscriptions.md) |異なる|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models.md) |同じ|
|[一方が Resource Manager、もう一方がクラシック](create-peering-different-deployment-models-subscriptions.md) |異なる|

クラシック デプロイメント モデルでデプロイされた 2 つの仮想ネットワークの間に、仮想ネットワーク ピアリングを作成することはできません。 仮想ネットワーク ピアリングの作成は、同じ Azure リージョンに存在する 2 つの仮想ネットワークの間に限定されます。 どちらもクラシック デプロイメント モデルで作成された仮想ネットワーク、または異なる Azure リージョンに存在する仮想ネットワークを接続する必要がある場合は、Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使ってそれらの仮想ネットワークを接続できます。 

仮想ネットワーク ピアリングは、[Azure Portal](#portal)、Azure [コマンド ライン インターフェイス](#cli) (CLI)、Azure [PowerShell](#powershell)、[Azure Resource Manager テンプレート](#template)のいずれかを使って作成できます。 いずれかのリンクをクリックすると、そのツールを使って仮想ネットワーク ピアリングを作成するための手順に直接移動します。

## <a name="portal"></a>ピアリングの作成 - Azure Portal

1. [Azure ポータル](https://portal.azure.com)にログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
2. **[+ 新規]**、**[ネットワーキング]**、**[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワークの作成]** ブレードで、次の設定の値を入力するか選択し、**[作成]** をクリックします。
    - **[名前]**: *myVnet1*
    - **[アドレス空間]**: *10.0.0.0/16*
    - **[サブネット名]**: "*既定値*"
    - **[サブネット アドレス範囲]**: *10.0.0.0/24*
    - **[サブスクリプション]**: サブスクリプションを選択します。
    - **[リソース グループ]**: **[新規作成]** を選択し、「*myResourceGroup*」と入力します
    - **[場所]**: "*米国東部*"
4. 手順 2 ～ 3 を繰り返し、手順 3 で次の値を指定します。
    - **[名前]**: *myVnet2*
    - **[アドレス空間]**: *10.1.0.0/16*
    - **[サブネット名]**: "*既定値*"
    - **[サブネットのアドレス範囲]**: *10.1.0.0/24*
    - **[サブスクリプション]**: サブスクリプションを選択します。
    - **[リソース グループ]**: **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。
    - **[場所]**: "*米国東部*"
5. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
6. **[myVNet1]** をクリックします。
7. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
8. 表示された **[myVnet1 - ピアリング]** ブレードで **[+ 追加]** をクリックします。
9. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、**[OK]** をクリックします。
     - **[名前]**: *myVnet1ToMyVnet2*
     - **[仮想ネットワークのデプロイ モデル]**:  **[Resource Manager]** を選択します。 
     - **[サブスクリプション]**: サブスクリプションを選択します。
     - **[仮想ネットワーク]**:  **[仮想ネットワークの選択]** をクリックし、**[myVnet2]** をクリックします。
     - **[仮想ネットワーク アクセスを許可する]**: **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-a-peering)に関するページを参照してください。
10. 前の手順で **[OK]** をクリックすると、**[ピアリングの追加]** ブレードが閉じて、**[myVnet1 - ピアリング]** ブレードが再度表示されます。 数秒後、作成したピアリングがブレードに表示されます。 作成した **myVnet1ToMyVnet2** ピアリングの **[ピアリング状態]** 列に **[開始済み]** と表示されます。 Vnet1 から Vnet2 へのピアリングを作成したので、今度は myVnet2 から myVnet1 へのピアリングを作成する必要があります。 各仮想ネットワーク内のリソースが相互に通信するためには、ピアリングを双方向で作成する必要があります。
11. myVnet2 について、手順 5 ～ 10 を繰り返します。  ピアリングに *myVnet2ToMyVnet1* という名前を付けます。
12. **[OK]** をクリックして MyVnet2 のピアリングを作成してから数秒経つと、作成したばかりの **myVnet2ToMyVnet1** ピアリングが**接続済み**として **[ピアリング状態]** 列に表示されます。
13. MyVnet1 について、手順 5 ～ 7 を繰り返します。 **myVnet1ToVNet2** ピアリングの **[ピアリング状態]** も**接続済み**になります。 ピアリングにおける両方の仮想ネットワークの **[ピアリング状態]** 列に**接続済み**と表示されれば、ピアリングは正常に確立されています。
14. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
15. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

## <a name="cli"></a>ピアリングの作成 - Azure CLI

ここで使用するスクリプトの要件は次のとおりです。

- Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` コマンドを実行します。 アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- Bash シェルで動作します。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 

CLI とその依存関係をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 次のスクリプトの **[使ってみる]** をクリックすると Cloud Shell が開き、Azure アカウントでログインできます。 スクリプトを実行するには、**[コピー]** ボタンをクリックし、Cloud Shell に内容を貼り付けます。

1. リソース グループを 1 つと仮想ネットワークを 2 つ作成します。

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. 2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成します。
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. スクリプトの実行後、各仮想ネットワークのピアリングを確認します。 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    *myVnet1* を *myVnet2* に置き換えて、このコマンドをもう一度実行します。 どちらのコマンドも、出力結果の **PeeringState** 列に **Connected** と表示されます。

     2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

4. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
5. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。


## <a name="powershell"></a>ピアリングの作成 - PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
3. PowerShell で、`login-azurermaccount` コマンドを入力して Azure にログインします。 ログインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
4. リソース グループを 1 つと仮想ネットワークを 2 つ作成します。 スクリプトを実行するには、次のスクリプトをコピーし、PowerShell に貼り付けて、最後の行が画面に表示された後で `Enter` キーを押します。

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. 2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成します。 次のスクリプトをコピーし、PowerShell に貼り付けて、最後の行が画面に表示された後で `Enter` キーを押します。
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. 仮想ネットワークのサブネットを確認するには、次のコマンドをコピーし、PowerShell に貼り付けて、`Enter` キーを押します。

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    *myVnet1* を *myVnet2* に置き換えて、このコマンドをもう一度実行します。 どちらのコマンドも、出力結果の **PeeringState** 列に **Connected** と表示されます。
7. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
8. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

## <a name="template"></a>ピアリングの作成 - Resource Manager テンプレート

1. [仮想ネットワーク ピアリングの作成](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering)に関する Resource Manager テンプレートを参照します。 このテンプレートには、Azure Portal、PowerShell、Azure CLI を使用したデプロイ手順が記載されています。 仮想ネットワーク ピアリングを作成するために必要なアクセス許可が割り当てられているアカウントでいずれかのツールにログインし、テンプレートをデプロイします。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
2. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
3. **(省略可)** このチュートリアルで作成したリソースを削除するには、Azure Portal、PowerShell、Azure CLI のいずれかを使って、この記事の「[リソースの削除](#delete)」セクションの手順を実行してください。

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングの作成に使用するアカウントには、必要なロールまたはアクセス許可が割り当てられている必要があります。 たとえば、VNet1、VNet2 という名前の 2 つの仮想ネットワークをピアリングする場合、各仮想ネットワークに対する次の最小限のロールまたはアクセス許可をアカウントに割り当てる必要があります。
    
|Virtual Network|役割|アクセス許可|
|---|---|---|
|VNet1|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

[組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)と、特定のアクセス許可を[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法 (Resource Manager のみ) の詳細を参照してください。

## <a name="delete"></a>リソースの削除
このチュートリアルが完了したら、使用料がかからないように、チュートリアルで作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、**[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

次のコマンドを入力します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

次のコマンドを入力します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="next-steps"></a>次のステップ

- 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](virtual-network-manage-peering.md#requirements-and-constraints)の要点をしっかりと理解します。
- [仮想ネットワーク ピアリングのさまざまな設定](virtual-network-manage-peering.md#create-a-peering)について理解を深めます。
- 仮想ネットワーク ピアリングで[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)する方法を学習します。

