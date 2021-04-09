---
title: キャプチャを有効化したイベント ハブの作成 - Azure Event Hubs | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、1 つのイベント ハブを含んだ Azure Event Hubs 名前空間を作成して Capture を有効にします
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 17157e05e4ad123ba2bbdffa199c111df9f8912e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653025"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>イベント ハブを含む名前空間を作成し、テンプレートを使用してキャプチャを有効にします

この記事では、Azure Resource Manager テンプレートを使用し、1 つのイベント ハブ インスタンスを含んだ [Event Hubs](./event-hubs-about.md) 名前空間を作成して、イベント ハブの [Capture 機能](event-hubs-capture-overview.md)を有効にする方法について説明します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

この記事では、選択した保存先に応じて Azure Storage Blob または Azure Data Lake Store にキャプチャされるイベントの指定方法についても取り上げます。

テンプレートの作成の詳細については、「 [Azure Resource Manager のテンプレートの作成][Authoring Azure Resource Manager templates]」を参照してください。 テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventHub resource types (Microsoft.EventHub のリソースの種類)](/azure/templates/microsoft.eventhub/allversions)」を参照してください。

Azure リソースの名前付け規則のパターンとプラクティスの詳細については、[Azure リソースの名前付け規則][Azure Resources naming conventions]に関するページを参照してください。

完成したテンプレートについては、次の GitHub リンクをクリックしてください。

- [イベント ハブとストレージへのキャプチャを有効にするテンプレート][Event Hub and enable Capture to Storage template] 
- [イベント ハブと Azure Data Lake Store へのキャプチャを有効にするテンプレート][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> 最新のテンプレートを確認する場合は、「 [Azure クイックスタート テンプレート][Azure Quickstart Templates] 」ギャラリーで "Event Hubs" を検索してください。
> 
> 

## <a name="what-will-you-deploy"></a>デプロイの対象

このテンプレートを使用して、イベント ハブを含んだ Event Hubs 名前空間をデプロイし、[Event Hubs Capture](event-hubs-capture-overview.md) も有効にします。 Event Hubs Capture を使用すると、指定した時間またはサイズの間隔で、Event Hubs のストリーミング データを Azure Blob Storage または Azure Data Lake Store に自動的に配信できます。 Event Hubs Capture を Azure Storage に対して有効にするには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Event Hubs Capture を Azure Data Lake Store に対して有効にするには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

作成する Event Hubs 名前空間の名前。

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Event Hubs 名前空間に作成するイベント ハブの名前。

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

イベント ハブにメッセージを保持する日数。 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

イベント ハブに作成するパーティションの数。

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

イベント ハブの Capture を有効にします。

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

イベント データのシリアル化に使用するエンコード形式。

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

データのキャプチャが Event Hubs Capture で開始される期間。

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
データのキャプチャが Capture で開始されるサイズ間隔。

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Event Hubs Capture が Avro ファイルを書き込むときに使用する名前の形式。 Capture の名前の形式には、`{Namespace}`、`{EventHub}`、`{PartitionId}`、`{Year}`、`{Month}`、`{Day}`、`{Hour}`、`{Minute}`、`{Second}` の各フィールドが含まれている必要があります。 これらは任意の順序で指定でき、区切り記号はあってもなくてもかまいません。
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

テンプレートの API バージョン。

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Azure Storage を保存先として選ぶ場合は、次のパラメーターを使用します。

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

目的のストレージ アカウントへのキャプチャを有効にするには、Azure ストレージ アカウントのリソース ID が必要になります。

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

イベント データをキャプチャする BLOB コンテナー。

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Azure Data Lake Store Gen1 を保存先として選ぶ場合は、次のパラメーターを使用します。 キャプチャしたイベントの保存先となる Data Lake Store パスには、アクセス許可を設定する必要があります。 アクセス許可を設定するには、「[Azure Data Lake Storage Gen 1 へのデータのキャプチャ](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1)」を参照してください。

### <a name="subscriptionid"></a>subscriptionId

Event Hubs 名前空間と Azure Data Lake Store のサブスクリプション ID。 この 2 つのリソースは同じサブスクリプション ID に属している必要があります。

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

キャプチャしたイベントに使用する Azure Data Lake Store の名前。

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

キャプチャしたイベントの保存先となるフォルダーのパス。 これは、キャプチャ操作中にイベントがプッシュされる先の Data Lake Store のフォルダーです。 このフォルダーに対してアクセス許可を設定する方法については、「[Azure Data Lake Store を使用して Event Hubs からデータをキャプチャする](../data-lake-store/data-lake-store-archive-eventhub-capture.md)」を参照してください。

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>キャプチャしたイベントの保存先となる Azure Storage に関してデプロイするリソース

1 つのイベント ハブを含んだ **EventHubs** 型の名前空間を作成し、さらに Azure Blob Storage に対して Capture を有効にします。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>保存先となる Azure Data Lake Store に関してデプロイするリソース

1 つのイベント ハブを含んだ **EventHubs** 型の名前空間を作成し、さらに Azure Data Lake Store に対して Capture を有効にします。

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> **skipEmptyArchives** プロパティを使用して、キャプチャ期間中にイベントが発生しないときの空のファイルの生成を有効または無効にすることができます。 

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Storage に対して Event Hubs Capture を有効にするには、次のようにしてテンプレートをデプロイします。
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Azure Data Lake Store に対して Event Hubs Capture を有効にするには、次のようにしてテンプレートをデプロイします。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

保存先としての Azure Blob Storage:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

保存先としての Azure Data Lake Store:

```azurecli
az deployment group create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>次のステップ

[Azure Portal](https://portal.azure.com) を使用して Event Hubs Capture を構成することもできます。 詳細については、「[Azure Portal を使用して Event Hubs Capture を有効にする](event-hubs-capture-enable-through-portal.md)」を参照してください。

Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](./event-hubs-about.md)
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
