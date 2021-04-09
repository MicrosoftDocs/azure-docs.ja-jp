---
title: Azure Disk Encryption と Azure 仮想マシン スケール セットの拡張機能のシーケンス処理
description: この記事では、Linux IaaS VM で Microsoft Azure Disk Encryption を有効にする方法を説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90988027"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Azure Disk Encryption を仮想マシン スケール セットの拡張機能のシーケンス処理と共に使用する

Azure ディスク暗号化などの拡張機能は、指定された順序で、Azure 仮想マシン スケール セットに追加できます。 これを行うには、[拡張機能のシーケンス処理](virtual-machine-scale-sets-extension-sequencing.md)を使用します。 

一般に、暗号化は次の場合にディスクに適用されます。

- ディスクまたはボリュームを準備する拡張機能またはカスタム スクリプトの後。
- 暗号化されたディスクまたはボリューム上のデータにアクセスまたは使用する拡張機能またはカスタム スクリプトの前。

どちらの場合でも、`provisionAfterExtensions` プロパティは、シーケンスの後ろで追加する必要のある拡張機能を指定します。

## <a name="sample-azure-templates"></a>サンプル Azure テンプレート

別の拡張子の後に Azure Disk Encryption を適用する場合は、`provisionAfterExtensions` プロパティを AzureDiskEncryption 拡張機能ブロック内に置きます。 

次に、Windows ディスクを初期化および書式設定する Powershell スクリプトである「CustomScriptExtension」を使用し、「AzureDiskEncryption」をその後に続けた例を示します。

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

別の拡張子の前に Azure Disk Encryption を適用する場合は、`provisionAfterExtensions` プロパティを続ける拡張機能のブロック内に置きます。

次に、「AzureDiskEncryption」を使用し、その後に Windows ベースの Azure VM で監視および診断機能を提供する拡張機能である「VMDiagnosticsSettings」を続けた例を示します。


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

その他の詳細なテンプレートについては、次を参照してください。
* ディスク (Linux) をフォーマットするカスタム シェル スクリプトの後に Azure Disk Encryption 拡張機能を適用する場合: [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>次のステップ
- 拡張機能のシーケンス処理に関する詳細情報:[仮想マシン スケール セット内の拡張機能のプロビジョニングをシーケンス処理する](virtual-machine-scale-sets-extension-sequencing.md)。
- `provisionAfterExtensions` プロパティに関する詳細情報:[Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)。
- [仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI を使用して仮想マシン スケール セットを暗号化する](disk-encryption-cli.md)
- [Azure PowerShell を使用して仮想マシン スケール セットを暗号化する](disk-encryption-powershell.md)
- [Azure Disk Encryption のキー コンテナーを作成して構成する](disk-encryption-key-vault.md)
