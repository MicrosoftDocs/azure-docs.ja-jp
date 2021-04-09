---
title: テンプレートでの Azure Key Vault の使用
description: Azure Key Vault を使用して Azure Resource Manager テンプレート (ARM テンプレート) のデプロイ時にセキュリティで保護されたパラメーターの値を渡す方法について説明します。
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 388996dc0054192f6d9f3c87e11ca1d15e8a85e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703887"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>チュートリアル:ARM テンプレートのデプロイで Azure Key Vault を統合する

Azure のキー コンテナーからシークレットを取得し、Azure Resource Manager テンプレート (ARM テンプレート) のデプロイ時にシークレットをパラメーターとして渡す方法を説明します。 キー コンテナー ID のみを参照するため、パラメーター値が公開されることはありません。 キー コンテナーのシークレットは、静的 ID または動的 ID を使用して参照することができます。 このチュートリアルでは、静的 ID を使用します。 静的 ID を使用する場合、テンプレート ファイルではなく、テンプレート パラメーター ファイルでキー コンテナーを参照します。 両方のアプローチの詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./key-vault-parameter.md)」を参照してください

「[リソースのデプロイ順序の設定](./template-tutorial-create-templates-with-dependent-resources.md)」チュートリアルでは、仮想マシン (VM) を作成します。 VM 管理者のユーザー名とパスワードを指定する必要があります。 パスワードを指定する代わりに、Azure Key Vault にパスワードを事前に格納しておき、デプロイ時にキー コンテナーからパスワードを取得するようテンプレートをカスタマイズすることができます。

![Resource Manager テンプレートとキー コンテナーの統合を示す図](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * キー コンテナーを準備する
> * クイック スタート テンプレートを開く
> * パラメーター ファイルを編集する
> * テンプレートのデプロイ
> * デプロイの検証
> * リソースをクリーンアップする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

キー コンテナーのセキュリティで保護された値を使用する Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
* セキュリティを向上させるために、生成されたパスワードを VM 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```console
    openssl rand -base64 32
    ```

    生成されたパスワードが、VM のパスワード要件を満たしていることを確認します。 各 Azure サービスには、特定のパスワード要件があります。 VM のパスワード要件については、[VM を作成するときのパスワードの要件とは何か](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)に関するページを参照してください。

## <a name="prepare-a-key-vault"></a>キー コンテナーを準備する

このセクションでは、テンプレートをデプロイするときにシークレットを取得できるように、キー コンテナーを作成し、それにシークレットを追加します。 キー コンテナーを作成するにはさまざまな方法があります。 このチュートリアルでは、Azure PowerShell を使用して [ARM テンプレート](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)をデプロイします。 このテンプレートは、次の 2 つのことを行います。

* `enabledForTemplateDeployment` プロパティを有効にしてキー コンテナーを作成します。 テンプレートのデプロイ プロセスが、このキー コンテナーで定義されているシークレットにアクセスできるようにするには、このプロパティを *true* にする必要があります。
* キー コンテナーにシークレットを追加します。 シークレットは、VM の管理者パスワードを格納します。

> [!NOTE]
> 仮想マシンのテンプレートをデプロイするユーザーが、キー コンテナーの所有者または共同作成者でない場合、所有者または共同作成者は、キー コンテナーの `Microsoft.KeyVault/vaults/deploy/action` アクセス許可へのアクセス権をユーザーに付与する必要があります。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./key-vault-parameter.md)」を参照してください。

