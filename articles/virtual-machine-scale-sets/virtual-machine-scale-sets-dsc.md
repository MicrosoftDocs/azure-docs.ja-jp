---
title: Desired State Configuration と仮想マシン スケール セットの使用
description: 仮想マシン スケール セットと Azure Desired State Configuration Extension を使用し、仮想マシンを構成します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87080490"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>仮想マシン スケール セットと Azure DSC 拡張機能の使用
[仮想マシン スケール セット](./overview.md) は、[Azure Desired State Configuration (DSC)](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) 拡張機能ハンドラーとともに使用できます。 仮想マシン スケール セットは、多数の仮想マシンをデプロイおよび管理する手段であり、負荷に応じて柔軟にスケールインおよびスケールアウトができます。 DSC は、オンラインで運用ソフトウェアを実行できるように VM を構成するときに使用されます。

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>仮想マシンへのデプロイと仮想マシン スケール セットへのデプロイの違い
仮想マシン スケール セットの基本テンプレートの構造は、1 つの VM の場合とは少し異なります。 具体的には、1 つの VM では、"virtualMachines" ノードに拡張機能がデプロイされます。 エントリ タイプ "extensions" があり、ここで DSC がテンプレートに追加されます

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

仮想マシン スケール セット ノードには、"VirtualMachineProfile" を備えた "properties" セクション、"extensionProfile" 属性があります。 DSC は "extensions" の下に追加します

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの動作
仮想マシン スケール セットの動作は、1 つの VM の動作と同じです。 新しく作成された VM は、DSC 拡張機能で自動的にプロビジョニングされます。 拡張機能に新しいバージョンの WMF が必要な場合、VM はオンラインになる前に再起動されます。 オンラインになると、DSC 構成 .zip がダウンロードされ、VM 上でプロビジョニングされます。 詳細については、 [Azure DSC 拡張機能の概要](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
[DSC 拡張機能用の Azure Resource Manager テンプレート](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json)に関するページをご覧ください。

[DSC 拡張機能で資格情報を安全に処理する方法](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json)に関するページを確認してください。 

Azure DSC 拡張機能ハンドラーの詳細については、「 [Azure Desired State Configuration 拡張機能ハンドラーの概要](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)」を参照してください。 

PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](/powershell/scripting/dsc/overview/overview)を参照してください。 
