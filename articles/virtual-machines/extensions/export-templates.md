---
title: VM 拡張機能を含む Azure リソース グループのエクスポート | Microsoft Docs
description: 仮想マシン拡張機能を含む Resource Manager テンプレートをエクスポートします。
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 652ed732a7fe8f08e48aba6fc4bd1b52164d1fa0
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169049"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>VM 拡張機能を含むリソース グループのエクスポート

Azure リソース グループを、再デプロイ可能な新しい Resource Manager テンプレートにエクスポートできます。 エクスポート プロセスでは、既存のリソースが解釈され、Resource Manager テンプレートが作成されます。これは、デプロイされると同様のリソース グループになります。 仮想マシン拡張機能を含むリソース グループに対してリソース グループのエクスポート オプションを使用する場合、拡張機能の互換性や保護された設定など、いくつかの項目について考慮する必要があります。

このドキュメントでは、サポートされる拡張機能の一覧やセキュリティで保護されているデータの処理の詳細を含め、仮想マシン拡張機能に関するリソース グループのエクスポート プロセスのしくみについて説明します。

## <a name="supported-virtual-machine-extensions"></a>サポートされる仮想マシン拡張機能

利用可能な仮想マシン拡張機能は多数あります。 しかし、すべての拡張機能が "Automation スクリプト" 機能を使用して Resource Manager テンプレートにエクスポートできるわけではありません。 サポートされていない仮想マシン拡張機能は、エクスポートされたテンプレートに手動で配置する必要があります。

次の拡張機能は、Automation スクリプト機能を使用してエクスポートできます。

| 拡張機能 ||||
|---|---|---|---|
| Acronis Backup | Datadog Windows Agent | Linux 用の OS の修正プログラム | VM スナップショット Linux
| Acronis Backup Linux | Docker 拡張機能 | Puppet Agent |
| BgInfo | DSC 拡張機能 | Site 24x7 APM インサイト |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux サーバー |
| BMC CTM Agent Windows | Dynatrace Windows | Site 24x7 Windows サーバー |
| Chef Client | HPE Security Application Defender | Trend Micro DSA |
| カスタム スクリプト | IaaS マルウェア対策 | Trend Micro DSA Linux |
| カスタム スクリプト拡張機能 | IaaS 診断 | Linux 用 VMAccess |
| Linux 用のカスタム スクリプト | Linux Chef Client | Linux 用 VMAccess |
| Datadog Linux Agent | Linux 診断 | VM スナップショット |

## <a name="export-the-resource-group"></a>リソース グループをエクスポートする

リソース グループを再利用可能なテンプレートにエクスポートするには、次の手順を実行します。

1. Azure portal にサインインする
2. ハブ メニューの [リソース グループ] をクリックします。
3. 一覧から、ターゲット リソース グループを選択します。
4. [リソース グループ] ブレードで、[Automation スクリプト] をクリックします。

![テンプレートのエクスポート](./media/export-templates/template-export.png)

Azure Resource Manager の Automation スクリプトは、Resource Manager テンプレート、パラメーター ファイル、いくつかのサンプル デプロイ スクリプト (PowerShell や Azure CLI など) を生成します。 この時点で、エクスポートされたテンプレートは、ダウンロード ボタンを使用してダウンロードしたり、新しいテンプレートとしてテンプレート ライブラリに追加したり、デプロイ ボタンを使用して再デプロイしたりできます。

## <a name="configure-protected-settings"></a>保護された設定を構成する

多くの Azure 仮想マシン拡張機能には保護された設定の構成が含まれていて、資格情報や構成文字列などの機密データを暗号化します。 保護された設定は、Automation スクリプトではエクスポートされません。 必要に応じて、保護された設定をエクスポートされたテンプレートに再度挿入する必要があります。

### <a name="step-1---remove-template-parameter"></a>手順 1. テンプレート パラメーターを削除する

リソース グループをエクスポートする場合、エクスポートされる保護された設定に値を指定するテンプレート パラメーターが 1 つ作成されます。 このパラメーターは削除できます。 このパラメーターを削除するには、パラメーターの一覧を調べて、次に示す JSON の例に類似したパラメーターを削除します。

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>手順 2. 保護された設定のプロパティを取得する

保護された設定にはそれぞれ一連の必須プロパティがあるため、これらの一連のプロパティを収集する必要があります。 保護された設定の構成の各パラメーターについては、[GitHub の Azure Resource Manager のスキーマ](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)を参照してください。 このスキーマには、このドキュメントの概要セクションに示した拡張機能のパラメーター セットのみが含まれています。 

スキーマ リポジトリ内で、目的の拡張機能 (この例では `IaaSDiagnostics`) を検索します。 拡張機能の `protectedSettings` オブジェクトが見つかったら、各パラメーターをメモしておきます。 この `IaasDiagnostic` 拡張機能の例では、必須パラメーターは `storageAccountName`、`storageAccountKey`、`storageAccountEndPoint` です。

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>手順 3. 保護された構成を再作成する

エクスポートされたテンプレートで `protectedSettings` を検索し、エクスポートされた保護された設定のオブジェクトを、拡張機能の必須パラメーターとそれぞれの値を含む新しいオブジェクトに置き換えます。

この `IaasDiagnostic` 拡張機能の例では、新しい保護された設定の構成は、次の例のようになります。

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

最終的な拡張機能のリソースは、次の JSON の例のようになります。

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

テンプレート パラメーターを使用してプロパティ値を指定する場合は、これらを作成する必要があります。 保護された設定の値用のテンプレート パラメーターを作成するときは、機密性の高い値が保護されるように、`SecureString` パラメーター型を使用してください。 パラメーターの使用の詳細については、「[Azure Resource Manager のテンプレートの作成](../../resource-group-authoring-templates.md)」を参照してください。

この `IaasDiagnostic` 拡張機能の例では、Resource Manager テンプレートのパラメーター セクションに次のパラメーターが作成されます。

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

この時点で、任意のテンプレート デプロイ方法を使用して、テンプレートをデプロイできます。
