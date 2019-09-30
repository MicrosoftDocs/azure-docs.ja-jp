---
title: リンクされた Azure Resource Manager テンプレートの作成 | Microsoft Docs
description: 仮想マシンを作成するためにリンクされた Azure Resource Manager テンプレートを作成する方法
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c5399f46106d94d593a15530ee0c223a3f5f3eaf
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802051"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>チュートリアル:リンクされた Azure Resource Manager テンプレートの作成

リンクされた Azure Resource Manager テンプレートを作成する方法について説明します。 リンクされたテンプレートを使用すると、あるテンプレートから別のテンプレートを呼び出すことができます。 これは、テンプレートをモジュール化する場合に役立ちます。 このチュートリアルでは、「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」で使用したものと同じテンプレートを使用し、仮想マシン、仮想ネットワーク、その他の依存リソース (ストレージ アカウントを含む) を作成します。 リンクされたテンプレートにストレージ アカウントのリソース作成を分離します。

リンクされたテンプレートの呼び出しは、関数の呼び出しと似ています。  また、リンクされたテンプレートにパラメーター値を渡す方法と、リンクされたテンプレートから "戻り値" を取得する方法についても説明します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * リンクされたテンプレートを作成する
> * リンクされたテンプレートをアップロードする
> * リンクされたテンプレートにリンクする
> * 依存関係を構成する
> * テンプレートのデプロイ
> * 追加のプラクティス

詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用](./resource-group-linked-templates.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/) と [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。 これは「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」で使用されたものと同じテンプレートです。 以下と同じ使用するテンプレートの 2 つのコピーを保存します。

* **メイン テンプレート**: ストレージ アカウントを除くすべてのリソースを作成します。
* **リンクされたテンプレート**: ストレージ アカウントを作成します。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. テンプレートによって定義されたリソースは、5 つあります。

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     テンプレートをカスタマイズする前にテンプレート スキーマの基本をある程度理解することは役に立ちます。
5. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。
6. **[ファイル]** > **[名前を付けて保存]** を選択し、このファイルの別のコピーを **linkedTemplate.json** という名前で作成します。

## <a name="create-the-linked-template"></a>リンクされたテンプレートを作成する

リンクされたテンプレートにより、ストレージ アカウントが作成されます。 リンクされたテンプレートは、ストレージ アカウントを作成するスタンドアロンのテンプレートとして使用できます。 このチュートリアルでは、リンクされたテンプレートで 2 つのパラメーターを受け取り、値をメイン テンプレートに渡し返します。 この "戻り" 値は `outputs` 要素で定義されます。

1. **linkedTemplate.json** が開いていない場合、このファイルを Visual Studio Code で開きます。
2. 次の変更を行います。

    * **location** 以外のパラメーターをすべて削除します。
    * **storageAccountName** というパラメーターを追加します。
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        ストレージ アカウント名と場所は、パラメーターとして、メイン テンプレートからリンクされたテンプレートに渡されます。

    * **variables** 要素とすべての変数定義を削除します。
    * ストレージ アカウント以外のすべてのリソースを削除します。 合計で 4 つのリソースを削除します。
    * ストレージ アカウント リソースの **name** 要素の値を次の目的で更新します。

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * **outputs** 要素を更新します。その結果、次のようになります。

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       **storageUri**は、メイン テンプレートの仮想マシン リソース定義で必要です。  この値を、出力値としてメイン テンプレートに値を渡し返します。

        完了すると、テンプレートは次のようになります。

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. 変更を保存します。

## <a name="upload-the-linked-template"></a>リンクされたテンプレートをアップロードする

メイン テンプレートとリンクされたテンプレートには、デプロイを実行する場所からアクセス可能にする必要があります。 このチュートリアルでは、「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」で使用されたものと同じテンプレートです。 メイン テンプレート (azuredeploy.json) は、シェルにアップロードされます。 リンクされたテンプレート (linkedTemplate.json) は、別の場所で安全に共有する必要があります。 次の PowerShell スクリプトでは、Azure Storage アカウントが作成され、ストレージ アカウントにテンプレートがアップロードされ、テンプレート ファイルへの制限付きアクセスを付与する目的で SAS トークンが生成されます。 チュートリアルを簡単にするために、スクリプトによって、リンクされたテンプレートが完全な形で共有の場所からダウンロードされます。 自分で作成したリンク済みテンプレートを使用する場合、[Cloud Shell](https://shell.azure.com) を使用してリンク済みテンプレートをアップロードし、独自のリンク済みテンプレートを使用するようにスクリプトを変更します。

> [!NOTE]
> スクリプトによって、8 時間以内に使用するように SAS トークンに制限が与えられます。 このチュートリアルを完了するためにもっと時間が必要な場合、有効期限を延ばしてください。

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. 緑のボタン **[試してみる]** を選択し、Azure Cloud Shell ウィンドウが開きます。
2. **[コピー]** を選択し、PowerShell スクリプトがコピーされます。
3. Shell ウィンドウ (濃紺色の部分) 内のどこでもよいので右クリックし、 **[貼り付け]** を選択します。
4. Shell ウィンドウの端にある 2 つの値をメモします (リソース グループ名とリンク済みテンプレート URI)。 値は、このチュートリアルの後の方で必要になります。
5. **[フォーカス モードの終了]** を選択し、Shell ウィンドウを閉じます。

実際には、メイン テンプレートをデプロイするときに SAS トークンを生成します。また、安全性を高める目的で、SAS トークンの有効期限を短くします。 詳細については、「[デプロイ時に SAS トークンを指定する](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)」を参照してください。

## <a name="call-the-linked-template"></a>リンクされたテンプレートを呼び出す

メイン テンプレートは azuredeploy.json と呼ばれます。

1. **azuredeploy.json** が開いていない場合、Visual Studio Code で開きます。
2. テンプレートからストレージ アカウント リソース定義を削除します。

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. 次の json スニペットを、ストレージ アカウント定義があった場所に追加します。

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    次の点に注意してください。

    * メイン テンプレートの `Microsoft.Resources/deployments` リソースは、別のテンプレートにリンクするために使用されます。
    * `deployments` リソースは、`linkedTemplate` という名前を持っています。 この名前は、[依存関係を構成する](#configure-dependency)場合に使用されます。
    * リンクされたテンプレートを呼び出すときは、[増分](./deployment-modes.md)デプロイ モードのみを使用できます。
    * `templateLink/uri` には、リンクされたテンプレート URI が含まれています。 (SAS トークン付きの) リンクされたテンプレートをアップロードしたときに与えられた URI にこの値を更新します。
    * メイン テンプレートから、リンクされたテンプレートに値を渡すには、`parameters` を使用します。
4. (SAS トークン付きの) リンクされたテンプレートをアップロードしたときに与えられた値に `uri` 要素の値が更新されていることを確認します。 実際には、URI にパラメーターを与えることをお勧めします。
5. 変更後のテンプレートを保存します。

## <a name="configure-dependency"></a>依存関係を構成する

「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)」の内容を思い出してください。仮想マシンのリソースはストレージ アカウントに依存します。

![Azure Resource Manager テンプレートの依存関係図](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

ストレージ アカウントが、リンクされたテンプレートで定義されているので、`Microsoft.Compute/virtualMachines` リソースの次の 2 つの要素を更新する必要があります。

* `dependsOn` 要素を再構成します。 ストレージ アカウントの定義が、リンクされたテンプレートに移動されます。
* `properties/diagnosticsProfile/bootDiagnostics/storageUri` 要素を再構成します。 「[リンクされたテンプレートを作成する](#create-the-linked-template)」で、出力値を追加しました。

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    この値は、メイン テンプレートで必要です。

1. azuredeploy.json が開いていない場合、Visual Studio Code で開きます。
2. 仮想マシン リソース定義を展開し、次のスクリーンショットに示すように、**dependsOn** を更新します。

    ![Azure Resource Manager のリンクされたテンプレートで依存関係を構成する](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    "*linkedTemplate*" は、デプロイ リソースの名前です。
3. 前のスクリーンショットに示すように、**properties/diagnosticsProfile/bootDiagnostics/storageUri** を更新します。
4. 変更後のテンプレートを保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)」セクションを参照してください。 リンクされたテンプレートを保存するためのストレージ アカウントと同じリソース グループ名を使用します。 次のセクションでリソースをクリーンアップする作業が楽になります。 セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 「[前提条件](#prerequisites)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="additional-practice"></a>追加のプラクティス

プロジェクトを改善する目的で、完成したプロジェクトに次の追加変更を行います。

1. リンクされたテンプレートの URI 値がパラメーターから取得されるようにメイン テンプレート (azuredeploy.json) を変更します。
2. リンクされたテンプレートをアップロードするときに SAS トークンを生成する代わりに、メイン テンプレートをデプロイするときにこのトークンを生成します。 詳細については、「[デプロイ時に SAS トークンを指定する](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、テンプレートをメイン テンプレートとリンクされたテンプレートにモジュール化しました。 仮想マシン拡張機能を使用してデプロイ後のタスクを実行する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [仮想マシン拡張機能のデプロイ](./resource-manager-tutorial-deploy-vm-extensions.md)
