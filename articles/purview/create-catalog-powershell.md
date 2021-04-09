---
title: 'クイックスタート: Azure PowerShell または Azure CLI を使用して Azure Purview アカウントを作成する (プレビュー)'
description: このクイックスタートでは、Azure PowerShell または Azure CLI を使用して、Azure Purview アカウントを作成する方法について説明します。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880001"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>クイックスタート: Azure PowerShell または Azure CLI を使用して Azure Purview アカウントを作成する

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このクイックスタートでは、Azure PowerShell または Azure CLI を使用して、Azure Purview アカウントを作成します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* Azure へのサインインに使用するユーザー アカウントは、共同作成者ロールまたは所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* テンプレートをデプロイするクライアント マシンに Azure PowerShell または Azure CLI をインストールします ([コマンド ライン デプロイ](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment))。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="configure-your-subscription"></a>サブスクリプションの構成

ご利用のサブスクリプションで Azure Purview を実行できるよう、必要に応じて、こちらの手順に従ってサブスクリプションを構成します。

   1. Azure portal で、**サブスクリプション** を検索して選択します。

   1. サブスクリプションの一覧から、使用するサブスクリプションを選択します。 サブスクリプションの管理アクセス許可が必要です。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Azure portal でのサブスクリプションの選択方法を示すスクリーンショット。":::

   1. サブスクリプションについては、**[リソース プロバイダー]** を選択します。 **[リソース プロバイダー]** ペインで、3 つのリソース プロバイダーをすべて検索して登録します。 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      登録されていない場合は、 **[登録]** を選択して登録します。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Azure portal で Microsoft dot Azure Purview リソース プロバイダーを登録する方法のスクリーンショット。":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview アカウント インスタンスを作成する

1. Azure の資格情報でサインインします

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. 複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Purview アカウント用のリソース グループを作成します。 既にお持ちの場合は、このステップをスキップしてかまいません。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Purview テンプレート ファイルを作成します (`purviewtemplate.json` など)。 `name`、`location`、`capacity` (`4` または `16`) を更新できます。

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Purview テンプレートをデプロイする

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. デプロイ コマンドから結果が返されます。 デプロイが成功したかどうかは、`ProvisioningState` を見て確認します。
    
## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Purview アカウントを作成する方法について学習しました。

次の記事に進んで、Azure Purview アカウントへのアクセスをユーザーに許可する方法を学習してください。 

> [!div class="nextstepaction"]
> [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)