---
title: Linux 用の Azure DSC 拡張機能
description: Desired State Configuration を使用して Azure Linux VM を構成できるよう、OMI パッケージと DSC パッケージをインストールします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.openlocfilehash: 3a9478f93a2a6f8e31be39a58a18e0e394b9d5c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560108"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux 用の DSC 拡張機能 (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) は、コードで構成を行って IT および開発インフラストラクチャを管理するために使用できる管理プラットフォームです。

> [!NOTE]
> Linux 用の DSC 拡張機能と [Linux 用の Log Analytics 仮想マシン拡張機能](./oms-linux.md)には現在競合があるため、並列構成ではサポートされていません。 2 つのソリューションを同じ VM で同時に使用しないでください。

DSCForLinux 拡張機能は Microsoft によって公開され、サポートされています。 拡張機能では、OMI および DSC のエージェントが Azure 仮想マシンにインストールされます。 DSC 拡張機能では、さらに次のアクションを実行できます。

- Azure Automation サービスから構成をプルするために、Linux VM を Azure Automation アカウントに登録する (Register ExtensionAction)。
- MOF 構成を Linux VM にプッシュする (Push ExtensionAction)。
- ノード構成をプルするために、メタ MOF 構成を Linux VM に適用してプル サーバーを構成する (Pull ExtensionAction)。
- カスタム DSC モジュールを Linux VM にインストールする (Install ExtensionAction)。
- カスタム DSC モジュールを Linux VM から削除する (Remove ExtensionAction)。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Linux が実行されているノードの場合、DSC Linux 拡張機能では、[PowerShell DSC に関するドキュメント](/powershell/scripting/dsc/getting-started/lnxgettingstarted)に示されている Linux ディストリビューションがすべてサポートされます。

### <a name="internet-connectivity"></a>インターネット接続

DSCForLinux 拡張機能では、ターゲット仮想マシンがインターネットに接続されている必要があります。 たとえば、Register 拡張機能では、Automation サービスへの接続が必要です。
その他のアクション (Pull、Pull、Install など) では、Azure Storage や GitHub への接続が必要です。 これはお客様が提供する設定によって異なります。

## <a name="extension-schema"></a>拡張機能のスキーマ

### <a name="public-configuration"></a>パブリック構成

ここでは、サポート対象のパブリック構成パラメーターをすべて紹介します。

* `FileUri`: (省略可能、文字列) MOF ファイル、メタ MOF ファイル、またはカスタム リソース zip ファイルの URI。
* `ResourceName`: (省略可能、文字列) カスタム リソース モジュールの名前。
* `ExtensionAction`: (省略可能、文字列) 拡張機能で実行されるアクションを指定します。 有効な値は Register、Push、Pull、Install、および Remove です。 指定しない場合、既定で Push アクションと見なされます。
* `NodeConfigurationName`: (省略可能、文字列) 適用するノード構成の名前。
* `RefreshFrequencyMins`: (省略可能、整数) プル サーバーからの構成の取得が DSC によって試行される頻度 (分単位) を指定します。
       プル サーバー上の構成がターゲット ノード上の現在の構成と異なる場合、その構成は保留中ストアにコピーされてから適用されます。
* `ConfigurationMode`: (省略可能、文字列) DSC によって構成が適用される方法を指定します。 有効な値は ApplyOnly、ApplyAndMonitor、および ApplyAndAutoCorrect です。
* `ConfigurationModeFrequencyMins`: (省略可能、整数) DSC によって構成が望ましい状態にされる頻度 (分単位) を指定します。

> [!NOTE]
> 2\.3 より前のバージョンを使用する場合、モード パラメーターは ExtensionAction と同じです。 モードは多義な用語であるようです。 混同を避けるため、バージョン2.3 以降では ExtensionAction が使用されます。 下位互換性を実現するために、拡張機能ではモードと ExtensionAction の両方がサポートされます。
>

### <a name="protected-configuration"></a>保護された構成

ここでは、サポート対象の保護された構成パラメーターをすべて紹介します。

