---
title: Windows の Azure カスタム スクリプト拡張機能 | Microsoft Docs
description: カスタム スクリプト拡張機能を使用して Windows VM の構成タスクを自動化します。
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2018
ms.author: roiyz
ms.openlocfilehash: 520ff1dfeefc8cca66710745012ee54b550a19a0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097925"
---
# <a name="custom-script-extension-for-windows"></a>Windows でのカスタムのスクリプト拡張機能

カスタム スクリプト拡張機能は、Azure 仮想マシンでスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

このドキュメントでは、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法について詳しく説明します。また、Windows システムでのトラブルシューティング手順についても詳しく説明します。

## <a name="prerequisites"></a>前提条件

> [!NOTE]  
> カスタム スクリプト拡張機能を使用してそのパラメーターと同じ VM で Update-AzVM を実行することは、自身を待機することになるため行わないでください。  
>   
> 

### <a name="operating-system"></a>オペレーティング システム

Linux 用カスタム スクリプト拡張機能は、サポートされている拡張機能 OS 上で動作します。詳細については、この[記事](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems)を参照してください。

### <a name="script-location"></a>スクリプトの場所

拡張機能を使用すると、Azure Blob ストレージの資格情報を使用して Azure Blob Storage にアクセスできます。 また、スクリプトは、GitHub や社内ファイル サーバーなどの、そのエンドポイントに VM をルーティングできる限り、任意の場所に保存できます。


### <a name="internet-connectivity"></a>インターネット接続
GitHub または Azure Storage などスクリプトを外部でダウンロードする必要がある場合は、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。 たとえば、スクリプトが Azure ストレージにある場合、[ストレージ](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)の Azure NSG サービス タグを使用してアクセスを許可できます。

スクリプトがローカル サーバー上にある場合も、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。

### <a name="tips-and-tricks"></a>ヒントとコツ
* この拡張機能が失敗する原因として最もよく挙げられるものはスクリプトの構文エラーであるため、スクリプトを実行してもエラーが発生しないことをテストしたり、失敗した個所を簡単に特定できるロギング機能をスクリプトに追加してください。
* 誤って複数回実行されたときも、システムに変更が加えられないよう、べき等のスクリプトを作成してください。
* スクリプトの実行時に、ユーザー入力を必要としないように作成してください。
* スクリプトの実行に許されているのは 90 分間で、これを超えると拡張機能へのプロビジョニングが失敗します。
* スクリプトの中に再起動を組み込まないでください。これを守らないと、インストールされているその他の拡張機能で問題が発生します。 再起動後にその拡張機能は実行されなくなります。 
* 再起動が必要なスクリプトの場合は、アプリケーションをインストールしてから、スクリプトを実行するなどしてください。Windows スケジュール化タスクを使用して、あるいは DSC、Chef、Puppet 拡張機能などのツールを使用して再起動をスケジュールすることができます。
* 拡張機能ではスクリプトを 1 回だけ実行します。すべての起動時にスクリプトを実行する場合は、拡張機能を使用して Windows スケジュール化タスクを作成する必要があります。
* スクリプトを実行する時期をスケジュールする場合は、拡張機能を使用して Windows スケジュール化タスクを作成する必要があります。 
* スクリプトが実行されている場合は、Azure Portal または CLI には拡張機能の状態が「移行中」とのみ表示されます。 実行中のスクリプトのステータスをより高い頻度で更新するには、独自のソリューションを作成する必要があります。
* カスタム スクリプト拡張機能では、プロキシ サーバーはネイティブではサポートされていませんが、*Curl* などの、プロキシ サーバーをサポートするファイル転送ツールをスクリプト内で使用することができます。 
* スクリプトまたはコマンドで使用している既定以外のディレクトリの場所に注意し、この状況を処理するロジックを用意してください。


## <a name="extension-schema"></a>拡張機能のスキーマ

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、構成ファイルに格納したり、コマンド ラインで指定したり、Azure Resource Manager テンプレートで指定したりできます。 

機微なデータは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

これらの項目は機密データとして扱う必要があり、拡張機能の保護された構成設定で指定する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
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
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**注**: 1 つの時点で VM にインストールできる拡張機能のバージョンは 1 つだけです。同じ VM は失敗するので、同じ Resource Manager テンプレートにはカスタム スクリプトを 2 回指定します。 

### <a name="property-values"></a>プロパティ値

| Name | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | 文字列 |
| type | CustomScriptExtension | 文字列 |
| typeHandlerVersion | 1.9 | int |
| fileUris (例) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (例:) | 123456789 | 32 ビットの整数 |
| commandToExecute (例) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 文字列 |
| storageAccountName (例) | examplestorageacct | 文字列 |
| storageAccountKey (例) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 文字列 |

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

