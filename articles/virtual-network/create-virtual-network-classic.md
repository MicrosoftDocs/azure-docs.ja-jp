---
title: 複数のサブネットを含んだ Azure 仮想ネットワーク (クラシック) を作成する | Microsoft Docs
description: Azure で、複数のサブネットを含んだ仮想ネットワーク (クラシック) を作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059008"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>複数のサブネットを含んだ仮想ネットワーク (クラシック) を作成する

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[2 種類のデプロイ モデル](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)があります。Resource Manager とクラシックです。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 ほとんどの新規仮想ネットワークは、[Resource Manager](quick-create-portal.md) デプロイメント モデルを使用して作成することをお勧めします。

このチュートリアルでは、パブリック用とプライベート用に別個のサブネットを含んだ基本的な Azure 仮想ネットワーク (クラシック) の作成方法について説明します。 サブネット内の仮想マシンやクラウド サービスなどの Azure リソースを作成することができます。 仮想ネットワーク (クラシック) に作成されるリソースは、相互に通信することも、仮想ネットワークに接続されている他のネットワーク内のリソースと通信することもできます。

すべての[仮想ネットワーク](manage-virtual-network.md)と[サブネット](virtual-network-manage-subnet.md)の設定について説明します。

> [!WARNING]
> 仮想ネットワーク (クラシック) は、[サブスクリプションが無効になる](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit)とすぐに Azure によって削除されます。 仮想ネットワーク (クラシック) は、リソースが仮想ネットワーク上に存在するかどうかに関係なく削除されます。 後でもう一度サブスクリプションを有効にした場合は、仮想ネットワークに存在していたリソースを再作成する必要があります。

