---
title: "テンプレートを使用した Azure Event Hubs 名前空間とコンシューマー グループの作成 | Microsoft Docs"
description: "Event Hubs とコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/07/2017
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 271eca35ad18725306410d1e8b81bdf06712db1f
ms.lasthandoff: 03/08/2017


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する
この記事では、Azure Resource Manager テンプレートを使用し、イベント ハブとコンシューマー グループをそれぞれ&1; つずつ含んだ Event Hubs タイプの名前空間を作成する方法について説明します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」をご覧ください。

完全なテンプレートについては、GitHub の[イベント ハブとコンシューマー グループを作成するためのテンプレート][Event Hub and consumer group template]を参照してください。

> [!NOTE]
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Event Hubs" を検索してください。
> 
> 

## <a name="what-will-you-deploy"></a>デプロイの対象
このテンプレートでデプロイされるのは、イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間です。

[Event Hubs](event-hubs-what-is-event-hubs.md) は、Azure への大規模なイベントとテレメトリ受信をわずかな待機時間と高い信頼性で提供するために使用される、イベント処理サービスです。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義します。

このテンプレートでは、次のパラメーターを定義します。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
作成する Event Hubs 名前空間の名前。

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
Event Hubs 名前空間に作成するイベント ハブの名前。

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
イベント ハブに対して作成するコンシューマー グループの名前。

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
テンプレートの API バージョン。

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>デプロイ対象のリソース
イベント ハブとコンシューマー グループを含んだ、 **EventHubs**タイプの名前空間を作成します。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

