---
title: "PowerShell コマンドレットを使用して Azure IoT Hub を作成する | Microsoft Docs"
description: "PowerShell コマンドレットを使用して IoT Hub を作成する方法。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 02227adeb8a9a7463506efa44ddc2977f8aae65a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>New-AzureRmIotHub コマンドレットを使用して IoT Hub を作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに

Azure PowerShell コマンドレットを使用すると、Azure IoT Hub を作成して管理できます。 このチュートリアルでは、PowerShell を使用して IoT Hub を作成する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、Azure Resource Manager デプロイメント モデルの使用について説明します。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 <br/>アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure PowerShell コマンドレット][lnk-powershell-install]。

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続
PowerShell コマンド プロンプトから、次のコマンドを入力して Azure サブスクリプションにサインインします。

```powershell
Login-AzureRmAccount
```

複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 次のコマンドで、使用できる Azure サブスクリプションの一覧を表示します。

```powershell
Get-AzureRMSubscription
```

以下のコマンドを使用して、コマンドを実行して IoT ハブを作成するために使用するサブスクリプションを選択します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Create resource group

IoT Hub をデプロイするためのリソース グループが必要です。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

次のコマンドを使用すると、IoT Hub をデプロイできる場所を確認できます。

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

IoT Hub 用のリソース グループを、IoT Hub がサポートされているいずれかの場所に作成するには、次のコマンドを使用します。 この例では、**米国東部**リージョンに **MyIoTRG1** というリソース グループを作成しています。

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前の手順で作成したリソース グループに IoT Hub を作成するには、次のコマンドを使用します。 この例では、**米国東部**リージョンに **MyTestIoTHub** という **S1** ハブを作成しています。

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT Hub の名前は一意である必要があります。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


サブスクリプションの IoT Hub の一覧を表示するには、次のコマンドを使用します。

```powershell
Get-AzureRmIotHub
```

前の例では、課金の対象とする S1 Standard IoT Hub を追加しています。 IoT Hub を削除するには、次のコマンドを使用します。

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

また、次のコマンドを使用して、リソース グループとそのグループに含まれるすべてのリソースを削除することもできます。

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>次のステップ

ここでは、PowerShell コマンドレットを使用して IoT Hub をデプロイしました。次の手順に進んでください。

* 他の [PowerShell コマンドレットで IoT Hub を操作][lnk-iothub-cmdlets]する方法をご確認ください。
* [IoT Hub リソース プロバイダー REST API][lnk-rest-api] の機能の詳細をご確認ください。

IoT Hub の開発に関する詳細については、以下の記事をご覧ください。

* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Edge を使用したデバイスのシミュレーション][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