#### <a name="property-value-details"></a>プロパティ値の詳細
* `commandToExecute`: (**必須**、文字列) 実行するエントリ ポイント スクリプト。 コマンドにパスワードなどの機密情報が含まれている場合、または fileUris が機密の場合は、代わりにこのフィールドを使用します。
  * `fileUris`: (省略可能、文字列の配列) ファイルをダウンロードする URL。
  * `timestamp` (省略可能、32 ビットの整数) このフィールドは、このフィールドの値を変更することによりスクリプトの再実行をトリガーする場合のみ使用します。  任意の整数値が使用できますが、前の値と異なる必要があります。
  * `storageAccountName`: (省略可能、文字列) ストレージ アカウントの名前。 ストレージの資格情報を指定する場合は、すべての `fileUris` が Azure BLOB の URL である必要があります。
  * `storageAccountKey`: (省略可能、文字列) ストレージ アカウントのアクセス キー。

パブリックまたはプロテクトのいずれかの設定に、次の値を設定することができます。拡張機能では、パブリックおよびプロテクトの両方の設定に以下の値が設定された場合、構成が拒否されます。
* `commandToExecute`

デバッグには、パブリック設定を使用することが便利な場合もありますが、保護された設定を使用することをお勧めします。

パブリック設定は、スクリプトを実行する仮想マシンにクリア テキストで送信されます。  保護された設定は、Azure と VM のみが知っているキーを使用して暗号化されます。 設定は、送信された VM に保存されます。つまり、設定が暗号化されていた場合は、VM 上に暗号化された状態で保存されます。 暗号化された値を復号化するために使用する証明書は、VM に格納され、実行時の設定 (必要な場合) の暗号化を解除するためにも使用されます。

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時にカスタム スクリプト拡張機能を実行できます。 次のサンプルでは、カスタム スクリプト拡張機能を使用する方法を示します。

* [チュートリアル: Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Deploy Two Tier Application on Windows and Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Windows と Azure SQL DB に 2 層アプリケーションをデプロイする)

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMCustomScriptExtension` コマンドを使用して、カスタム スクリプト拡張機能を既存の仮想マシンに追加できます。 詳細については、[Set-AzVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmcustomscriptextension) を参照してください。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>その他の例

### <a name="using-multiple-script"></a>複数のスクリプトを使用する
次の例では、サーバーのビルドに 3 つのスクリプトを使用します。'commandToExecute' で最初のスクリプトを呼出してから、残りのスクリプトを呼び出す方法を選ぶことができます。たとえば、実行を制御するマスター スクリプトを使用して、エラーを適正に処理し、ログを記録し、状態を管理できます。

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>ローカルの共有からスクリプトを実行する
次の例では、スクリプトの場所にローカル SMB サーバーを使用できます。*commandToExecute* を除き、他の設定にする必要はないことに注意してください。

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>CLI でカスタム スクリプトを複数回実行する方法
カスタム スクリプト拡張機能を複数回実行する場合、そのアクションは、次の条件下でのみ行うことができます。
1. 拡張機能 'Name' パラメーターが拡張機能の以前のデプロイと同じである。
2. 構成を更新する必要がある。更新しない場合、コマンドは再実行されません。 コマンドには、タイムスタンプなどの動的プロパティを追加できます。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、ターゲット仮想マシンの次のディレクトリ内のファイルにログ記録されます。
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定したファイルは、ターゲット仮想マシンの次のディレクトリにダウンロードされます。
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
`<n>` は 10 進整数であり、拡張機能の実行間で変わる可能性があります。  `1.*` 値は、拡張機能の実際の現在の `typeHandlerVersion` 値と一致します。  たとえば、実際のディレクトリは、`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` になります。  

`commandToExecute` コマンドを実行すると、拡張機能によってこのディレクトリ (例: `...\Downloads\2`) が現在の作業ディレクトリとして設定されます。 この処理により、`fileURIs` プロパティを使用してダウンロードされたファイルを見つけるときに相対パスを使用できます。 例については、下記の表を参照してください。

絶対ダウンロード パスは経時的に変わる可能性があるため、可能であれば、`commandToExecute` の文字列では相対スクリプト/ファイル パスを使用することをお勧めします。 例: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

最初の URI セグメントの後のパス情報は、`fileUris` プロパティのリストを使用してダウンロードされたファイル用に保持されます。  次の表に示すように、`fileUris` 値の構造を反映するために、ダウンロードされたファイルはダウンロード サブディレクトリにマップされます。  

#### <a name="examples-of-downloaded-files"></a>ダウンロードされたファイルの例

| fileUris の URI | ダウンロードされた相対的な場所 | ダウンロードされた絶対的な場所* |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* 上記のように、絶対ディレクトリ パスは VM の有効期間中に変わりますが、CustomScript 拡張機能の 1 回の実行中に変わることはありません。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 Azure サポート インシデントを送信することもできます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
