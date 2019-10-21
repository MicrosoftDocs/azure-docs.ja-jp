---
title: Azure Desired State Configuration 拡張機能ハンドラー | Microsoft Docs
description: DSC 拡張機能を使用して Azure VM に PowerShell DSC 構成をアップロード､適用します｡
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: ee5a6c732bcb48cd347b8d87b95d2896d7230a08
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092370"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC 拡張機能

## <a name="overview"></a>概要

Windows 用の PowerShell DSC 拡張機能は､Microsoft から公開､サポートされています｡ この拡張機能は Azure VM に PowerShell DSC 構成をアップロード､適用します｡ DSC 拡張機能は、PowerShell DSC を呼び出して、受け取った DSC 構成を VM に適用します。 このドキュメントでは、Windows 用の DSC 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

DSC 拡張機能は､次の OS に対応しています｡

Windows Server 2019、Windows Server 2016、Windows Server 2012R2、Windows Server 2012、Windows Server 2008 R2 SP1、 Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>インターネット接続

Windows 用の DSC 拡張機能では、ターゲットの仮想マシンが Azure と通信できることが必要になります。また、構成パッケージ (.zip ファイル) が Azure の外部の場所に格納されている場合は、その場所と通信できる必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Azure Resource Manager テンプレートの DSC 拡張機能の settings 部分のスキーマを示しています｡ 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>設定のプロパティ値

| 名前 | データ型 | 説明
| ---- | ---- | ---- |
| settings.wmfVersion | string | VM にインストールする Windows Management Framework のバージョンを指定します。 このプロパティを "latest" に設定すると、WMF の最新バージョンがインストールされます。 現在、このプロパティに設定できる値は、4.0 か 5.0、latest のみです。 これらの設定できる値は更新される可能性があります。 既定値は "latest" です。 |
| settings.configuration.url | string | DSC 構成 zip ファイルのダウンロード元の URL の場所を指定します。 指定した URL へのアクセスに SAS トークンが必要な場合、protectedSettings.configurationUrlSasToken プロパティに SAS トークンの値を設定する必要があります。 settings.configuration.script または settings.configuration.function を定義する場合、このプロパティは必須です。
| settings.configuration.script | string | DSC 構成の定義を含むスクリプトのファイル名を指定します。 このスクリプトは、configuration.url プロパティで指定した URL からダウンロードされた zip ファイルのルート フォルダーに含まれている必要があります。 settings.configuration.url または settings.configuration.script を定義する場合、このプロパティは必須です。
| settings.configuration.function | string | DSC 構成の名前を指定します。 名前が指定された構成は、configuration.script で定義したスクリプト内に含まれている必要があります。 settings.configuration.url または settings.configuration.functiont を定義する場合、このプロパティは必須です。
| settings.configurationArguments | コレクション | DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されません｡
| settings.configurationData.url | string | DSC 構成の入力として使用する構成データ (.pds1) ファイルのダウンロード元の URL を指定します。 指定した URL へのアクセスに SAS トークンが必要な場合、protectedSettings.configurationDataUrlSasToken プロパティに SAS トークンの値を設定する必要があります。
| settings.privacy.dataEnabled | string | テレメトリの収集を有効または無効にします。 このプロパティに指定できる値は、Enable か Disable、"､$null のみです。 このプロパティを空白または null のままにすると、テレメトリが有効になります。
| settings.advancedOptions.forcePullAndApply | Bool | この設定の目的は、拡張機能を使用して Azure Automation DSC にノードを登録する際のエクスペリエンスを向上させることです。  値が `$true` の場合、この拡張機能は、成功または失敗を返す前に、サービスからプルされた構成が最初に実行されるのを待ちます。  値が $false に設定されている場合は、拡張機能によって返される状態により、ノードが Azure Automation State Configuration に正常に登録されているかどうかが示されるだけで、登録時にノード構成は実行されません。
| settings.advancedOptions.downloadMappings | コレクション | WMF や .NET などの依存関係をダウンロードする代替場所を定義します｡

### <a name="protected-settings-property-values"></a>保護された設定のプロパティ値

| 名前 | データ型 | 説明
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されます｡ |
| protectedSettings.configurationUrlSasToken | string | configuration.url で定義した URL にアクセスするための SAS トークンを指定します。 このプロパティは暗号化されます｡ |
| protectedSettings.configurationDataUrlSasToken | string | ConfigurationData.url で定義した URL にアクセスするための SAS トークンを指定します。 このプロパティは暗号化されます｡ |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。
テンプレートは、デプロイ後の構成が必要な仮想マシンを 1 つ以上デプロイするときに最適です。
Windows 用の DSC 拡張機能を含む Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)にあります。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能パッケージがダウンロードされて､Azure VM 上のこの場所にデプロイされます｡
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

拡張機能状態ファイルには､拡張機能の実行ごとにサブ状態と状態の成功/エラー コードと共に､詳細なエラーと説明が含まれます。
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

拡張機能の出力ログは､次のディレクトリに記録されます｡

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>エラー コードとその意味

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 1000 | 一般的なエラー | 拡張機能のログには､このエラーのメッセージが特定の例外によって提供されます｡ |
| 52 | 拡張機能のインストール エラー | このエラーのメッセージは特定の例外によって提供されます｡ |
| 1002 | WMF インストール エラー | WMF のインストール中のエラーです｡ |
| 1004 | 無効な zip パッケージ | 無効な zip｡zipの解凍エラー｡ |
| 1100 | 引数エラー | ユーザーが指定した入力に問題があったことを示します｡ このエラーのメッセージは特定の例外によって提供されます｡|


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
