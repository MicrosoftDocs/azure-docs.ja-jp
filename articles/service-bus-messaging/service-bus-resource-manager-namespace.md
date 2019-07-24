---
title: Azure Resource Manager テンプレートを使用した Service Bus メッセージング名前空間の作成 | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して Service Bus メッセージング名前空間を作成します。
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 4471c9d5b6c09bcf4d9100cccfa725f36cf9a3f8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045084"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用した Service Bus 名前空間の作成
このクイック スタートでは、**Standard** の SKU で **Messaging** タイプの Service Bus 名前空間を作成する Azure Resource Manager テンプレートを作成します。 また、デプロイの実行用に指定するパラメーターについても取り上げます。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」をご覧ください。 完全なテンプレートについては、GitHub の [Service Bus 名前空間テンプレート][Service Bus namespace template]に関するページを参照してください。

> [!NOTE]
> 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。 
> 
> * [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
> * [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
> * [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
> * [トピック、サブスクリプション、ルールを含んだ Service Bus の名前空間を作成する](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Service Bus" を検索してください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="quick-deployment"></a>高速なデプロイ
JSON を記述したり PowerShell/CLI コマンドを実行したりすることなくサンプルを実行するには、次のボタンを選択します。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

テンプレートを手動で作成してデプロイするには、この記事の後続のセクションに従ってください。

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

**Azure PowerShell** を使用して Resource Manager テンプレートをデプロイするには、[Azure PowerShell をインストール](https://docs.microsoft.com/powershell/azure/install-Az-ps)します。

**Azure CLI** を使用して Resource Manager テンプレートをデプロイするには、[Azure CLI をインストール]( /cli/azure/install-azure-cli)します。

## <a name="create-the-resource-manager-template-json"></a>Resource Manager テンプレート JSON を作成する 
次の内容を記述した **MyServiceBusNamespace.json** という名前の JSON ファイルを作成します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
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
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

このテンプレートは、標準の Service Bus 名前空間を作成します。 JSON の構文とプロパティについては、[namespaces](/azure/templates/microsoft.servicebus/namespaces) テンプレート リファレンスを参照してください。

## <a name="create-the-parameters-json"></a>パラメーター JSON を作成する
前の手順で作成したテンプレートには、`Parameters` というセクションがあります。 パラメーターは、デプロイするプロジェクトやターゲット環境に応じて異なる値に対して定義します。 このテンプレートでは、**serviceBusNamespaceName**、**serviceBusSku**、および **location** のパラメーターを定義します。 Service Bus の SKU の詳細については、作成する [Service Bus の SKU](https://azure.microsoft.com/pricing/details/service-bus/) に関するページを参照してください。

次の内容を記述した **MyServiceBusNamespace-Parameters.json** という名前の JSON ファイルを作成します。 

> [!NOTE] 
> Service Bus 名前空間の名前を指定します。 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Azure PowerShell を使用してテンプレートをデプロイする

### <a name="sign-in-to-azure"></a>Azure へのサインイン
1. Azure PowerShell を起動します。

2. 次のコマンドを実行して、Azure にサインインします。

   ```azurepowershell
   Login-AzAccount
   ```
3. 次のコマンドを発行して、現在のサブスクリプション コンテキストを設定します。

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>リソースのデプロイ
Azure PowerShell を使用してリソースをデプロイするには、JSON ファイルを作成したフォルダーに移動し、次のコマンドを実行します。

> [!IMPORTANT]
> これらのコマンドを実行する前に、$resourceGroupName の値として Azure リソース グループの名前を指定します。 

1. リソース グループ名の変数を宣言し、その値を指定します。 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Azure リソース グループを作成します。

    ```azurepowershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```
3. Resource Manager テンプレートをデプロイします。 デプロイ自体、リソース グループ、テンプレート用の JSON ファイル、パラメーター用の JSON ファイルの名前を指定します

    ```azurepowershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Azure CLI を使用してテンプレートをデプロイする

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurecli
    az login
    ```
2. 現在のサブスクリプションのコンテキストを設定します。 `MyAzureSub` は、使用する Azure サブスクリプションの名前に置き換えてください。

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>リソースのデプロイ
Azure CLI を使用してリソースをデプロイするには、JSON ファイルがあるフォルダーに移動し、次のコマンドを実行します。

> [!IMPORTANT]
> az group create コマンドで Azure リソース グループの名前を指定します 。

1. Azure リソース グループを作成します。 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Resource Manager テンプレートをデプロイします。 リソース グループ、デプロイ、テンプレート用の JSON ファイル、パラメーター用の JSON ファイルの名前を指定します。

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>次の手順
この記事では、Service Bus 名前空間を作成しました。 キュー、トピック/サブスクリプションを作成して使用する方法については、他のクイック スタートを参照してください。 

- [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus トピックの概要](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
