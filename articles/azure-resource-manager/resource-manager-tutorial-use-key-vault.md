---
title: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する | Microsoft Docs
description: Azure Key Vault を使用して Resource Manager テンプレートのデプロイ時にセキュリティで保護されたパラメーターの値を渡す方法について説明します
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436576"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>チュートリアル:Resource Manager テンプレートのデプロイで Azure Key Vault を統合する

Azure のキー コンテナーからシークレットを取得し、Azure Resource Manager のデプロイ時にシークレットをパラメーターとして渡す方法を説明します。 キー コンテナー ID のみを参照するため、パラメーター値が公開されることはありません。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください

「[リソースのデプロイ順序の設定](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」チュートリアルでは、仮想マシン (VM) を作成します。 VM 管理者のユーザー名とパスワードを指定する必要があります。 パスワードを指定する代わりに、Azure Key Vault にパスワードを事前に格納しておき、デプロイ時にキー コンテナーからパスワードを取得するようテンプレートをカスタマイズすることができます。

![Resource Manager テンプレートとキー コンテナーの統合を示す図](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * キー コンテナーを準備する
> * クイック スタート テンプレートを開く
> * パラメーター ファイルを編集する
> * テンプレートのデプロイ
> * デプロイの検証
> * リソースのクリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を持つ [Visual Studio Code](https://code.visualstudio.com/)。
* セキュリティを向上させるために、生成されたパスワードを VM 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    生成されたパスワードが、VM のパスワード要件を満たしていることを確認します。 各 Azure サービスには、特定のパスワード要件があります。 VM のパスワード要件については、[VM を作成するときのパスワードの要件とは何か](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)に関するページを参照してください。

## <a name="prepare-a-key-vault"></a>キー コンテナーを準備する

このセクションでは、テンプレートをデプロイするときにシークレットを取得できるように、キー コンテナーを作成し、それにシークレットを追加します。 キー コンテナーを作成するにはさまざまな方法があります。 このチュートリアルでは、Azure PowerShell を使用して [Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)をデプロイします。 このテンプレートは、次の処理を実行します。

* `enabledForTemplateDeployment` プロパティを有効にしてキー コンテナーを作成します。 テンプレートのデプロイ プロセスが、このキー コンテナーで定義されているシークレットにアクセスできるようにするには、このプロパティを *true* にする必要があります。
* キー コンテナーにシークレットを追加します。 シークレットは、VM の管理者パスワードを格納します。

> [!NOTE]
> 仮想マシンのテンプレートをデプロイするユーザーが、キー コンテナーの所有者または共同作成者でない場合、所有者または共同作成者は、キー コンテナーの *Microsoft.KeyVault/vaults/deploy/action* アクセス許可へのアクセス権をユーザーに付与する必要があります。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください。

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
```

> [!IMPORTANT]
> * リソース グループ名は、**rg** が追加されたプロジェクト名です。 [このチュートリアルで作成したリソースのクリーンアップ](#clean-up-resources)を容易にするには、[次のテンプレートをデプロイする](#deploy-the-template)ときに、同じプロジェクト名およびリソース グループ名を使用します。
> * シークレットの既定の名前は **vmAdminPassword** です。 これは、テンプレート内でハードコードされています。
> * テンプレートによってシークレットを取得できるようにするには、キー コンテナーに対する [テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする] アクセス ポリシーを有効にする必要があります。 このポリシーは、テンプレートで有効にします。 アクセス ポリシーの詳細については、「[キー コンテナーとシークレットをデプロイする](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets)」を参照してください。

テンプレートには、*keyVaultId* という 1 つの出力値があります。 仮想マシンをデプロイするときに、後で使用するために ID 値を書き留めておきます。 リソース ID の形式は、次のとおりです。

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

ID をコピーして貼り付けると、ID が複数の行に分かれてしまう場合があります。 行をマージして、余分なスペースを削除してください。

デプロイを検証するには、同じシェル ウィンドウで次の PowerShell コマンドを実行して、シークレットをクリア テキストで取得します。 このコマンドは、前述の PowerShell スクリプトで定義された *$keyVaultName* 変数を使用しているため、同じシェル セッションでのみ機能します。

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

キー コンテナーとシークレットの準備ができました。 以降のセクションでは、デプロイ時にシークレットを取得するように既存のテンプレートをカスタマイズする方法について説明します。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM (単純な Windows VM をデプロイする)](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」と呼ばれます。

1. Visual Studio Code の **[ファイル]**  >  **[ファイルを開く]** を選択します。

1. **[ファイル名]** ボックスに次の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。 シナリオは、「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」で使用されたものと同じテンプレートです。
   このテンプレートには、次の 5 つのリソースが定義されています。

   * `Microsoft.Storage/storageAccounts` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)をご覧ください。
   * `Microsoft.Network/publicIPAddresses` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)をご覧ください。
   * `Microsoft.Network/virtualNetworks` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)をご覧ください。
   * `Microsoft.Network/networkInterfaces` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)をご覧ください。
   * `Microsoft.Compute/virtualMachines` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

   カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

1. **[ファイル]**  >  **[名前を付けて保存]** の順に選択し、このファイルのコピーを *azuredeploy.json* という名前でローカル コンピューターに保存します。

1. 手順 1. から 3. を繰り返して、次の URL を開き、ファイルを *azuredeploy.parameters.json* として保存します。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>パラメーター ファイルを編集する

テンプレート ファイルに変更を加える必要はありません。

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
    > **id** の値を、前の手順で作成したキー コンテナーのリソース ID に置き換えます。

    ![キー コンテナーと Resource Manager テンプレートの仮想マシンのデプロイ パラメーター ファイルを統合する](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. 次の値を更新します。

    * **adminUsername**: 仮想マシンの管理者アカウントの名前。
    * **dnsLabelPrefix**: dnsLabelPrefix 値の名前。

    名前の例については、前の画像を参照してください。

1. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

「[テンプレートのデプロイ](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)」の手順に従います。 *azuredeploy.json* と *azuredeploy.parameters.json* の両方を Cloud Shell にアップロードし、次の PowerShell スクリプトを使用してテンプレートをデプロイします。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

テンプレートをデプロイするときに、キー コンテナーで使用したのと同じリソース グループを使用します。 このようにすると、2 つではなく 1 つのリソース グループのみを削除すればよいため、リソースのクリーンアップが簡単になります。

## <a name="validate-the-deployment"></a>デプロイの検証

仮想マシンが正常にデプロイされたら、キー コンテナーに格納されているパスワードを使用してサインイン資格情報をテストします。

1. [Azure Portal](https://portal.azure.com)を開きます。

1. **[リソース グループ]**  >  **[\<*YourResourceGroupName*>]**  >  **[simpleWinVM]** の順に選択します。
1. 上部の **[接続]** を選択します。
1. **[RDP ファイルのダウンロード]** を選択します。その後、指示に従って、キー コンテナーに格納されているパスワードを使用して仮想マシンにサインインします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Key Vault からシークレットを取得しました。 その後、テンプレートのデプロイで、シークレットを使用しました。 リンク済みテンプレートを作成する方法についての説明は、次を参照してください:

> [!div class="nextstepaction"]
> [リンク済みテンプレートを作成する](./resource-manager-tutorial-create-linked-templates.md)
