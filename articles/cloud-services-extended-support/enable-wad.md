---
title: Cloud Services (延長サポート) で Windows Azure 診断拡張機能を適用する
description: Cloud Services (延長サポート) に Windows Azure 診断拡張機能を適用する
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ad2a27d1e41ba8e589aa98542c4a0cb3d92afbea
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430867"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Cloud Services (延長サポート) で Windows Azure 診断拡張機能を適用する 
どのクラウド サービスでも主要なパフォーマンス メトリックを監視することができます。 各クラウド サービス ロールは最小限のデータ (CPU 使用率、ネットワーク使用状況、ディスク使用率) を収集します。 クラウド サービスで Microsoft.Azure.Diagnostics 拡張機能がロールに適用されている場合、そのロールは追加のデータ ポイントを収集できます。 詳細については、[拡張機能の概要](extensions.md)に関する記事を参照してください

Windows Azure Diagnostics 拡張機能は、[PowerShell](deploy-powershell.md) または [ARM テンプレート](deploy-template.md)を使用して Cloud Services (延長サポート) に対して有効にできます

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>PowerShell を使用して Windows Azure Diagnostics 拡張機能を適用する

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFile = "<WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>ARM テンプレートを使用して Windows Azure Diagnostics 拡張機能を適用する
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md) または [Visual Studio](deploy-visual-studio.md) を使用して、クラウド サービス (延長サポート) をデプロイします。
