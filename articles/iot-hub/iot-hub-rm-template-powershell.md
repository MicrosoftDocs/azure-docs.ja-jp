---
title: "テンプレートを使用した Azure IoT Hub の作成 (PowerShell) | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して PowerShell から IoT ハブを作成する方法。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: dc9f9c39a8eb644229887f76b5c441d4211af059
ms.openlocfilehash: 6f9c36239f8485313066a594eea74bfcd168536e
ms.lasthandoff: 02/24/2017


---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager テンプレートを使用した IoT ハブの作成 (PowerShell)
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに
Azure リソース マネージャーを使って、Azure IoT ハブをプログラムを使用して作成、管理できます。 このチュートリアルでは、Azure Resource Manager テンプレートを使用して PowerShell で IoT Hub を作成する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Azure Resource Manager デプロイメント モデルの使用について説明します。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 <br/>アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure PowerShell 1.0][lnk-powershell-install] 以降。

> [!TIP]
> PowerShell スクリプトと Azure Resource Manager テンプレートを使って Azure のリソースを作成する方法の詳細については、[Azure Resource Manager での Azure PowerShell の使用][lnk-powershell-arm]に関する記事を参照してください。 
> 
> 

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続
PowerShell コマンド プロンプトから、次のコマンドを入力して Azure サブスクリプションにサインインします。

```
Login-AzureRmAccount
```

IoT Hub のデプロイ先と現在サポートされている API バージョンは、次のコマンドで調べることができます。

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

IoT Hub がサポートされているいずれかの場所から次のコマンドを使用し、IoT Hub の追加先となるリソース グループを作成します。 この例では、 **MyIoTRG1**というリソース グループを作成しています。

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Azure Resource Manager テンプレートの送信による IoT Hub の作成
JSON テンプレートを使用して、リソース グループに IoT Hub を作成します。 Azure Resource Manager テンプレートを使用して、既存の IoT Hub に変更を加えることもできます。

1. テキスト エディターを使用して、**template.json** という Azure Resource Manager テンプレートを作成し、標準的な IoT Hub を新規作成するリソース定義 (以下) を記述します。 この例では、IoT Hub を**米国東部**リージョンに追加し、Event Hub と互換性のあるエンドポイントに&2; つのコンシューマー グループ (**cg1** と **cg2**) を作成し、**2016-02-03** API バージョンを使用します。 また、このテンプレートでは、 **hubName**というパラメーターに IoT Hub の名前を指定する必要があります。 IoT Hub をサポートする場所の最新のリストについては、「[Azure の状態][lnk-status]」を参照してください。
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```
2. Azure Resource Manager テンプレート ファイルをローカル マシン上に保存します。 この例では、**c:\templates** フォルダーに保存することを前提としています。
3. 次のコマンドを実行して、新しい IoT Hub をデプロイします。実際の IoT Hub の名前をパラメーターで指定してください。 この例では、IoT Hub の名前は **abcmyiothub** です (この名前はグローバルに一意でなければならないため、自分の名前やイニシャルを含めます)。
   
    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
4. 作成した IoT Hub のキーが出力として表示されます。
5. アプリケーションで新しい IoT ハブが追加されたことを確認するには、[Azure Portal][lnk-azure-portal] にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [!NOTE]
> このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。 IoT ハブを削除するには、[Azure Portal][lnk-azure-portal] を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。
> 
> 

## <a name="next-steps"></a>次のステップ
ここでは、PowerShell と Azure Resource Manager テンプレートを使用して IoT Hub をデプロイしました。次の手順に進んでください。

* [IoT Hub リソース プロバイダー REST API][lnk-rest-api] の機能の詳細をご確認ください。
* Azure Resource Manager の機能の詳細については、「[Azure Resource Manager の概要][lnk-azure-rm-overview]」を参照してください。

IoT Hub の開発に関する詳細については、以下の記事をご覧ください。

* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

