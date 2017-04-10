---
title: "テンプレートを使用した Azure Service Bus 名前空間のトピックとサブスクリプションの作成 | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用したトピックとサブスクリプションを含んだ Service Bus 名前空間の作成"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/18/2017
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 27ad541fc51c497528355cd1091cc48ae8fe1ee8


---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用したトピックとサブスクリプションを含んだ Service Bus 名前空間の作成
この記事では、Azure Resource Manager テンプレートを使用して、トピックとサブスクリプションを含んだ Service Bus の名前空間を作成する方法について説明します。 さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成について詳しくは、「[Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」をご覧ください。

完全なテンプレートについては、[Service Bus の名前空間にトピックとサブスクリプションを追加する][Service Bus namespace with topic and subscription]テンプレートを参照してください。

> [!NOTE]
> 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
> 
> * [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
> * [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
> * [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
> * [トピック、サブスクリプション、ルールを含んだ Service Bus の名前空間を作成する](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Service Bus" を検索してください。
> 
> 

## <a name="what-will-you-deploy"></a>デプロイの対象
このテンプレートでデプロイされるのは、トピックとサブスクリプションを含んだ Service Bus 名前空間です。

[Service Bus のトピックとサブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)は、"*発行とサブスクライブ*" のパターンで一対多の形式の通信を実現します。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
作成する Service Bus 名前空間の名前。

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Service Bus 名前空間に作成するトピックの名前。

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Service Bus 名前空間に作成するサブスクリプションの名前。

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
テンプレートの Service Bus API バージョン。

```json
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>デプロイ対象のリソース
**Messaging**タイプの標準的な Service Bus 名前空間を作成し、トピックとサブスクリプションを追加します。

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>次のステップ
Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

* [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
* [Service Bus リソースを Service Bus Explorer で管理する](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/



<!--HONumber=Jan17_HO4-->


