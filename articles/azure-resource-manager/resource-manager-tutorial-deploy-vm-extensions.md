---
title: Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする方法について説明します
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a40619000998d7222781094db2829aabcc6a7fb2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100770"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>チュートリアル:Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする

[Azure 仮想マシン拡張機能](../virtual-machines/extensions/features-windows.md)を使用して、Azure VM でデプロイ後の構成タスクと自動化タスクを実行する方法について説明します。 Azure VM と共に、多くのさまざまな VM 拡張機能を使用できます。 このチュートリアルでは、Azure Resource Manager テンプレートからカスタム スクリプト拡張機能をデプロイして、Windows VM で PowerShell スクリプトを実行します。  スクリプトによって Web サーバーが VM にインストールされます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * PowerShell スクリプトを準備する
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * デプロイを検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)。 [拡張機能のインストール](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)に関する記事を参照してください。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 また、パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-powershell-script"></a>PowerShell スクリプトを準備する

次の内容が含まれた PowerShell スクリプトは、[パブリック アクセスが有効な Azure ストレージ アカウント](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1)から共有されます。

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

ファイルを独自の場所に発行する場合は、チュートリアルの後半でテンプレートの `fileUri` 要素を更新する必要があります。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code の **[ファイル]** > **[ファイルを開く]** を選択します。
1. **[ファイル名]** ボックスに https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json という URL を貼り付けます。

1. ファイルを開くには、**[開く]** を選択します。  
    このテンプレートには、次の 5 つのリソースが定義されています。

   * **Microsoft.Storage/storageAccounts**。 [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)をご覧ください。
   * **Microsoft.Network/publicIPAddresses**。 [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)をご覧ください。
   * **Microsoft.Network/virtualNetworks**。 [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)をご覧ください。
   * **Microsoft.Network/networkInterfaces**。 [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)をご覧ください。
   * **Microsoft.Compute/virtualMachines**。 [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

1. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを *azuredeploy.json* という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

次の内容を使用して、仮想マシン拡張機能リソースを既存のテンプレートに追加します。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

このリソース定義の詳細については、[拡張機能のリファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions)を参照してください。 以下にいくつかの重要な要素を示します。

* **name**:拡張機能リソースが仮想マシン オブジェクトの子リソースであるため、名前には仮想マシン名のプレフィックスが含まれている必要があります。 「[子リソース](./resource-group-authoring-templates.md#child-resources)」を参照してください。
* **dependsOn**:仮想マシンの作成後に拡張機能リソースを作成します。
* **fileUris**: スクリプト ファイルが格納される場所です。 提供された場所を使用しない場合は、値を更新する必要があります。
* **commandToExecute**: このコマンドによってスクリプトが呼び出されます。  

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)」で使用されたものと同じテンプレートです。 生成されたパスワードを仮想マシンの管理者アカウントに対して使用することが推奨されます。 この記事の「[前提条件](#prerequisites)」セクションを参照してください。

## <a name="verify-the-deployment"></a>デプロイを検証する

1. Azure portal で VM を選択します。
1. VM の概要で、**[クリックしてコピー]** を選択して IP アドレスをコピーし、ブラウザーのタブに貼り付けます。  
   インターネット インフォメーション サービス (IIS) の既定のウェルカム ページ (下記) が表示されます。

![インターネット インフォメーション サービスのウェルカム ページ](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

デプロイした Azure リソースが不要になったら、リソース グループを削除してそれらをクリーンアップしてください。

1. Azure portal の左側のウィンドウで、**[リソース グループ]** を選択します。
2. **[名前でフィルター]** ボックスにリソース グループの名前を入力します。
3. リソース グループ名を選択します。  
    リソース グループには 6 つのリソースが表示されます。
4. 一番上のメニューで、**[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、仮想マシンと仮想マシン拡張機能をデプロイしました。 拡張機能によって、仮想マシンに IIS Web サーバーがインストールされました。 Azure SQL Database 拡張機能を使用して BACPAC ファイルをインポートする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [SQL 拡張機能のデプロイ](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
