---
title: Azure シリアル コンソールを有効または無効にする |Microsoft Docs
description: Azure シリアル コンソール サービスを有効または無効にする方法
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 1c1fe208c77142351a786fa636896e64a8a467d7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129467"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Azure シリアル コンソールを有効または無効にする

他のリソースと同様に、Azure シリアル コンソールを有効または無効にすることができます。 シリアル コンソールは、グローバル Azure のすべてのサブスクリプションで既定で有効になっています。 現時点では、シリアル コンソールを無効にすると、サブスクリプション全体でこのサービスが無効になります。 サブスクリプションでのシリアル コンソールの無効化または再有効化には、サブスクリプションに対する共同作成者レベル以上のアクセス権が必要です。

また、ブート診断を無効にすることで、個々の VM または仮想マシン スケール セット インスタンスのシリアル コンソールを無効にすることもできます。 VM/仮想マシン スケール セットとブート診断ストレージ アカウントの両方で、共同作成者レベル以上のアクセス権が必要になります。

## <a name="vm-level-disable"></a>VM レベルの無効化
シリアル コンソールは、ブート診断設定を無効にすることによって、特定の VM または仮想マシン スケール セットに対して無効にできます。 VM または仮想マシン スケール セット用のシリアル コンソールを無効にするには、Azure portal からブート診断を無効にします。 仮想マシン スケール セットでシリアル コンソールを使用している場合は、仮想マシン スケール セット インスタンスを最新モデルにアップグレードしたことを確認してください。


## <a name="subscription-level-disable"></a>サブスクリプション レベルの無効化

### <a name="azure-cli"></a>Azure CLI

Azure CLI で次のコマンドを使用することで、サブスクリプション全体に対してシリアル コンソールを無効にして再度有効にできます。

サブスクリプションに対してシリアル コンソールを無効にするには、以下のコマンドを使用します。
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

サブスクリプションに対してシリアル コンソールを有効にするには、以下のコマンドを使用します。
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default"
```

あるサブスクリプションに対するシリアル コンソールの現在の有効/無効状態を取得するには、次のコマンドを使用します。
```azurecli-interactive
subscriptionId=$(az account show -o=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" -o=json | jq .properties
```

### <a name="powershell"></a>PowerShell

PowerShell を使用して、シリアル コンソールを有効または無効にすることもできます。

サブスクリプションに対してシリアル コンソールを無効にするには、以下のコマンドを使用します。
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

サブスクリプションに対してシリアル コンソールを有効にするには、以下のコマンドを使用します。
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>次の手順
* [Linux VM 用 Azure シリアル コンソール](./serial-console-linux.md)についての詳細を参照する
* [Windows VM 用 Azure シリアル コンソール](./serial-console-windows.md)についての詳細を参照する
* [Azure シリアル コンソール内の電源管理オプション](./serial-console-power-options.md)について参照する