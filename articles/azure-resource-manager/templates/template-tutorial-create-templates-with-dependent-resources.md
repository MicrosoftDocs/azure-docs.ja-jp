---
title: 依存リソースを含むテンプレート
description: 複数のリソースを含む Azure Resource Manager テンプレート (ARM テンプレート) を作成し、Azure portal を使用してデプロイする方法を説明します。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f64e51a26e1f916f9f96704c55412a870a509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673938"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>チュートリアル:依存リソースを含む ARM テンプレートを作成する

Azure Resource Manager テンプレート (ARM テンプレート) を作成して、複数のリソースをデプロイしたりデプロイ順序を構成したりする方法を説明します。 テンプレートを作成したら、Azure portal から Cloud Shell を使用してテンプレートをデプロイします。

このチュートリアルでは、ストレージ アカウント、仮想マシン、仮想ネットワーク、およびその他の依存リソースを作成します。 別のリソースが存在するまではデプロイできないリソースもあります。 たとえば、仮想マシンを作成するには、そのストレージ アカウントとネットワーク インターフェイスが存在している必要があります。 このリレーションシップは、一方のリソースと他方のリソースに依存関係を持たせることにより定義します。 Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 詳細については、「[Azure Resource Manager テンプレートでのリソース デプロイ順序の定義](./define-resource-dependency.md)」を参照してください。

![Resource Manager テンプレートの依存リソースのデプロイ順序を示す図](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートを調べる
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

リソースの依存関係について取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、[ARM テンプレートのデプロイで Azure Key Vault を統合する](./template-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイックスタート テンプレートは、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、 **[ファイル]**  >  **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**  >  **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを _azuredeploy.json_ という名前でローカル コンピューターに保存します。

## <a name="explore-the-template"></a>テンプレートを調べる

このセクションでテンプレートを調べるときは、次の質問に回答するようにしてください。

* このテンプレートに定義されている Azure リソースの数はいくつか。
* リソースの 1 つは、Azure Storage アカウントです。 定義は前回のチュートリアルで使用されたものと同じか。
* このテンプレートに定義されているリソースのテンプレート 参照を見つけることができるか。
* リソースの依存関係を見つけることができるか。

1. Visual Studio Code で、`resources` 内の最初のレベルの要素と 2 番目のレベルの要素のみが表示されるまで要素を折り畳みます。

    ![Visual Studio Code ARM テンプレート](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    テンプレートによって定義されたリソースは、6 つあります。

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)。
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)。
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)。
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)。
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)。
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)。

     テンプレートをカスタマイズする前に、テンプレートのリファレンスを確認しておくと参考になります。

1. 最初のリソースを展開します。 それはストレージ アカウントです。 リソース定義と[テンプレート リファレンス](/azure/templates/Microsoft.Storage/storageAccounts)を比較します。

    ![Visual Studio Code ARM テンプレートのストレージ アカウント定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. 2 番目のリソースを展開します。 リソースの種類は `Microsoft.Network/publicIPAddresses` です。 リソース定義と[テンプレート リファレンス](/azure/templates/microsoft.network/publicipaddresses)を比較します。

    ![Visual Studio Code ARM テンプレートのパブリック IP アドレス定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. 3 番目のリソースを展開します。 リソースの種類は `Microsoft.Network/networkSecurityGroups` です。 リソース定義と[テンプレート リファレンス](/azure/templates/microsoft.network/networksecuritygroups)を比較します。

    ![Visual Studio Code ARM テンプレートのネットワーク セキュリティ グループ定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. 4 番目のリソースを展開します。 リソースの種類は `Microsoft.Network/virtualNetworks` です。

    ![Visual Studio Code ARM テンプレートの仮想ネットワークの dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    `dependsOn` 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 このリソースは、もう 1 つのリソースに依存しています。

    * `Microsoft.Network/networkSecurityGroups`

1. 5 番目の要素を展開します。 リソースの種類は `Microsoft.Network/networkInterfaces` です。 このリソースは他の 2 つのリソースに依存しています。

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. 6 番目のリソースを展開します。 このリソースは、仮想マシンです。 次に示す他の 2 つのリソースに依存しています。

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

次の図は、このテンプレートのリソースと依存関係の情報を示しています。

![Visual Studio Code ARM テンプレートの依存関係図](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

依存関係を指定することにより、Resource Manager は、ソリューションを効率的にデプロイします。 ストレージ アカウント、パブリック IP アドレス、および仮想ネットワークは、依存関係がないため、同時にデプロイされます。 パブリック IP アドレスと仮想ネットワークがデプロイされると、ネットワーク インターフェイスが作成されます。 その他のすべてのリソースがデプロイされると、Resource Manager は、仮想マシンをデプロイします。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。

1. 左上の **[PowerShell]** または **[Bash]** (CLI の場合) を選択して、希望の環境を選択します。  切り替えた場合は、シェルを再起動する必要があります。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。 先のスクリーンショットをご覧ください。 前に保存したファイルを選択します。 ファイルをアップロードした後、`ls` コマンドと `cat` コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。

1. 次の PowerShell スクリプトを実行してテンプレートをデプロイします。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. 仮想マシンの確認を目的とする仮想マシンへの RDP が、正常に作成されました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。 リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、テンプレートを作成してデプロイし、仮想マシン、仮想ネットワーク、および依存リソースを作成しました。 デプロイ スクリプトを使用してデプロイ前後の操作を実行する方法については、次のページを参照してください。

> [!div class="nextstepaction"]
> [デプロイ スクリプトの使用](./template-tutorial-deployment-script.md)
