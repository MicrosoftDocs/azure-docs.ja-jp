---
title: 依存リソースを含む Azure Resource Manager テンプレートを作成する | Microsoft Docs
description: 複数のリソースを含む Azure Resource Manager テンプレートを作成し、Azure Portal を使用してデプロイする方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7e6ac19d3116b51a309816170413b694a15ef0c2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086226"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>チュートリアル:依存リソースを含む Azure Resource Manager テンプレートを作成する

Azure Resource Manager テンプレートを作成して、複数のリソースをデプロイしたりデプロイ順序を構成したりする方法を説明します。 テンプレートを作成したら、Azure Portal から Cloud Shell を使用してテンプレートをデプロイします。

このチュートリアルでは、ストレージ アカウント、仮想マシン、仮想ネットワーク、およびその他の依存リソースを作成します。 別のリソースが存在するまではデプロイできないリソースもあります。 たとえば、仮想マシンを作成するには、そのストレージ アカウントとネットワーク インターフェイスが存在している必要があります。 このリレーションシップは、一方のリソースと他方のリソースに依存関係を持たせることにより定義します。 Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 詳細については、「[Define the order for deploying resources in Azure Resource Manager Templates](./resource-group-define-dependencies.md)」 (Azure Resource Manager テンプレートでリソースをデプロイする順序を定義する) を参照してください。

![Resource Manager テンプレートの依存リソースのデプロイ順序を示す図](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートを調べる
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)。  [拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を参照してください。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="explore-the-template"></a>テンプレートを調べる

このセクションでテンプレートを調べるときは、次の質問に回答するようにしてください。

* このテンプレートに定義されている Azure リソースの数はいくつか。
* リソースの 1 つは、Azure Storage アカウントです。  定義は前回のチュートリアルで使用されたものと同じか。
* このテンプレートに定義されているリソースのテンプレート 参照を見つけることができるか。
* リソースの依存関係を見つけることができるか。

1. Visual Studio Code で、**resources** 内の最初のレベルの要素と 2 番目のレベルの要素のみが表示されるまで要素を折り畳みます。

    ![Visual Studio Code Azure Resource Manager テンプレート](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    テンプレートによって定義されたリソースは、5 つあります。

   * `Microsoft.Storage/storageAccounts` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)をご覧ください。
   * `Microsoft.Network/publicIPAddresses` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)をご覧ください。
   * `Microsoft.Network/virtualNetworks` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)をご覧ください。
   * `Microsoft.Network/networkInterfaces` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)をご覧ください。
   * `Microsoft.Compute/virtualMachines` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

2. 最初のリソースを展開します。 それはストレージ アカウントです。 リソース定義と[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)を比較します。

    ![Visual Studio Code の Azure Resource Manager テンプレートのストレージ アカウント定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. 2 番目のリソースを展開します。 リソースの種類は `Microsoft.Network/publicIPAddresses` です。 リソース定義と[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)を比較します。

    ![Visual Studio Code の Azure Resource Manager テンプレートのパブリック IP アドレス定義](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. 4 番目のリソースを展開します。 リソースの種類は `Microsoft.Network/networkInterfaces` です。  

    ![Visual Studio Code Azure Resource Manager テンプレートの dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 このリソースは他の 2 つのリソースに依存しています。

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. 5 番目の要素を展開します。 このリソースは、仮想マシンです。 次に示す他の 2 つのリソースに依存しています。

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

次の図は、このテンプレートのリソースと依存関係の情報を示しています。

![Visual Studio Code Azure Resource Manager テンプレートの依存関係図](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

依存関係を指定することにより、Resource Manager は、ソリューションを効率的にデプロイします。 ストレージ アカウント、パブリック IP アドレス、および仮想ネットワークは、依存関係がないため、同時にデプロイされます。 パブリック IP アドレスと仮想ネットワークがデプロイされると、ネットワーク インターフェイスが作成されます。 その他のすべてのリソースがデプロイされると、Resource Manager は、仮想マシンをデプロイします。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

テンプレートをデプロイする方法は多数あります。  このチュートリアルでは、Azure Portal から Cloud Shell を使用します。

1. [Cloud Shell](https://shell.azure.com) にサインインします。 
2. Cloud Shell の左上隅から **[PowerShell]** を選択し、**[確認]** を選択します。  このチュートリアルでは、PowerShell を使用します。
3. Cloud Shell から **[ファイルのアップロード]** を選択します。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. チュートリアルで前に保存したテンプレートを選択します。 既定の名前は **azuredeploy.json** です。  同じ名前のファイルが既にある場合は、古いファイルが通知なしに上書きされます。

    オプションで **ls $HOME** コマンドと **cat $HOME/azuredeploy.json** コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。 

5. Cloud Shell から、次の PowerShell コマンドを実行します。 セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 「[前提条件](#prerequisites)」を参照してください。

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. 次の PowerShell コマンドを実行して、新しく作成された仮想マシンの一覧を表示します。

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    仮想マシンの名前は、テンプレート内に **SimpleWinVM** としてハードコーディングされています。

9. 仮想マシンの確認を目的とする仮想マシンへの RDP が、正常に作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレートを作成してデプロイし、仮想マシン、仮想ネットワーク、および依存リソースを作成しました。 条件に基づいて Azure リソースをデプロイする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [使用条件](./resource-manager-tutorial-use-conditions.md)
