---
title: チュートリアル - Azure Resource Manager テンプレートを使用して Azure IoT Hub のメッセージ ルーティングを構成する
description: チュートリアル - Azure Resource Manager テンプレートを使用して Azure IoT Hub のメッセージ ルーティングを構成する
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621430"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>チュートリアル:Azure Resource Manager テンプレートを使用して IoT Hub のメッセージ ルーティングを構成する

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>メッセージ ルーティング

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>テンプレートとパラメーター ファイルのダウンロード

このチュートリアルのパート 2 では、IoT ハブにメッセージを送信する Visual Studio アプリケーションをダウンロードして実行します。 このダウンロード内のフォルダーには、Azure Resource Manager テンプレートとパラメーター ファイルのほか、Azure CLI と PowerShell のスクリプトが含まれています。

早速、[Azure IoT C# サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)をダウンロードしましょう。 master.zip ファイルを解凍します。 Resource Manager テンプレートとパラメーター ファイルは、**template_iothub.json** および **template_iothub_parameters.json** という名前で、/iot-hub/Tutorials/Routing/SimulatedDevice/resources/ にあります。

## <a name="create-your-resources"></a>リソースの作成

リソースはすべて、Azure Resource Manager (RM) テンプレートを使用して作成します。 Azure CLI と PowerShell のスクリプトは一度に数行実行することができます。 RM テンプレートは 1 回の手順でデプロイされます。 この記事では、それぞれを理解しやすいように各セクションを個別に説明します。 さらに、テンプレートをデプロイしてテスト用の仮想デバイスを作成する方法を説明します。 テンプレートのデプロイ後、ポータルでメッセージ ルーティング構成を確認できます。

IoT ハブ名やストレージ アカウント名など、いくつかのリソース名はグローバルに一意であることが必要です。 リソースの命名を容易にするために、それらのリソース名には、現在の日付および時刻から生成される英数字のランダム値が追加されるよう設定されています。 

テンプレートを見ると、それらのリソースに対し変数が設定されている場所がわかります。リソースは渡されたパラメーターを受け取り、そのパラメーターに *randomValue* を連結します。 

次のセクションでは、使用されるパラメーターについて説明します。

### <a name="parameters"></a>パラメーター

これらのパラメーターのほとんどには既定値があります。 末尾が **_in** のものは、*randomValue* と連結されてグローバルに一意になります。 

**randomValue**: この値は、テンプレートをデプロイした時点の最新の日付および時刻から生成されます。 このフィールドは、テンプレート自体で生成されるため、パラメーター ファイルには存在しません。

**subscriptionId**: このフィールドは、テンプレートのデプロイ先となるサブスクリプションに自動的に設定されます。 このフィールドは自動的に設定されるため、パラメーター ファイルには存在しません。

**IoTHubName_in**: このフィールドは、IoT ハブのベース名です。これが randomValue と連結されてグローバルに一意になります。

**location**:このフィールドは、デプロイ先となる Azure リージョンです ("westus" など)。

**consumer_group**: このフィールドは、ルーティング エンドポイントを通じて受信されるメッセージに対して設定されるコンシューマー グループです。 Azure Stream Analytics で結果をフィルター処理するために使用されます。 たとえば、ストリーム全体からすべてのデータを受け取る場合や、**Contoso** に設定された consumer_group を通じてデータを受信する場合、Azure Stream Analytics ストリーム (と Power BI レポート) を設定して、それらのエントリだけを表示できます。 このフィールドは、このチュートリアルのパート 2 で使用されます。

**sku_name**: このフィールドは、IoT ハブのスケーリングです。 この値は S1 以上にする必要があります。Free レベルは、複数のエンドポイントが許容されていないため、このチュートリアルには使用できません。

**sku_units**: このフィールドは、使用できる IoT Hub ユニットの数であり、**sku_name** と対で使用します。

**d2c_partitions**: このフィールドは、イベント ストリームに使用されるパーティションの数です。

**storageAccountName_in**: このフィールドは、作成されるストレージ アカウントの名前です。 メッセージは、ストレージ アカウント内のコンテナーにルーティングされます。 このフィールドが randomValue と連結されて、グローバルに一意になります。

**storageContainerName**: このフィールドは、ストレージ アカウントにルーティングされたメッセージの格納先となるコンテナーの名前です。

**storage_endpoint**: このフィールドは、メッセージ ルーティングによって使用されるストレージ アカウント エンドポイントの名前です。

**service_bus_namespace_in**: このフィールドは、作成される Service Bus 名前空間の名前です。 この値が randomValue と連結されて、グローバルに一意になります。

**service_bus_queue_in**: このフィールドは、メッセージのルーティングに使用される Service Bus キューの名前です。 この値が randomValue と連結されて、グローバルに一意になります。

**AuthRules_sb_queue**: このフィールドは、Service Bus キューの承認規則です。キューの接続文字列を取得するために使用されます。

### <a name="variables"></a>変数

これらの値はテンプレート内で使用され、大半はパラメーターから得られます。

**queueAuthorizationRuleResourceId**: このフィールドは、Service Bus キュー用の承認規則の ResourceId です。 そして ResourceId は、キューの接続文字列を取得するために使用されます。

**iotHubName**: このフィールドは、randomValue が連結された後の IoT ハブの名前です。 

**storageAccountName**:このフィールドは、randomValue が連結された後のストレージ アカウントの名前です。 

**service_bus_namespace**: このフィールドは、randomValue が連結された後の名前空間です。

**service_bus_queue**: このフィールドは、randomValue が連結された後の Service Bus キュー名です。

**sbVersion**: 使用する Service Bus API のバージョンです。 このケースでは "2017-04-01" です。

### <a name="resources-storage-account-and-container"></a>リソース: ストレージ アカウントとコンテナー

最初に作成されるリソースは、ストレージ アカウントと、メッセージのルーティング先となるコンテナーです。 コンテナーは、ストレージ アカウントに属するリソースです。 ストレージ アカウントに対する `dependsOn` 句が存在するため、コンテナーの前にストレージ アカウントを作成しておく必要があります。

このセクションは、次のような内容になっています。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>リソース: Service Bus の名前空間とキュー

2 番目に作成されるリソースは、Service Bus 名前空間と、メッセージのルーティング先となる Service Bus キューです。 SKU は Standard に設定されます。 API バージョンは変数から取得されます。 また、このセクションのデプロイ時に Service Bus 名前空間をアクティブ化するように設定されます (status:Active)。 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

このセクションでは、Service Bus キューが作成されます。 スクリプトのこの部分には、キューの前に名前空間が作成されるようにする `dependsOn` 句があります。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>リソース: IoT Hub とメッセージ ルーティング

ストレージ アカウントと Service Bus キューが作成されたところで、それらにメッセージをルーティングする IoT ハブを作成します。 RM テンプレートで `dependsOn` 句が使用されているため、Service Bus リソースとストレージ アカウントが作成される前にハブの作成が試みられることはありません。 

以下に示すのは、IoT ハブ セクションの最初の部分です。 テンプレートのこの部分に依存関係の設定があり、プロパティから始まります。

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

次は、IoT ハブのメッセージ ルーティング構成のセクションです。 最初に、エンドポイントのセクションがあります。 テンプレートのこの部分で、Service Bus キューとストレージ アカウントのルーティング エンドポイントが設定されます (接続文字列もここに含まれます)。

キューの接続文字列を作成するためには、queueAuthorizationRulesResourcedId が必要です。これはインラインで取得されます。 ストレージ アカウントの接続文字列を作成するには、プライマリ ストレージ キーを取得し、それを接続文字列の書式で使用します。

また、BLOB の形式も、エンドポイントの構成で `AVRO` または `JSON` に設定します。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

次のセクションは、エンドポイントへのメッセージ ルートに関するものです。 設定はエンドポイントごとに 1 つあるので、Service Bus キュー用に 1 つ、ストレージ アカウント コンテナー用に 1 つ存在します。

ストレージにルーティングされるメッセージのクエリ条件は `level="storage"` で、Service Bus キューにルーティングされるメッセージのクエリ条件は `level="critical"` であることを思い出してください。

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

次の JSON は、IoT ハブ セクションの残りの部分です。ここにはハブの既定の情報と SKU が含まれています。

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>リソース: Service Bus キューの承認規則

Service Bus キューの承認規則は、Service Bus キューの接続文字列を取得するために使用されます。 Service Bus 名前空間と Service Bus キューよりも前に作成されることがないよう、`dependsOn` 句が使用されています。

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>リソース: コンシューマー グループ

このセクションでは、このチュートリアルのパート 2 で Azure Stream Analytics によって使用される IoT Hub データのコンシューマー グループを作成します。

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>リソース: 出力

デプロイ スクリプトに値を返して表示したい場合は、出力セクションを使用します。 テンプレートのこの部分は、Service Bus キューの接続文字列を返します。 必ずしも値を返す必要はありません。これは呼び出し元のスクリプトに結果を返す方法の例として含まれています。

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>RM テンプレートのデプロイ

テンプレートを Azure にデプロイするには、テンプレートとパラメーター ファイルを Azure Cloud Shell にアップロードしたうえで、テンプレートをデプロイするためのスクリプトを実行します。 Azure Cloud Shell を開いてサインインします。 この例では、PowerShell を使用します。

ファイルをアップロードするには、メニュー バーの **[ファイルのアップロード/ダウンロード]** アイコンを選択して、[アップロード] を選択します。

![[ファイルのアップロード/ダウンロード] アイコンが強調表示されているスクリーンショット。](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

表示されたエクスプローラーを使用して、自分のローカル ディスク上のファイルを探して選択し、 **[開く]** を選択します。

ファイルのアップロード後、次の画像のような結果のダイアログが表示されます。

![結果のアップロードとダウンロードが強調表示された Cloud Shell のメニュー バー](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Cloud Shell インスタンスによって使用される共有にファイルがアップロードされます。 

デプロイを行うスクリプトを実行します。 このスクリプトの最後の行では、返されるように設定された変数 (Service Bus キューの接続文字列) を取得します。

このスクリプトは、次の変数を設定して使用します。

**$RGName** は、テンプレートのデプロイ先となるリソース グループの名前です。 このフィールドは、テンプレートをデプロイする前に作成されます。

**$location** は、テンプレートに使用される Azure の場所です ("westus" など)。

**deploymentname** は、デプロイに割り当てる名前です。返される変数の値を取得する際に使用します。

PowerShell スクリプトを次に示します。 この PowerShell スクリプトをコピーして Cloud Shell ウィンドウに貼り付け、Enter キーを押して実行してください。

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

スクリプト エラーが発生した場合は、スクリプトをローカルで編集し、それをもう一度 Cloud Shell にアップロードして、スクリプトを再実行できます。 スクリプトが正常に実行されたら、次の手順に進みます。

## <a name="create-simulated-device"></a>シミュレートされたデバイスの作成

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>ポータルでのメッセージ ルーティングの表示

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>次のステップ

すべてのリソースを設定してメッセージ ルートを構成したので、次のチュートリアルに進み、ルーティングされたメッセージに関する情報を処理して表示する方法を確認しましょう。

> [!div class="nextstepaction"]
> [パート 2 - メッセージ ルーティング結果の表示](tutorial-routing-view-message-routing-results.md)