* `StorageAccountName`: (省略可能、文字列) ファイルが含まれているストレージ アカウントの名前
* `StorageAccountKey`: (省略可能、文字列) ファイルが含まれているストレージ アカウントのキー
* `RegistrationUrl`: (省略可能、文字列) Azure Automation アカウントの URL
* `RegistrationKey`: (省略可能、文字列) Azure Automation アカウントのアクセス キー

## <a name="scenarios"></a>シナリオ

### <a name="register-an-azure-automation-account"></a>Azure Automation アカウントを登録する

protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

PowerShell 形式
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>(Azure ストレージ アカウント内の) MOF 構成ファイルを VM に適用する

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

PowerShell 形式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>(パブリック ストレージ内の) MOF 構成ファイルを VM に適用する

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell 形式
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>(Azure ストレージ アカウント内の) メタ MOF 構成ファイルを VM に適用する

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

PowerShell 形式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>(パブリック ストレージ内の) メタ MOF 構成ファイルを VM に適用する

public.json

```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```

PowerShell 形式

```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>カスタム リソース モジュール (Azure ストレージ アカウント内の zip ファイル) を VM にインストールする

protected.json

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

PowerShell 形式
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>カスタム リソース モジュール (パブリック ストレージ内の zip ファイル) を VM にインストールする

public.json

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}

```

PowerShell 形式

```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>VM からカスタム リソース モジュールを削除する

public.json

```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```

PowerShell 形式

```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成 (Azure Automation へのオンボードなど) が必要な仮想マシンを 1 つ以上デプロイするときに最適です。

サンプルの Resource Manager テンプレートは、[201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) と [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu) です。

Azure Resource Manager テンプレートの詳細については、[Azure Resource Manager テンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)に関するページを参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

### <a name="use-azure-cliazure-cli"></a>[Azure CLI][azure-cli] の使用

DSCForLinux 拡張機能をデプロイする前に、セクション 3 のさまざまなシナリオに応じて、`public.json` と `protected.json` を構成します。

#### <a name="classic"></a>クラシック

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

クラシック デプロイ モードは、Azure サービス管理モードとも呼ばれます。 これに切り替えるには、次を実行します。

```
$ azure config mode asm
```

次を実行して DSCForLinux 拡張機能をデプロイできます。

```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

使用可能な最新バージョンの拡張機能を確認するには、次を実行します。

```
$ azure vm extension list
```

#### <a name="resource-manager"></a>リソース マネージャー

Azure Resource Manager モードに切り替えるには、次を実行します。

```
$ azure config mode arm
```

次を実行して DSCForLinux 拡張機能をデプロイできます。

```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

> [!NOTE]
> Azure Resource Manager モードでは現在、`azure vm extension list` は使用できません。
>

### <a name="use-azure-powershellazure-powershell"></a>[Azure PowerShell][azure-powershell] の使用

#### <a name="classic"></a>クラシック

Azure サービス管理モードで Azure アカウントにサインインするには、次を実行します。

```powershell
Add-AzureAccount
```

次を実行して DSCForLinux 拡張機能をデプロイします。

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

前のセクションのさまざまなシナリオに応じて、$privateConfig と $publicConfig の内容を変更します。

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>リソース マネージャー

Azure Resource Manager モードで Azure アカウントにサインインするには、次を実行します。

```powershell
Login-AzAccount
```

Azure Resource Manager での Azure PowerShell の使用方法については、「[Azure PowerShell を使用した Azure リソースの管理](../../azure-resource-manager/management/manage-resources-powershell.md)」を参照してください。

次を実行して DSCForLinux 拡張機能をデプロイできます。

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

前のセクションのさまざまなシナリオに応じて、$privateConfig と $publicConfig の内容を変更します。

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

エラー コード:51 は、サポートされていないディストリビューションまたはサポートされていない拡張機能アクションのいずれかを表します。
場合によっては、より高いバージョンの OMI がマシンに既に存在するときに、DSC Linux 拡張機能で OMI のインストールが失敗します。 [エラー応答:(000003) ダウングレードは許可されていません]

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせてください。 または、Azure サポート インシデントを送信できます。 [Azure サポート サイト](https://azure.microsoft.com/support/options/)に移動して **[Get support]\(サポートを受ける\)** を選択します。 Azure サポートの使用方法の詳細については、「 [Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ

拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。
