---
title: "Windows VM のカスタム スクリプト拡張機能 | Microsoft Docs"
description: "カスタム スクリプト拡張機能を使って Azure VM 構成タスクを自動化し、リモート Windows VM でPowerShell スクリプトを実行する"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6b1195281ef1937cfa466088be1c01da9e08b7f9
ms.lasthandoff: 03/25/2017


---

# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>クラシック デプロイメント モデルを使用した Windows 用のカスタム スクリプト拡張機能

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](../../virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について説明します。

カスタム スクリプト拡張機能は、Azure 仮想マシンでスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

このドキュメントでは、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法について詳しく説明します。また、Windows システムでのトラブルシューティング手順についても詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Windows 用のカスタム スクリプト拡張機能は、Windows Server 2008 R2、2012、2012 R2、2016 の各リリースで実行できます。

### <a name="script-location"></a>スクリプトの場所

スクリプトは、Azure Storage か、有効な URLを通じてアクセスできるその他の場所に格納されている必要があります。

### <a name="internet-connectivity"></a>インターネット接続

Windows 用のカスタム スクリプト拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、カスタム スクリプト拡張機能のスキーマを示しています。 この拡張機能には、スクリプトの場所 (Azure Storage か、有効な URL でアクセスできるその他の場所) と、実行するコマンドが必要です。 Azure Storage をスクリプトのソースとして使用する場合は、Azure Storage のアカウント名とアカウント キーが必要です。 これらの項目は機密データとして扱う必要があり、拡張機能の保護された構成設定で指定する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| 拡張機能 | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (例) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (例) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時にカスタム スクリプト拡張機能を実行できます。 カスタム スクリプト拡張機能を含むサンプル テンプレートは、[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) で入手できます。

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzureVMCustomScriptExtension` コマンドを使用して、カスタム スクリプト拡張機能を既存の仮想マシンに追加できます。 詳細については、「[Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)」をご覧ください。

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、ターゲット仮想マシンの次のディレクトリ内のファイルにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

スクリプト自体は、ターゲット仮想マシンの次のディレクトリにダウンロードされます。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/en-us/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。
