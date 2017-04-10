---
title: "Windows の Azure カスタム スクリプト拡張機能 | Microsoft Docs"
description: "カスタム スクリプト拡張機能を使用して Windows VM の構成タスクを自動化します。"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: bd67dc463daee2d7763e722f079930d8712fe478
ms.openlocfilehash: 81a08a3cbd14c4a61efe68d9dd9fcd032dd1e1cd
ms.lasthandoff: 02/23/2017


---
# <a name="custom-script-extension-for-windows"></a>Windows でのカスタムのスクリプト拡張機能

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
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| type | 拡張機能 |
| typeHandlerVersion | 1.8 |
| fileUris (例) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (例) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (例) | examplestorageacct |
| storageAccountKey (例) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時にカスタム スクリプト拡張機能を実行できます。 カスタム スクリプト拡張機能を含むサンプル テンプレートは、[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) で入手できます。

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzureRmVMCustomScriptExtension` コマンドを使用して、カスタム スクリプト拡張機能を既存の仮想マシンに追加できます。 詳細については、「[Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension)」をご覧ください。
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、ターゲット仮想マシンの次のディレクトリ内のファイルにログ記録されます。
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定したファイルは、ターゲット仮想マシンの次のディレクトリにダウンロードされます。
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
`<n>` は&10; 進整数であり、拡張機能の実行間で変わる可能性があります。  `1.*` 値は、拡張機能の実際の現在の `typeHandlerVersion` 値と一致します。  たとえば、実際のディレクトリは、`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` になります。  

`commandToExecute` コマンドを実行すると、拡張機能によってこのディレクトリ (例: `...\Downloads\2`) が現在の作業ディレクトリとして設定されます。 これにより、`fileURIs` プロパティを使用してダウンロードされたファイルを見つけるときに相対パスを使用できます。 例については、下記の表を参照してください。

絶対ダウンロード パスは経時的に変わる可能性があるため、可能であれば、`commandToExecute` の文字列では相対スクリプト/ファイル パスを使用することをお勧めします。 次に例を示します。
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

最初の URI セグメントの後のパス情報は、`fileUris` プロパティのリストを使用してダウンロードされたファイル用に保持されます。  次の表に示すように、`fileUris` 値の構造を反映するために、ダウンロードされたファイルはダウンロード サブディレクトリにマップされます。  

#### <a name="examples-of-downloaded-files"></a>ダウンロードされたファイルの例

| fileUris の URI | ダウンロードされた相対的な場所 | ダウンロードされた絶対的な場所* |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 上記のように、絶対ディレクトリ パスは VM の有効期間中に変わりますが、CustomScript 拡張機能の&1; 回の実行中に変わることはありません。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム] (https://azure.microsoft.com/en-us/support/forums/) で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。

