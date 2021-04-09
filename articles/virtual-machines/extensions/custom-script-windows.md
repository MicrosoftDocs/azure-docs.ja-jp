---
title: Windows 向けの Azure カスタム スクリプト拡張機能
description: カスタム スクリプト拡張機能を使用して Windows VM の構成タスクを自動化します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 13b4c4ef50ea37cabe30474d339acb19176cef97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553903"
---
# <a name="custom-script-extension-for-windows"></a>Windows でのカスタムのスクリプト拡張機能

カスタム スクリプト拡張機能は、Azure 仮想マシンでスクリプトをダウンロードし、実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行できます。

このドキュメントでは、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用したカスタム スクリプト拡張機能の使用方法について詳しく説明します。また、Windows システムでのトラブルシューティング手順についても詳しく説明します。

## <a name="prerequisites"></a>前提条件

> [!NOTE]  
> カスタム スクリプト拡張機能を使用してそのパラメーターと同じ VM で Update-AzVM を実行することは、自身を待機することになるため行わないでください。  

### <a name="operating-system"></a>オペレーティング システム

Windows 用カスタム スクリプト拡張機能は、サポートされている拡張機能 OS で動作します。

### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>スクリプトの場所

Azure Blob ストレージの資格情報を使用して Azure Blob Storage にアクセスするように拡張機能を構成できます。 スクリプトは、GitHub や社内ファイル サーバーなど、そのエンドポイントに VM をルーティングできる限り、任意の場所に保存できます。

### <a name="internet-connectivity"></a>インターネット接続

