---
title: ARM テンプレートを使用して SQL Server VM を作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure 仮想マシン (VM) に SQL Server を作成する方法を説明します。
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359644"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して SQL Server VM を作成する

この Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure 仮想マシン (VM) に SQL Server をデプロイします。 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

SQL Server VM ARM テンプレートには、次のものが必要です。

- 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) または [PowerShell](/powershell/scripting/install/installing-powershell)。 
- [仮想ネットワーク](../../../virtual-network/quick-create-portal.md)と[サブネット](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)が準備された構成済みの[リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。


## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/)からのものです。

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

このテンプレートには、次の 5 つの Azure リソースが定義されています。 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): パブリック IP アドレスを作成します。 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): ネットワーク セキュリティ グループを作成します。 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): ネットワーク インターフェイスを構成します。 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Azure に仮想マシンを作成します。 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): 仮想マシンを SQL IaaS Agent 拡張機能に登録します。 

その他の Azure VM 上の SQL Server のテンプレートについては、[クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular)をご覧ください。


## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートでは、目的の SQL Server バージョンがインストールされており、SQL IaaS Agent 拡張機能に登録されている仮想マシンが作成されます。 

   [![Azure へのデプロイ](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. 次の値を選択または入力します。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **[リソース グループ]** :SQL Server VM 用に準備されたリソース グループ。 
    * **[リージョン]** :リージョンを選択します。  たとえば **[米国中部]** です。
    * **[仮想マシン名]** : SQL Server 仮想マシンの名前を入力します。 
    * **仮想マシンのサイズ**: ドロップダウンから、ご利用の仮想マシンに適したサイズを選びます。
    * **Existing Virtual Network Name (既存の仮想ネットワーク名)** : SQL Server VM 用に準備された仮想ネットワークの名前を入力します。 
    * **Existing Vnet Resource Group (既存の VNet リソース グループ)** : 仮想ネットワークが準備されたリソース グループを入力します。 
    * **既存のサブネットの名前**: 準備したサブネットの名前。 
    * **イメージ プラン**: ビジネス ニーズに最も適した SQL Server と Windows Server イメージを選択します。 
    * **SQL Sku (SQL SKU)** : ビジネス ニーズに最も適した SQL Server SKU のエディションを選択します。 
    * **管理ユーザー名**: 仮想マシンの管理者のユーザー名。 
    * **管理パスワード**: VM 管理者アカウントによって使用されるパスワード。 
    * **Storage Workload Type (ストレージ ワークロードの種類)** : ビジネスに最適なワークロードのストレージの種類。 
    * **Sql Data Disks Count (SQL データ ディスクの数)** : SQL Server でデータ ファイルに使用されるディスクの数。  
    * **データ パス**: SQL Server データ ファイルのパス。 
    * **Sql Log Disks Count\(SQL ログ ディスクの数\)** : SQL Server でログ ファイルに使用されるディスクの数。 
    * **ログ パス**: SQL Server ログ ファイルのパス。 
    * **[場所]** :すべてのリソースの場所。この値は、既定値の `[resourceGroup().location]` のままにしておく必要があります。 

3. **[Review + create]\(レビュー + 作成\)** を選択します。 SQL Server VM が正常にデプロイされた後、通知が表示されます。

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure CLI を使用して、デプロイされたリソースを確認できます。 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用してリソース グループを削除します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [ チュートリアル: 初めての ARM テンプレートを作成してデプロイする](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

SQL Server VM をデプロイするその他の方法については、以下を参照してください。 
- [Azure Portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

詳細については、[Azure VM 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページを参照してください。