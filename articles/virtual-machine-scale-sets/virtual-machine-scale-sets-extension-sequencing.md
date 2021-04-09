---
title: 拡張機能のシーケンス処理と Azure 仮想マシン スケール セットを使用する
description: 仮想マシン スケール セット上で複数の拡張機能をデプロイする際に拡張機能のプロビジョニングをシーケンス処理する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83121033"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>仮想マシン スケール セット内の拡張機能のプロビジョニングをシーケンス処理する
Azure 仮想マシンの拡張機能は、デプロイ後の構成と管理、監視、セキュリティなどの機能を提供します。 通常、運用環境のデプロイでは、目的の結果を実現するために、VM インスタンス用に構成された複数の拡張機能の組み合わせを使用します。

仮想マシン上で複数の拡張機能を使用する場合は、同じ OS リソースを必要とする拡張機能が同時にこれらのリソースを取得しようとしていないことを確認することが重要です。 また、一部の拡張機能は、環境設定やシークレットなどの必須構成を提供するために他の拡張機能に依存します。 適切な順序とシーケンス処理が設定されていないと、依存する拡張機能のデプロイは失敗することがあります。

この記事では、仮想マシン スケール セット内の VM インスタンスに対して構成する拡張機能をシーケンス処理する方法を説明します。

## <a name="prerequisites"></a>前提条件
この記事では、次の内容を熟知していることを前提としています。
-   Azure 仮想マシン[拡張機能](../virtual-machines/extensions/overview.md)
-   仮想マシン スケール セットの[変更](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-extension-sequencing"></a>拡張機能のシーケンス処理を使用するタイミング
拡張機能のシーケンス処理はスケール セットに対して必須ではなく、指定されていない限り、拡張機能は任意の順序でスケール セット インスタンスにプロビジョニングできます。

たとえば、スケール セット モデルで 2 つの拡張機能 (ExtensionA と ExtensionB) がモデルに指定されている場合、次のプロビジョニング シーケンスのいずれかが発生する可能性があります。
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

必ず ExtensionB より前に ExtensionA をプロビジョニングすることがアプリケーションに必要な場合は、この記事の説明に従って拡張機能のシーケンス処理を使用する必要があります。 拡張機能のシーケンス処理では、次の 1 つのシーケンスのみが発生します:
-   ExtensionA - > ExtensionB

定義されたプロビジョニング シーケンスで指定されていない拡張機能は、定義されたシーケンスの前、後、途中など、いつでもプロビジョニングできます。 拡張機能のシーケンス処理では、特定の拡張機能が別の特定の拡張機能より後にプロビジョニングされることだけを指定します。 モデルで定義されている他の拡張機能のプロビジョニングには影響しません。

たとえば、スケール セット モデルで 3 つの拡張機能 (ExtensionA、ExtensionB、ExtensionC) がモデルに指定され、ExtensionC が ExtensionA より後にプロビジョニングされるように設定されている場合、次のいずれかのプロビジョニング シーケンスが発生する可能性があります:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

定義された拡張機能シーケンスの実行中に他の拡張機能がプロビジョニングされないようにする必要がある場合は、スケール セット モデル内のすべての拡張機能をシーケンス処理することをお勧めします。 上記の例では、1 つのシーケンス処理だけが行われるように、ExtensionB が ExtensionC より後にプロビジョニングされるように設定できます。
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>拡張機能のシーケンス処理を使用する方法
拡張機能のプロビジョニングをシーケンス処理するには、拡張機能名の配列を受け取るプロパティ "provisionAfterExtensions" を含むようにスケール セット モデル内の拡張機能定義を更新する必要があります。 スケール セット モデルでは、プロパティ配列値で示されている拡張機能を完全に定義する必要があります。

### <a name="template-deployment"></a>テンプレートのデプロイ
次の例では、拡張機能が順番にプロビジョニングされるように、スケール セットに 3 つの拡張機能 (ExtensionA、ExtensionB、ExtensionC) があるテンプレートを定義します。
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

プロパティ "provisionAfterExtensions" は拡張機能名の配列を受け取るので、上記の例は、ExtensionC が ExtensionA と ExtensionB より後にプロビジョニングされるが ExtensionA と ExtensionB 間に順序指定は不要となるように変更できます。 次のテンプレートを使用して、このシナリオを実現できます。

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
次の例では、スケール セット モデルに ExtensionC という名前の新しい拡張機能を追加します。 ExtensionC は、スケール セット モデルで既に定義されている ExtensionA と ExtensionB に依存します。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

ExtensionC がスケール セット モデルで以前に定義されていて、その依存関係を追加する必要がある場合は、`PATCH` を実行して既にデプロイされている拡張機能のプロパティを編集できます。

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
既存のスケール セット インスタンスに対する変更は、次の[アップグレード](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)時に適用されます。

### <a name="azure-powershell"></a>Azure PowerShell
[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) コマンドレットを使用して、アプリケーションの正常性拡張機能をスケール セット モデルの定義に追加します。 拡張機能のシーケンス処理では、Az PowerShell 1.2.0 以上を使用する必要があります。

次の例は、Windows ベースのスケール セットのスケール セット モデルで `extensionProfile` に[アプリケーションの正常性拡張機能](virtual-machine-scale-sets-health-extension.md)を追加しています。 アプリケーションの正常性拡張機能は、スケール セット内に既に定義されている[カスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)のプロビジョニング後にプロビジョニングされます。

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az vmss 拡張機能セット](/cli/azure/vmss/extension#az-vmss-extension-set) を使用して、アプリケーションの正常性拡張機能をスケール セット モデルの定義に追加します。 拡張機能のシーケンス処理では、Azure CLI 2.0.55 以上を使用する必要があります。

次の例は、Windows ベースのスケール セットのスケール セット モデルに[アプリケーションの正常性拡張機能](virtual-machine-scale-sets-health-extension.md)を追加しています。 アプリケーションの正常性拡張機能は、スケール セット内に既に定義されている[カスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-windows.md)のプロビジョニング後にプロビジョニングされます。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>トラブルシューティング

### <a name="not-able-to-add-extension-with-dependencies"></a>依存関係を持つ拡張機能を追加できない
1. provisionAfterExtensions で指定された拡張機能がスケール セット モデル内に定義されていることを確認します。
2. 循環依存関係が導入されていないことを確認します。 たとえば、次のシーケンスは許可されません: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. 依存関係を設定する拡張機能の拡張機能 "properties" に "settings" プロパティがあることを確認します。 たとえば、ExtentionB を ExtensionA より後にプロビジョニングする必要がある場合は、ExtensionA の ExtensionA "properties" に "settings" フィールドが必要です。 拡張機能に必須設定が必要でない場合は、空の "settings" プロパティを指定できます。

### <a name="not-able-to-remove-extensions"></a>拡張機能を削除できない
削除する拡張機能が他の拡張機能の provisionAfterExtensions に表示されてないことを確認します。

## <a name="next-steps"></a>次のステップ
仮想マシン スケール セットに[ご自身のアプリケーションをデプロイする](virtual-machine-scale-sets-deploy-app.md)方法を学習します。
