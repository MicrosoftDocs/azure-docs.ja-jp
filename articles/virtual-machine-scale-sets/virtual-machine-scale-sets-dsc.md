---
title: "Desired State Configuration と仮想マシン スケール セットの使用 | Microsoft Docs"
description: "仮想マシン スケール セットと Azure DSC 拡張機能の使用"
services: virtual-machine-scale-sets
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: c0d9ea4f506371ca21a08f6e1d6128c00123cbac


---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>仮想マシン スケール セットと Azure DSC 拡張機能の使用
[仮想マシンのスケール セット (VMSS)](virtual-machine-scale-sets-overview.md) は、[Azure Desired State Configuration (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 拡張機能ハンドラーと共に使用できます。 VMSS は多数の仮想マシンをデプロイおよび管理する手段であり、そのスケールは負荷に応じて柔軟に拡大および縮小することができます。 DSC は、オンラインで運用ソフトウェアを実行できるように VM を構成するときに使用されます。

## <a name="differences-between-deploying-to-vm-and-vmss"></a>VM へのデプロイと VMSS へのデプロイの違い
VMSS の基本テンプレートの構造は、1 つの VM とは少し異なります。 具体的には、1 つの VM では、"virtualMachines" ノードに拡張機能がデプロイされます。 エントリ タイプ "extensions" があり、ここで DSC がテンプレートに追加されます

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

VMSS ノードには、"VirtualMachineProfile" を備えた "properties" セクション、"extensionProfile" 属性があります。 DSC は "extensions" の下に追加します

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>VMSS の動作
VMSS の動作は、1 つの VM の動作と同じです。 新しく作成された VM は、DSC 拡張機能で自動的にプロビジョニングされます。 拡張機能に新しいバージョンの WMF が必要な場合、VM はオンラインになる前に再起動されます。 オンラインになると、DSC 構成 .zip がダウンロードされ、VM 上でプロビジョニングされます。 詳細については、 [Azure DSC 拡張機能の概要](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
[DSC 拡張機能用の Azure Resource Manager テンプレート](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

[DSC 拡張機能で資格情報を安全に処理する方法](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを確認してください。 

Azure DSC 拡張機能ハンドラーの詳細については、「 [Azure Desired State Configuration 拡張機能ハンドラーの概要](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 




<!--HONumber=Nov16_HO3-->


