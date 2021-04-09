---
title: Stackify Retrace Azure Linux Agent 拡張機能
description: Linux 仮想マシンに Stackify Retrace Linux エージェントをデプロイします｡
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559003"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux Agent 拡張機能

## <a name="overview"></a>概要

Stackify は､問題を素早く発見､解決するのに役立つ､アプリケーションに関する詳細を記録する製品を提供します｡ 開発者のチームにとって､Retrace は複数環境をサポートする完全統合型のアプリ パフォーマンスのスーパー パワーです｡ Retrace には､あらゆる開発チームが必要とするいくつかのツールが統合されています｡

Retrace は 1 つのプラットフォーム上のすべての環境にまたがって次の機能のすべてを提供する唯一のツールです｡

* APM (Application performance management)
* アプリケーションとサーバーログの記録
* エラーの追跡と監視
* サーバー､アプリケーション､およびカスタム メトリック

**Stackify Linux Agent 拡張機能について**

この拡張機能は､Retrace 用 Linux Agent のインストール パスを提供します｡ 

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム 

Retrace エージェントは､次の Linux ディストリビューションに対して実行できます｡

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS､14.04 LTS､16.10､17.04 |
| Debian | 7.9+ および 8.2+､9 |
| Red Hat | 6.7+､7.1+ |
| CentOS | 6.3+､7.0+ |

### <a name="internet-connectivity"></a>インターネット接続

Linux 用の Stackify Agent 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

Stackify への接続を許可するために､ネットワーク構成の調整が必要になることがあります｡ https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall を参照してください｡ 


## <a name="extension-schema"></a>拡張機能のスキーマ

---

次の JSON は、Stackify Retrace Agent 拡張機能のスキーマを示しています。 この拡張機能には `environment` および `activationKey` が必要です｡

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>テンプレートのデプロイ 

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に Stackify Retrace Linux Agent 拡張機能を実行できます。  

仮想マシン拡張機能の JSON は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON の配置は、リソースの名前と種類の値に影響します。 詳細は、子リソースの名前と種類の設定に関する記事を参照してください。

次の例では、Stackify Retrace Linux 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの "resources": [] オブジェクトに配置されます。

この拡張機能には `environment` および `activationKey` が必要です｡

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

拡張機能 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれて、種類は入れ子になっている構成を反映します。

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMExtension` コマンドを使用して、Stackify Retrace Linux Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。

この拡張機能には `environment` および `activationKey` が必要です｡

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ 

Azure CLI ツールを使用して、Stackify Retrace Linux Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。  

この拡張機能には `environment` および `activationKey` が必要です｡

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="error-codes"></a>エラー コード

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 10 | インストール エラー | wget が必要 |
| 20 | インストール エラー | python が必要 |
| 30 | インストール エラー | sudo が必要 |
| 40 | インストール エラー | activationKey が必要 |
| 51 | インストール エラー | OS distro はサポートされていません |
| 60 | インストール エラー | environment が必要 |
| 70 | インストール エラー | Unknown |
| 80 | 有効化エラー | サービスの設定に失敗しました |
| 90 | 有効化エラー | サービスの開始に失敗しました |
| 100 | 無効化エラー | サービスの停止に失敗しました |
| 110 | 無効化エラー | サービスの削除に失敗しました |
| 120 | アンインストール エラー | サービスの停止に失敗しました |

ヘルプが必要な場合は､Stackify サポートに問い合わせることができます (問い合わせ先: https://support.stackify.com )｡