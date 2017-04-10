---
title: "Windows 用の OMS Azure 仮想マシン拡張機能 | Microsoft Docs"
description: "仮想マシン拡張機能を使用して、Windows 仮想マシンに OMS エージェントをデプロイします。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 9ce257eac44e51b7303016d993bc8d71e270629e
ms.lasthandoff: 03/15/2017


---
# <a name="oms-virtual-machine-extension-for-windows"></a>Windows 用の OMS 仮想マシン拡張機能

Operations Management Suite (OMS) は、クラウドとオンプレミスの資産全体にまたがる監視、アラート、アラート修復の機能を提供します。 Windows 用の OMS Agent 仮想マシン拡張機能は、Microsoft によって発行およびサポートされています。 この拡張機能は、Azure 仮想マシンに OMS エージェントをインストールし、仮想マシンを既存の OMS ワークスペースに登録します。 このドキュメントでは、Windows 用の OMS 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム
Windows 用の OMS Agent 拡張機能は、Windows Server 2008 R2、2012、2012 R2、2016 の各リリースで実行できます。

### <a name="internet-connectivity"></a>インターネット接続
Windows 用の OMS Agent 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、OMS Agent 拡張機能のスキーマを示しています。 この拡張機能では、ターゲット OMS ワークスペースのワークスペース ID とワークスペース キーが必要です (これらは OMS ポータルで確認できます)。 ワークスペース キーは機密データとして取り扱う必要があるため、保護された設定構成に格納される必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 **workspaceId** と **workspaceKey** の大文字と小文字は区別されることに注意してください。

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (例) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に OMS Agent 拡張機能を実行できます。 OMS Agent VM 拡張機能を含むサンプル テンプレートは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)にあります。 

仮想マシン拡張機能の JSON は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../azure-resource-manager/resource-manager-template-child-resource.md)に関する記事を参照してください。 

次の例では、OMS 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

拡張機能 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれて、種類は入れ子になっている構成を反映します。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzureRmVMExtension` コマンドを使用して、OMS Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure PowerShell モジュールを使用して次のコマンドを実行します。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/en-us/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。