次の Azure PowerShell スクリプトを実行するには、 **[使ってみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェル ウィンドウを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * リソース グループ名は、**rg** が追加されたプロジェクト名です。 [このチュートリアルで作成したリソースのクリーンアップ](#clean-up-resources)を容易にするには、[次のテンプレートをデプロイする](#deploy-the-template)ときに、同じプロジェクト名およびリソース グループ名を使用します。
> * シークレットの既定の名前は **vmAdminPassword** です。 これは、テンプレート内でハードコードされています。
> * テンプレートによってシークレットを取得できるようにするには、キー コンテナーに対する **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** アクセス ポリシーを有効にする必要があります。 このポリシーは、テンプレートで有効にします。 アクセス ポリシーの詳細については、「[キー コンテナーとシークレットをデプロイする](./key-vault-parameter.md#deploy-key-vaults-and-secrets)」を参照してください。

テンプレートには、`keyVaultId` という 1 つの出力値があります。 後でこのチュートリアルの中でシークレット値を取得する際、シークレット名と共にこの ID を使用することになります。 リソース ID の形式は、次のとおりです。

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

ID をコピーして貼り付けると、ID が複数の行に分かれてしまう場合があります。 行をマージして、余分なスペースを削除してください。

デプロイを検証するには、同じシェル ウィンドウで次の PowerShell コマンドを実行して、シークレットをクリア テキストで取得します。 このコマンドは、前述の PowerShell スクリプトで定義された `$keyVaultName` 変数を使用しているため、同じシェル セッションでのみ機能します。

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

キー コンテナーとシークレットの準備ができました。 以降のセクションでは、デプロイ時にシークレットを取得するように既存のテンプレートをカスタマイズする方法について説明します。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイックスタート テンプレートは、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM (単純な Windows VM をデプロイする)](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」と呼ばれます。

1. Visual Studio Code の **[ファイル]**  >  **[ファイルを開く]** を選択します。

1. **[ファイル名]** ボックスに次の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。 シナリオは、「[チュートリアル: 依存リソースを含む ARM テンプレートを作成する](./template-tutorial-create-templates-with-dependent-resources.md)」の「テンプレートのデプロイ」セクションを参照してください。
   このテンプレートには、次の 6 つのリソースが定義されています。

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)。
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)。
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)。
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)。
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)。
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)。

   カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

1. **[ファイル]**  >  **[名前を付けて保存]** の順に選択し、このファイルのコピーを *azuredeploy.json* という名前でローカル コンピューターに保存します。

1. 手順 1. から 3. を繰り返して、次の URL を開き、ファイルを *azuredeploy.parameters.json* として保存します。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>パラメーター ファイルを編集する

静的 ID を使用した方法では、テンプレート ファイルに変更を加える必要はありません。 シークレット値の取得は、テンプレート パラメーター ファイルを構成することによって行います。

1. Visual Studio Code で、*azuredeploy.parameters.json* がまだ開かれていない場合は、開きます。
1. `adminPassword` パラメーターを次のように更新します。

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
                "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > `id` の値を、前の手順で作成したキー コンテナーのリソース ID に置き換えます。 `secretName` は **vmAdminPassword** としてハードコーディングされます。  「[キー コンテナーを準備する](#prepare-a-key-vault)」を参照してください。

    ![キー コンテナーと Resource Manager テンプレートの仮想マシンのデプロイ パラメーター ファイルを統合する](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. 次の値を更新します。

    * `adminUsername`:仮想マシンの管理者アカウントの名前。
    * `dnsLabelPrefix`:`dnsLabelPrefix` の値に名前を指定します。

    名前の例については、前の画像を参照してください。

1. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。

1. 左上の **[PowerShell]** または **[Bash]** (CLI の場合) を選択して、希望の環境を選択します。  切り替えた場合は、シェルを再起動する必要があります。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。 *azuredeploy.json* と *azuredeploy.parameters.json* の両方を Cloud Shell にアップロードします。 ファイルをアップロードした後、`ls` コマンドと `cat` コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。

1. 次の PowerShell スクリプトを実行してテンプレートをデプロイします。

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    テンプレートをデプロイするときに、キー コンテナーで使用したのと同じリソース グループを使用します。 このようにすると、2 つではなく 1 つのリソース グループのみを削除すればよいため、リソースのクリーンアップが簡単になります。

## <a name="validate-the-deployment"></a>デプロイの検証

仮想マシンが正常にデプロイされたら、キー コンテナーに格納されているパスワードを使用してサインイン資格情報をテストします。

1. [Azure Portal](https://portal.azure.com)を開きます。

1. **[リソース グループ]**  >  **\<*YourResourceGroupName*>**  >  **[simpleWinVM]** の順に選択します。
1. 上部の **[接続]** を選択します。
1. **[RDP ファイルのダウンロード]** を選択します。その後、指示に従って、キー コンテナーに格納されているパスワードを使用して仮想マシンにサインインします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Key Vault からシークレットを取得しました。 その後、テンプレートのデプロイで、シークレットを使用しました。 仮想マシン拡張機能を使用してデプロイ後のタスクを実行する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [仮想マシン拡張機能のデプロイ](./template-tutorial-deploy-vm-extensions.md)
