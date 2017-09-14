---
title: "複数のサブネットを含んだ Azure 仮想ネットワークを作成する | Microsoft Docs"
description: "Azure で、複数のサブネットを含んだ仮想ネットワークを作成する方法について説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a31f0524a6fa1de45498f340a27b863a3c627e04
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>複数のサブネットを含んだ仮想ネットワークを作成する

このチュートリアルでは、パブリック用とプライベート用に別個のサブネットを含んだ基本的な Azure 仮想ネットワークの作成方法について説明します。 サブネットには、仮想マシン、App Service 環境、仮想マシンのスケール セット、Azure HDInsight、クラウド サービスなどの Azure リソースを作成することができます。 仮想ネットワーク内のリソースは、相互に通信することも、仮想ネットワークに接続されている他のネットワーク内のリソースと通信することもできます。

次のセクションでは、[Azure Portal](#portal)、Azure コマンドライン インターフェイス ([Azure CLI](#azure-cli))、 [Azure PowerShell](#powershell)、および[Azure Resource Manager テンプレート](#resource-manager-template)を使って仮想ネットワークを作成するために実行する手順を説明します。 仮想ネットワークの作成に使用したツールに関係なく、結果は同じです。 このチュートリアルの該当セクションに移動するには、ツール リンクをクリックしてください。 すべての[仮想ネットワーク](virtual-network-manage-network.md)と[サブネット](virtual-network-manage-subnet.md)の設定について説明します。

この記事では、リソース マネージャーの展開モデルを使用して仮想ネットワークを作成する手順について説明します。新しい仮想ネットワークを作成するときにはこの展開モデルを使用することをお勧めします。 仮想ネットワーク (クラシック) を作成する必要がある場合は、「[Create a virtual network (classic)](create-virtual-network-classic.md)」(仮想ネットワーク (クラシック) を作成する) を参照してください。 Azure 展開モデルの知識がない場合は、[Azure 展開モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="portal"></a>Azure Portal

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) を開きます。 [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用してログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
2. ポータルで、**[+ 新規]** > **[ネットワーキング]** > **[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワークの作成]** ブレードで、次の値を入力して **[作成]** をクリックします。

    |Setting|値|
    |---|---|
    |名前|MyVnet|
    |アドレス空間|10.0.0.0/16|
    |サブネット名|パブリック|
    |サブネットのアドレス範囲|10.0.0.0/24|
    |リソース グループ|**[新規作成]** を選択して、「**myResourceGroup**」と入力します。|
    |サブスクリプションと場所|サブスクリプションと場所を選択します。

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (*リージョン*とも呼ばれる) について、それぞれのリンク先のページでご覧ください。
4. ポータルでは、仮想ネットワークを作成するときに、サブネットを 1 つだけ作成できます。 このチュートリアルでは、仮想ネットワークを作成した後に、2 つ目のサブネットを作成します。 インターネットにアクセス可能なリソースを、後で**パブリック** サブネットで作成できます。 また、インターネットからアクセスできないリソースは、**プライベート** サブネットで作成できます。 2 つ目のサブネットを作成するために、ページの上部にある **[リソースの検索]** ボックスに、「**myVnet**」と入力します。 検索結果で、**[myVnet]** をクリックします。 サブスクリプションに同じ名前の複数の仮想ネットワークがある場合は、各仮想ネットワーク下に一覧表示されるリソース グループを確認してください。 必ず **myResourceGroup** リソース グループを含む **myVnet**の検索結果をクリックしてください。
5. **[myVnet]** ブレードで、**[設定]** の **[サブネット]** をクリックします。
6. **[myVnet - サブネット]** ブレードで **[+サブネット]** をクリックします。
7. **[サブネットの追加]** ブレードで、**[名前]** に「**Private**」と入力します。 **[アドレス範囲]**に「**10.0.1.0/24**」と入力します。  **[OK]**をクリックします。
8. **[myVnet - サブネット]** ブレードで、サブネットを確認します。 先ほど作成した **Public** サブネットと **Private** サブネットが表示されます。
9. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」の手順を実行してください。

## <a name="azure-cli"></a>Azure CLI

Windows、Linux、または macOS のどこからコマンドを実行しても、Azure CLI コマンドは同じです。 ただし、オペレーティング システム シェル間ではスクリプトに違いがあります。 次の手順のスクリプトは、Bash シェルで実行します。 

1. [Azure CLI のインストールと構成](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Cloud Shell には Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上にあるクラウド シェル (**> _**) ボタンをクリックするか、その後の手順で *[試してみる]* ボタンをクリックします。 
2. CLI をローカルで実行する場合、`az login` コマンドを使用して Azure にログインします。 Cloud Shell を使用する場合、既にログインしています。
3. 次のスクリプトとコメントを確認してください。 ブラウザーで、スクリプトをコピーし、CLI セッションに貼り付けます。

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. スクリプトの実行が完了したら、仮想ネットワークのサブネットを確認します。 次のコマンドをコピーし、CLI セッションに貼り付けます。

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

## <a name="powershell"></a>PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションで、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)と `login-azurermaccount` コマンドを使用して Azure にログインします。

3. 次のスクリプトとコメントを確認してください。 ブラウザーでスクリプトをコピーし、PowerShell セッションに貼り付けます。

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. 仮想ネットワークのサブネットを確認するには、次のコマンドをコピーして、PowerShell セッションに貼り付けます。

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

## <a name="resource-manager-template"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートを使用して、仮想ネットワークをデプロイできます。 テンプレートの詳細については、[Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)に関する記事をご覧ください。 テンプレートにアクセスしてそのパラメーターについての情報を入手するには、「[Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)」(2 つのサブネットを含んだ仮想ネットワークの作成) のテンプレートをご覧ください。 テンプレートのデプロイには、[ポータル](#template-portal)、[Azure CLI](#template-cli)、[PowerShell](#template-powershell) のいずれかを使用できます。

**省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete)」のセクションにある手順を実行してください。

### <a name="template-portal"></a>Azure Portal

1. ブラウザーで[テンプレート ページ](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)を開きます。
2. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。 Azure にログインしていない場合は、表示される Azure Portal のログイン画面にログインします。
3. [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して、ポータルにサインインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
4. 各パラメーターの値を次のように入力します。

    |パラメーターが含まれる必要があります。|値|
    |---|---|
    |サブスクリプション|サブスクリプションを選択します。|
    |リソース グループ|myResourceGroup|
    |場所|場所を選択します。|
    |VNet 名|MyVnet|
    |VNet のアドレス プレフィックス|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|パブリック|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. 利用条件に同意して **[購入]** をクリックし、仮想ネットワークをデプロイします。

### <a name="template-cli"></a>Azure CLI

1. [Azure CLI のインストールと構成](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Cloud Shell には Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上にあるクラウド シェル **> _** ボタンをクリックするか、その後の手順で **[試してみる]** ボタンをクリックします。 
2. CLI をローカルで実行する場合、`az login` コマンドを使用して Azure にログインします。 Cloud Shell を使用する場合、既にログインしています。
3. 仮想ネットワークのリソース グループを作成するには、次のコマンドをコピーし、CLI セッションに貼り付けます。

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. 次のパラメーター オプションのいずれかを使用して、テンプレートをデプロイできます。
    - **既定のパラメーター値** 次のコマンドを入力します。
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **カスタムのパラメーター値** テンプレートをデプロイする前に、テンプレートをダウンロードして変更します。 また、コマンド ラインでパラメーターを使用してテンプレートをデプロイしたり、別個のパラメーター ファイルを使ってテンプレートをデプロイすることもできます。 テンプレートとパラメーター ファイルをダウンロードするには、[[Create a virtual network with two subnets]\(2 つのサブネットで仮想ネットワークを作成する\)](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) テンプレート ページで、**[GitHub で参照する]** ボタンをクリックします。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックします。 次に、**[Raw]** ボタンをクリックして、ファイルを表示します。 ブラウザーで、ファイルのコンテンツをコピーします。 お使いのコンピューター上のファイルにコンテンツを保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイできます。  

    これらの手段でテンプレートをデプロイする方法について詳しくは、「`az group deployment create --help`」と入力してください。

### <a name="template-powershell"></a>PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションで、ご利用の [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使ってサインインするには、「`login-azurermaccount`」と入力します。
3. 仮想ネットワークのリソース グループを作成するには、次のコマンドを入力します。

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. 次のパラメーター オプションのいずれかを使用して、テンプレートをデプロイできます。
    - **既定のパラメーター値** 次のコマンドを入力します。
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **カスタムのパラメーター値** デプロイする前に、テンプレートをダウンロードして変更します。 また、コマンド ラインでパラメーターを使用してテンプレートをデプロイしたり、別個のパラメーター ファイルを使ってテンプレートをデプロイすることもできます。 テンプレートとパラメーター ファイルをダウンロードするには、[[Create a virtual network with two subnets]\(2 つのサブネットで仮想ネットワークを作成する\)](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) テンプレート ページで、**[GitHub で参照する]** ボタンをクリックします。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックします。 次に、**[Raw]** ボタンをクリックして、ファイルを表示します。 ブラウザーで、ファイルのコンテンツをコピーします。 お使いのコンピューター上のファイルにコンテンツを保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイできます。  

    これらの手段でテンプレートをデプロイする方法に関する詳細は、「`Get-Help New-AzureRmResourceGroupDeployment`」と入力して確認してください。 

## <a name="delete"></a>リソースの削除

このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、**[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

CLI セッションで次のコマンドを入力します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

PowerShell セッションで次のコマンドを入力します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

- すべての仮想ネットワークとサブネットの設定に関する詳細については、[仮想ネットワークの管理](virtual-network-manage-network.md#view-vnet)、および[仮想ネットワークのサブネットの管理](virtual-network-manage-subnet.md#create-subnet)に関する記事をご覧ください。 運用環境の仮想ネットワークとサブネットを使用して別の要件を満たすには、さまざまなオプションがあります。
- 受信方向と送信方向のサブネット トラフィックをフィルタリングするには、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)を作成してサブネットに適用します。
- [Windows 仮想マシン](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または[Linux 仮想マシン](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成して、既存の仮想ネットワークに接続します。
- 同じ Azure の場所の 2 つの仮想ネットワークを接続するには、仮想ネットワーク間の[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)を作成します。
- [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または [Azure ExpressRoute ](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続します。