GitHub または Azure Storage などからスクリプトを外部でダウンロードする必要がある場合は、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。 たとえば、スクリプトが Azure Storage にある場合、[ストレージ](../../virtual-network/network-security-groups-overview.md#service-tags)に Azure NSG サービス タグを使用することでアクセスを許可できます。

CustomScript 拡張機能には、証明書の検証を省略する方法がないことに注意してください。 たとえば、セキュリティで保護された場所から 自己署名証明書などをダウンロードするときに、"*検証プロシージャによると、リモート証明書は無効です*" のようなエラーが発生する可能性があります。 証明書が、仮想マシンの *"信頼されたルート証明機関"* ストアに正しくインストールされていることを確認してください。

スクリプトがローカル サーバー上にあるとき、場合によっては、ファイアウォールを追加し、ネットワーク セキュリティ グループ ポートを開く必要があります。

### <a name="tips-and-tricks"></a>ヒントとコツ

* この拡張機能が非常に高い確率で失敗する原因は、スクリプトの構文エラーです。エラーなく実行できるかスクリプトをテストし、さらに、失敗した場所を簡単に見つけられるよう、スクリプトのログ記録を追加してください。
* べき等のスクリプトを記述してください。 それにより、誤って再び実行されることがあっても、システム変更が引き起こされません。
* スクリプトの実行時に、ユーザー入力を必要としないように作成してください。
* スクリプトの実行に許されているのは 90 分間で、これを超えると拡張機能へのプロビジョニングが失敗します。
* スクリプトの中に再起動を組み込まないでください。これを守らないと、インストールされているその他の拡張機能で問題が発生します。 再起動後にその拡張機能は実行されなくなります。
* スクリプトが再起動を誘導するとき、アプリケーションをインストールし、スクリプトを実行する場合、Windows スケジュール化タスクを使用して再起動をスケジュールしたり、DSC、Chef、Puppet 拡張機能などのツールを使用したりすることができます。
* VM エージェントの停止または更新の原因となるスクリプトを実行することはお勧めしません。 これにより、拡張機能が移行中の状態のままになり、タイムアウトになる可能性があります。
* 拡張機能ではスクリプトを 1 回だけ実行します。すべての起動時にスクリプトを実行する場合は、拡張機能を使用して Windows スケジュール化タスクを作成する必要があります。
* スクリプトを実行する時期をスケジュールする場合は、拡張機能を使用して Windows スケジュール化タスクを作成する必要があります。
* スクリプトが実行されている場合は、Azure Portal または CLI には拡張機能の状態が「移行中」とのみ表示されます。 実行中のスクリプトのステータスをより高い頻度で更新するには、独自のソリューションを作成する必要があります。
* カスタム スクリプト拡張機能では、プロキシ サーバーはネイティブではサポートされていませんが、*Invoke-WebRequest* などの、プロキシ サーバーをサポートするファイル転送ツールをスクリプト内で使用できます
* スクリプトまたはコマンドで使用している既定以外のディレクトリの場所に注意し、この状況を処理するロジックを用意してください。
* カスタム スクリプト拡張機能は LocalSystem アカウントで実行されます
* *storageAccountName* プロパティと *storageAccountKey* プロパティの使用を計画している場合は、これらのプロパティを *protectedSettings* に併置する必要があります。

## <a name="extension-schema"></a>拡張機能のスキーマ

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、構成ファイルに格納したり、コマンド ラインで指定したり、Azure Resource Manager テンプレートで指定したりできます。

機微なデータは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

これらの項目は機密データとして扱う必要があり、拡張機能の保護された構成設定で指定する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> managedIdentity プロパティ を storageAccountName プロパティまたは storageAccountKey プロパティと組み合わせて使用することは **できません**

> [!NOTE]
> 1 つの時点で VM にインストールできる拡張機能のバージョンは 1 つだけです。同じ VM は失敗するので、同じ Resource Manager テンプレートにはカスタム スクリプトを 2 回指定します。

> [!NOTE]
> このスキーマは、VirtualMachine リソース内で、またはスタンドアロン リソースとして使用できます。 この拡張機能が ARM テンプレートでスタンドアロン リソースとして使用される場合、リソースの名前は "virtualMachineName/extensionName" という形式である必要があります。

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.10 | INT |
| fileUris (例) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (例:) | 123456789 | 32-bit integer |
| commandToExecute (例) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (例) | examplestorageacct | string |
| storageAccountKey (例) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity (例) | { } or { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } or { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON オブジェクト |

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

#### <a name="property-value-details"></a>プロパティ値の詳細

* `commandToExecute`: (**必須**、文字列) 実行するエントリ ポイント スクリプト。 コマンドにパスワードなどの機密情報が含まれている場合、または fileUris が機密の場合は、代わりにこのフィールドを使用します。
* `fileUris`: (省略可能、文字列の配列) ファイルをダウンロードする URL。
* `timestamp` (省略可能、32 ビットの整数) このフィールドは、このフィールドの値を変更することによりスクリプトの再実行をトリガーする場合のみ使用します。  任意の整数値が使用できますが、前の値と異なる必要があります。
* `storageAccountName`: (省略可能、文字列) ストレージ アカウントの名前。 ストレージの資格情報を指定する場合は、すべての `fileUris` が Azure BLOB の URL である必要があります。
* `storageAccountKey`: (省略可能、文字列) ストレージ アカウントのアクセス キー
* `managedIdentity`: (省略可能、json オブジェクト) ファイルをダウンロードするための[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)
  * `clientId`: (省略可能、文字列) マネージド ID のクライアント ID
  * `objectId`: (省略可能、文字列) マネージド ID のオブジェクト ID

パブリックまたはプロテクトのいずれかの設定に、次の値を設定することができます。拡張機能では、パブリックおよびプロテクトの両方の設定に以下の値が設定された場合、構成が拒否されます。

* `commandToExecute`

デバッグには、パブリック設定を使用することが便利な場合もありますが、保護された設定を使用することをお勧めします。

パブリック設定は、スクリプトを実行する仮想マシンにクリア テキストで送信されます。  保護された設定は、Azure と VM のみが知っているキーを使用して暗号化されます。 設定は、送信時に VM に保存されます。つまり、設定が暗号化されていた場合は、VM 上に暗号化された状態で保存されます。 暗号化された値を復号化するために使用する証明書は、VM に格納され、実行時の設定 (必要な場合) の暗号化を解除するためにも使用されます。

####  <a name="property-managedidentity"></a>プロパティ: managedIdentity
> [!NOTE]
> このプロパティは、保護された設定でのみ指定する **必要があります**。

CustomScript (バージョン 1.10 以降) では、"fileUris" 設定で指定された URL からファイルをダウンロードするための[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) ベースの RBAC がサポートされています。 これにより、ユーザーが SAS トークンやストレージ アカウント キーなどのシークレットを渡さなくとも、CustomScript で Azure Storage プライベート BLOB またはコンテナーにアクセスできるようになります。

この機能を使用するには、ユーザーが、[システムによって割り当てられたか](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)または[ユーザーが割り当てた](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) ID を CustomScript が実行されると想定される VM または VMSS に追加し、[Azure Storage コンテナーまたは BLOB にマネージド ID のアクセス権を付与する必要があります](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)。

ターゲット VM/VMSS でシステムによって割り当てられた ID を使用するには、"managedidentity" フィールドを空の JSON オブジェクトに設定します。 

> 例:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

ターゲット VM/VMSS でユーザーが割り当てた ID を使用するには、"managedidentity" フィールドを、マネージド ID のクライアント ID またはオブジェクト ID で構成します。

> 例 :
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity プロパティ を storageAccountName プロパティまたは storageAccountKey プロパティと組み合わせて使用することは **できません**

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、デプロイ時にカスタム スクリプト拡張機能を実行できます。 次のサンプルでは、カスタム スクリプト拡張機能を使用する方法を示します。

* [チュートリアル:Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Deploy Two Tier Application on Windows and Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows) (Windows と Azure SQL DB に 2 層アプリケーションをデプロイする)

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMCustomScriptExtension` コマンドを使用して、カスタム スクリプト拡張機能を既存の仮想マシンに追加できます。 詳細については、[Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension) を参照してください。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>追加の例

### <a name="using-multiple-scripts"></a>複数のスクリプトを使用する

この例では、サーバーの構築に利用されるスクリプトが 3 つあります。 **commandToExecute** は最初のスクリプトを呼び出します。他のスクリプトの呼び出し方に関する選択肢が与えられます。 たとえば、マスター スクリプトで実行を制御し、適切にエラーを処理し、ログを記録し、状態を管理することができます。 スクリプトはローカル コンピューターにダウンロードされ、実行されます。 たとえば、`1_Add_Tools.ps1` で、スクリプトに `.\2_Add_Features.ps1` を追加することで `2_Add_Features.ps1` を呼び出し、`$settings` に定義した他のスクリプトに対してこの過程を繰り返します。

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>ローカルの共有からスクリプトを実行する

この例では、スクリプトの場所にローカル SMB サーバーを使用することをお勧めします。 そうすることで、**commandToExecute** を除く、他の設定を提供する必要がありません。

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>CLI でカスタム スクリプトを複数回実行する方法

カスタム スクリプト拡張機能を複数回実行する場合、そのアクションは、次の条件下でのみ行うことができます。

* 拡張機能 **Name** パラメーターは拡張機能の前のデプロイと同じです。
* 構成を更新します。そうしないと、コマンドは再実行されません。 コマンドには、タイムスタンプなどの動的プロパティを追加できます。

または、[ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) プロパティを **true** に設定できます。

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest の使用

スクリプトで [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) を使っている場合は、パラメーター `-UseBasicParsing` を指定する必要があります。そうしないと、詳細な状態をチェックするときに次のエラーを受け取ります。

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

カスタム スクリプト拡張機能をスケール セットにデプロイするには、「[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)」を参照してください。

## <a name="classic-vms"></a>クラシック VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

クラシック VM でカスタム スクリプト拡張機能をデプロイするには、Azure portal または Classic Azure PowerShell コマンドレットを使用できます。

### <a name="azure-portal"></a>Azure portal

クラシック VM リソースに移動します。 **[設定]** で **[拡張機能]** を選択します。

**[+ 追加]** をクリックし、リソースの一覧で **[カスタム スクリプト拡張機能]** を選択します。

**[拡張機能のインストール]** ページでローカル PowerShell ファイルを選択し、引数が必要であればそれを入力し、 **[OK]** をクリックします。

### <a name="powershell"></a>PowerShell

[Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) コマンドレットを使用すると、カスタム スクリプト拡張機能を既存の仮想マシンに追加できます。

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

デプロイ先の仮想マシンの次のフォルダーにあるファイルに拡張機能出力のログが記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

デプロイ先の仮想マシンの次のフォルダーに指定のファイルがダウンロードされます。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

`<n>` は 10 進整数であり、拡張機能の実行間で変わる可能性があります。  `1.*` 値は、拡張機能の実際の現在の `typeHandlerVersion` 値と一致します。  たとえば、実際のディレクトリは、`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` になります。  

`commandToExecute` コマンドを実行すると、拡張機能によってこのディレクトリ (例: `...\Downloads\2`) が現在の作業ディレクトリとして設定されます。 この処理により、`fileURIs` プロパティを使用してダウンロードされたファイルを見つけるときに相対パスを使用できます。 例については、下記の表を参照してください。

絶対ダウンロード パスは経時的に変わる可能性があるため、可能であれば、`commandToExecute` の文字列では相対スクリプト/ファイル パスを使用することをお勧めします。 次に例を示します。

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

最初の URI セグメントの後のパス情報は、`fileUris` プロパティ リスト経由でダウンロードされるファイルのために保持されます。  次の表に示すように、`fileUris` 値の構造を反映するために、ダウンロードされたファイルはダウンロード サブディレクトリにマップされます。  

#### <a name="examples-of-downloaded-files"></a>ダウンロードされたファイルの例

| fileUris の URI | ダウンロードされた相対的な場所 | ダウンロードされた絶対的な場所 <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> 絶対ディレクトリ パスは VM のライフタイムの経過と共に変わりますが、CustomScript の 1 回の実行内でのみの変更となります。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 Azure サポート インシデントを送信することもできます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
