---
title: PowerShell コマンドレットを使用して Azure IoT Hub を作成する | Microsoft Docs
description: PowerShell コマンドレットを使用してリソース グループを作成し、そのリソース グループに IoT ハブを作成する方法を説明します。 また、ハブを削除する方法も説明します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: da021e3ba0fd93a182ea76a1ba4b7042b325aacc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142375"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>New-AzIotHub コマンドレットを使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに

Azure PowerShell コマンドレットを使用すると、Azure IoT Hub を作成して管理できます。 このチュートリアルでは、PowerShell を使用して IoT Hub を作成する方法を説明します。

このハウツー記事を完了するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続

Cloud Shell を使用している場合は、既にサブスクリプションにログインしています。 そうではなく、PowerShell をローカルに実行している場合は、次のコマンドを入力して Azure サブスクリプションにサインインします。

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

IoT Hub をデプロイするためのリソース グループが必要です。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

お使いの IoT ハブ用のリソース グループを作成するには、[New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) コマンドを使用します。 この例では、**米国東部** リージョンに **MyIoTRG1** というリソース グループを作成しています。

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前の手順で作成したリソース グループに IoT ハブを作成するには、[New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub) コマンドを使用します。 この例では、**米国東部** リージョンに **MyTestIoTHub** という **S1** ハブを作成しています。

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT ハブの名前は、グローバルに一意である必要があります。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) コマンドを使用して、お使いのサブスクリプションに含まれるすべての IoT ハブを一覧表示できます。

```azurepowershell-interactive
Get-AzIotHub
```

この例では、前のステップで作成した S1 Standard IoT Hub を示します。

IoT ハブを削除するには、[Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) コマンドを使用します。

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

また、[Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) コマンドを使用して、リソース グループとそのグループに含まれるすべてのリソースを削除することもできます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>次のステップ

ここでは、PowerShell コマンドレットを使用して IoT ハブをデプロイしました。さらに詳しく知りたい場合は、以下の記事をご覧ください。

* [IoT ハブを操作するための PowerShell コマンドレット](/powershell/module/az.iothub/)。

* [IoT Hub リソースプロバイダー REST API](/rest/api/iothub/iothubresource)。

IoT Hub の開発に関する詳細については、以下の記事をご覧ください。

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)