仮想ネットワーク (クラシック) は、[Azure Portal](#portal)、[Azure コマンド ライン インターフェイス (CLI) 1.0](#azure-cli)、[PowerShell](#powershell) のいずれかを使って作成できます。

## <a name="portal"></a>ポータル

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) を開きます。 [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用してログインします。 Azure アカウントを持っていない場合、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
2. ポータルで **[リソースの作成]** をクリックします。
3. 表示された **[新規]** ウィンドウの上部にある **[Marketplace を検索]** ボックスに「"*仮想ネットワーク*"」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
4. 表示された **[仮想ネットワーク]** ウィンドウの **[デプロイ モデルの選択]** ボックスで **[クラシック]** を選択し、 **[作成]** をクリックします。 
5. **[仮想ネットワーク (クラシック) の作成]** ウィンドウで次の値を入力し、 **[作成]** をクリックします。

    |Setting|値|
    |---|---|
    |名前|myVnet|
    |アドレス空間|10.0.0.0/16|
    |サブネット名|パブリック|
    |サブネットのアドレス範囲|10.0.0.0/24|
    |Resource group|**[新規作成]** を選択して、「**myResourceGroup**」と入力します。|
    |サブスクリプションと場所|サブスクリプションと場所を選択します。

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (*リージョン*とも呼ばれる) について、それぞれのリンク先のページでご覧ください。
4. ポータルでは、仮想ネットワークを作成するときに、サブネットを 1 つだけ作成できます。 このチュートリアルでは、仮想ネットワークを作成した後に、2 つ目のサブネットを作成します。 インターネットにアクセス可能なリソースを、後で**パブリック** サブネットで作成できます。 また、インターネットからアクセスできないリソースは、**プライベート** サブネットで作成できます。 2 つ目のサブネットを作成するために、ページ上部にある **[リソースの検索]** ボックスに「**myVnet**」と入力します。 検索結果に **[myVnet]** が表示されたら、それをクリックします。
5. 表示された **[仮想ネットワーク (クラシック) の作成]** ウィンドウで ( **[設定]** セクションの) **[サブネット]** をクリックします。
6. 表示された **[myVnet - サブネット]** ウィンドウで **[+追加]** をクリックします。
7. **[サブネットの追加]** ウィンドウで、 **[名前]** に「**プライベート**」と入力します。 **[アドレス範囲]** に「**10.0.1.0/24**」と入力します。  Click **OK**.
8. **[myVnet - サブネット]** ウィンドウに、作成した**パブリック** サブネットと**プライベート** サブネットが表示されます。
9. **省略可能**: このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。
    - **[myVnet]** ウィンドウで **[概要]** をクリックします。
    - **[myVnet]** ウィンドウで **[削除]** アイコンをクリックします。
    - 削除を確定するには、 **[仮想ネットワークの削除]** ボックスで **[はい]** をクリックします。

## <a name="azure-cli"></a>Azure CLI

1. [Azure CLI をインストールして構成](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)するか、Azure Cloud Shell 内で CLI を使用できます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 CLI コマンドのヘルプを表示するには、「`azure <command> --help`」と入力します。 
2. CLI セッションで、次のコマンドを使用して Azure にログインします。 下のボックスで **[試してみる]** をクリックすると、Cloud Shell が開きます。 次のコマンドを入力しなくても、Azure サブスクリプションにログインできます。

    ```azurecli-interactive
    azure login
    ```

3. CLI がサービス管理モードであることを確認するには、次のコマンドを入力します。

    ```azurecli-interactive
    azure config mode asm
    ```

4. プライベート サブネットを含む仮想ネットワークを作成します。

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. 仮想ネットワーク内にパブリック サブネットを作成します。

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. 仮想ネットワークとサブネットをレビューします。

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **省略可能**: 使用料がかからないように、このチュートリアルの完了時に、作成したリソースを削除することをお勧めします。

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> CLI を使用して仮想ネットワーク (クラシック) を作成するためにリソース グループを指定することはできませんが、"*既定-ネットワーク*" という名前のリソース グループに仮想ネットワークが作成されます。

## <a name="powershell"></a>PowerShell

1. PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始します。
3. PowerShell で、`Add-AzureAccount` コマンドを入力して Azure にログインします。
4. 必要に応じて次のパスとファイル名を変更してから、既存のネットワーク構成ファイルをエクスポートします。

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. パブリック サブネットとプライベート サブネットを含む仮想ネットワークを作成するには、任意のテキスト エディターを使用して、次の **VirtualNetworkSite** 要素をネットワーク構成ファイルに追加します。

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    [ネットワーク構成ファイル スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)全体をレビューします。

6. ネットワーク構成ファイルをインポートします。

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > 変更したネットワーク構成ファイルをインポートすると、サブスクリプションの既存の仮想ネットワーク (クラシック) が変更される可能性があります。 前の仮想ネットワークのみを追加し、サブスクリプションの既存の仮想ネットワークは変更または削除しないようにしてください。 

7. 仮想ネットワークとサブネットをレビューします。

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **省略可能**: 使用料がかからないように、このチュートリアルの完了時に、作成したリソースを削除することをお勧めします。 仮想ネットワークを削除するには、手順 4. から 6. をもう一度実行します。ここでは、手順 5. で追加した **VirtualNetworkSite** 要素を削除します。
 
> [!NOTE]
> PowerShell を使用して仮想ネットワーク (クラシック) を作成するためにリソース グループを指定することはできませんが、"*既定-ネットワーク*" という名前のリソース グループに仮想ネットワークが作成されます。

---

## <a name="next-steps"></a>次の手順

- すべての仮想ネットワークとサブネットの設定について詳しくは、[仮想ネットワークの管理](manage-virtual-network.md)、および[仮想ネットワークのサブネットの管理](virtual-network-manage-subnet.md)に関する記事をご覧ください。 運用環境の仮想ネットワークとサブネットを使用して別の要件を満たすには、さまざまなオプションがあります。
- [Windows 仮想マシン](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または [Linux 仮想マシン](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成して、既存の仮想ネットワークに接続します。
- 同じ Azure の場所の 2 つの仮想ネットワークを接続するには、仮想ネットワーク間の[仮想ネットワーク ピアリング](create-peering-different-deployment-models.md)を作成します。 仮想ネットワーク (Resource Manager) を仮想ネットワーク (クラシック) にピアリングすることはできますが、2 つの仮想ネットワーク (クラシック) 間でピアリングを作成することはできません。
- [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Azure ExpressRoute ](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続します。
