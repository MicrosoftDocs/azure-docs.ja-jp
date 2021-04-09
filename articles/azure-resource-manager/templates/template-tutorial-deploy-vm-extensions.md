---
title: テンプレートを使用して VM 拡張機能をデプロイする
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して仮想マシン拡張機能をデプロイする方法について説明します。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e04006a0908832c623230d89caa62b0985f32e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587946"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>チュートリアル:ARM テンプレートを使用して仮想マシン拡張機能をデプロイする

[Azure 仮想マシン拡張機能](../../virtual-machines/extensions/features-windows.md)を使用して、Azure VM でデプロイ後の構成タスクと自動化タスクを実行する方法について説明します。 Azure VM と共に、多くのさまざまな VM 拡張機能を使用できます。 このチュートリアルでは、Azure Resource Manager テンプレート (ARM テンプレート) からカスタム スクリプト拡張機能をデプロイして、Windows VM で PowerShell スクリプトを実行します。 スクリプトによって Web サーバーが VM にインストールされます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * PowerShell スクリプトを準備する
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、[ARM テンプレートのデプロイで Azure Key Vault を統合する](./template-tutorial-use-key-vault.md)方法に関するチュートリアルを参照してください。 また、パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-powershell-script"></a>PowerShell スクリプトを準備する

インライン PowerShell スクリプトまたはスクリプト ファイルを使用することができます。 このチュートリアルでは、スクリプト ファイルの使用方法について説明します。 次の内容が含まれた PowerShell スクリプトは、[GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1) から共有されます。

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

ファイルを独自の場所に発行する場合は、チュートリアルの後半でテンプレートの `fileUri` 要素を更新する必要があります。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイックスタート テンプレートは、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code の **[ファイル]**  >  **[ファイルを開く]** を選択します。
1. **[ファイル名]** ボックスに次の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. ファイルを開くには、 **[開く]** を選択します。
    このテンプレートには、次の 5 つのリソースが定義されています。

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)。
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)。
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)。
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)。
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)。
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。

1. **[ファイル]**  >  **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを *azuredeploy.json* という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

次の内容を使用して、仮想マシン拡張機能リソースを既存のテンプレートに追加します。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
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
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

このリソース定義の詳細については、[拡張機能のリファレンス](/azure/templates/microsoft.compute/virtualmachines/extensions)を参照してください。 以下にいくつかの重要な要素を示します。

* `name`:拡張機能リソースが仮想マシン オブジェクトの子リソースであるため、名前には仮想マシン名のプレフィックスが含まれている必要があります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。
* `dependsOn`:仮想マシンの作成後に拡張機能リソースを作成します。
* `fileUris`: スクリプト ファイルが格納される場所です。 提供された場所を使用しない場合は、値を更新する必要があります。
* `commandToExecute`:このコマンドによってスクリプトが呼び出されます。

インライン スクリプトを使用するには、`fileUris` を削除し、`commandToExecute` を次のように更新します。

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

このインライン スクリプトは、_iisstart.html_ の内容も更新します。

Web サーバーにアクセスできるように、HTTP ポートを開放する必要もあります。

1. テンプレートで `securityRules` を探します。
1. **default-allow-3389** の横に次のルールを追加します。

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、「[チュートリアル: 依存リソースを含む ARM テンプレートを作成する](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)」の「**テンプレートのデプロイ**」セクションを参照してください。 生成されたパスワードを仮想マシンの管理者アカウントに対して使用することが推奨されます。 この記事の「[前提条件](#prerequisites)」セクションを参照してください。

Cloud Shell から次のコマンドを実行して、VM のパブリック IP アドレスを取得します。

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

IP アドレスを Web ブラウザーに貼り付けます。 インターネット インフォメーション サービス (IIS) の既定のウェルカム ページ (下記) が表示されます。

![インターネット インフォメーション サービスのウェルカム ページ](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

デプロイした Azure リソースが不要になったら、リソース グループを削除してそれらをクリーンアップしてください。

1. Azure portal の左側のウィンドウで、 **[リソース グループ]** を選択します。
2. **[名前でフィルター]** ボックスにリソース グループの名前を入力します。
3. リソース グループ名を選択します。
    リソース グループには 6 つのリソースが表示されます。
4. 一番上のメニューで、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシンと仮想マシン拡張機能をデプロイしました。 拡張機能によって、仮想マシンに IIS Web サーバーがインストールされました。 Azure SQL Database 拡張機能を使用して BACPAC ファイルをインポートする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [SQL 拡張機能のデプロイ](./template-tutorial-deploy-sql-extensions-bacpac.md)
