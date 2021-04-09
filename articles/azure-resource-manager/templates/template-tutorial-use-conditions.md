---
title: テンプレートでの条件の使用
description: 条件に基づいて Azure リソースをデプロイする方法について説明します。 新しいリソースをデプロイしたり既存のリソースを使用したりする方法を紹介します。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696026"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>チュートリアル:ARM テンプレートでの条件の使用

Azure Resource Manager テンプレート (ARM テンプレート) で条件に基づいて Azure リソースをデプロイする方法について説明します。

[リソースのデプロイ順序の設定](./template-tutorial-create-templates-with-dependent-resources.md)に関するチュートリアルでは、仮想マシン、仮想ネットワーク、およびその他の依存リソース (ストレージ アカウントなど) を作成します。 新しいストレージ アカウントを毎回作成する代わりに、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用するかをユーザーに選択してもらいます。 この目的を達成するために、追加のパラメーターを定義します。 パラメーターの値に **new** が指定されると、新しいストレージ アカウントが作成されます。 それ以外の場合、名前が指定された既存のストレージ アカウントが使用されます。

![条件を使用する Resource Manager テンプレートの図](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの変更
> * テンプレートのデプロイ
> * リソースをクリーンアップする

このチュートリアルでは、条件を使用する基本的なシナリオについてのみ説明します。 詳細については、次を参照してください。

* [テンプレート ファイルの構造:条件](conditional-resource-deployment.md)。
* [ARM テンプレートのリソースを条件付きでデプロイする](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy)。
* [テンプレート関数:If](./template-functions-logical.md#if)。
* [Azure Resource Manager テンプレートの比較関数](./template-functions-comparison.md)

条件について取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

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
1. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。
1. テンプレートによって定義されたリソースは、6 つあります。

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)。
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)。
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)。
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)。
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)。
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)。

    テンプレートをカスタマイズする前に、テンプレートのリファレンスを確認しておくと参考になります。

1. **[ファイル]**  >  **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを _azuredeploy.json_ という名前でローカル コンピューターに保存します。

## <a name="modify-the-template"></a>テンプレートの変更

既存のテンプレートに 2 つの変更を加えます。

* ストレージ アカウント名パラメーターを追加します。 ユーザーは、新しいストレージ アカウント名を指定するか、または既存のストレージ アカウント名を指定することができます。
* `newOrExisting` という新しいパラメーターを追加します。 デプロイする際には、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用するかどうかが、このパラメーターを使用して判別されます。

変更を行う手順は次のとおりです。

1. Visual Studio Code で _azuredeploy.json_ を開きます。
1. テンプレート全体で 3 つの `variables('storageAccountName')` を `parameters('storageAccountName')` に置き換えます。
1. 次の変数定義を削除します。

    ![削除する必要のある変数定義が強調表示されているスクリーンショット。](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. parameters セクションの先頭に、次の 2 つのパラメーターを追加します。

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Visual Studio Code で Alt + Shift + F キーを押してテンプレートの書式を設定します。

    更新したパラメーター定義は次のようになります。

    ![Resource Manager での条件の使用](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. ストレージ アカウント定義の先頭に次の行を追加します。

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    この条件では、パラメーター `newOrExisting` の値が確認されます。 パラメーターの値に **new** が指定されていると、ストレージ アカウントが作成されます。

    更新したストレージ アカウント定義は次のようになります。

    ![更新したストレージ アカウント定義を示すスクリーンショット。](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. 仮想マシンのリソース定義の `storageUri` プロパティを次の値に更新します。

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    この変更は、異なるリソース グループの既存のストレージ アカウントを使用するときに必要になります。

1. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。

1. 左上の **[PowerShell]** または **[Bash]** (CLI の場合) を選択して、希望の環境を選択します。 切り替えた場合は、シェルを再起動する必要があります。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。 先のスクリーンショットをご覧ください。 前のセクションで保存したファイルを選択します。 ファイルをアップロードした後、`ls` コマンドと `cat` コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。

1. 次の PowerShell スクリプトを実行してテンプレートをデプロイします。

    > [!IMPORTANT]
    > ストレージ アカウント名は Azure 内で一意である必要があります。 名前に使用できるのは、小文字と数字だけです。 24 文字以内にする必要があります。 ストレージ アカウント名は、プロジェクト名に **store** が追加されたものです。 プロジェクト名と生成されたストレージ アカウント名がストレージ アカウント名の要件を満たしていることを確認してください。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > `newOrExisting` が **new** のときに、指定したストレージ アカウント名のストレージ アカウントが既に存在している場合、デプロイは失敗します。

`newOrExisting` を **existing** に設定した別のデプロイを作成して、既存のストレージ アカウントを指定します。 事前にストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](../../storage/common/storage-account-create.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 リソース グループを削除するには、 **[Try it]/(試してみる/)** を選択し Cloud Shell を開きます。 PowerShell スクリプトを貼り付けるには、シェル ウィンドウを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用するかをユーザーが選択できるテンプレートを作成しました。 Azure Key Vault からシークレットを取得し、テンプレートのデプロイでそのシークレットをパスワードとして使用する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [テンプレートのデプロイで Key Vault を統合する](./template-tutorial-use-key-vault.md)
