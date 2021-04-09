---
title: テンプレートを使用した Azure IoT Hub の作成 (PowerShell) | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して Azure PowerShell から IoT ハブを作成する方法です。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 1fedadfa7e5b4ec3d7de30d0ad3ef1b1bfa0e0ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144402"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager テンプレートを使用した IoT ハブの作成 (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager テンプレートを使って IoT ハブとコンシューマー グループを作成する方法を学習します。 Resource Manager テンプレートとは、ご利用のソリューションに対して配置が必要なリソースを定義した JSON ファイルのことをいいます。 Resource Manager の作成の詳細については、[Azure Resource Manager のドキュメント](../azure-resource-manager/index.yml)に関する記事をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

このクイック スタートで使われる Resource Manager テンプレートは、[Azure クイック スタートのテンプレート](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)に関する記事からのものです。 テンプレートのコピーを次に示します。

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

テンプレートでは、3 つのエンドポイント (eventhub、cloud-to-device、messaging) を持つ Azure Iot ハブとコンシューマー グループが作成されます。 テンプレートの他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)」をご覧ください。 Iot Hub テンプレートのスキーマについては、[こちら](/azure/templates/microsoft.devices/iothub-allversions)をご覧ください。

テンプレートをデプロイするには複数の方法があります。  このチュートリアルでは、Azure PowerShell を使います。

PowerShell スクリプトを実行するには、**[使ってみる]** を選択して、Azure Cloud シェルを開きます。 スクリプトを貼り付けるには、シェルを右クリックし、[貼り付け] を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

PowerShell スクリプトを見るとわかるように、使うテンプレートは Azure クイック スタート テンプレートからのものです。 独自のものを使うには、最初に、テンプレート ファイルを Cloud Shell にアップロードしてから、`-TemplateFile` スイッチを使ってファイル名を指定する必要があります。  例については、「[テンプレートのデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)」をご覧ください。

## <a name="next-steps"></a>次のステップ

ここでは、Azure Resource Manager テンプレートを使って IoT ハブをデプロイしました。次の手順に進んでください。

* [IoT Hub リソース プロバイダー REST API][lnk-rest-api] の機能の詳細をご確認ください。
* Azure Resource Manager の機能の詳細については、「[Azure Resource Manager の概要][lnk-azure-rm-overview]」を参照してください。
* テンプレートで使用する JSON 構文とプロパティについては、「[Microsoft.Devices resource types (Microsoft.Devices のリソースの種類)](/azure/templates/microsoft.devices/iothub-allversions)」を参照してください。

IoT Hub の開発に関する詳細については、以下の記事をご覧ください。

* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md