---
title: Linux 用の Azure DSC 拡張機能
description: Desired State Configuration を使用して Azure Linux VM を構成できるよう、OMI パッケージと DSC パッケージをインストールします。
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 67f72c5b396bc935f7bec34bc8a52f63131649b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904476"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux 用の DSC 拡張機能 (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) は、コードで構成を行って IT および開発インフラストラクチャを管理できる管理プラットフォームです。

DSCForLinux 拡張機能は Microsoft によって公開され、サポートされています。 拡張機能では、OMI および DSC のエージェントが Azure 仮想マシンにインストールされます。 DSC 拡張機能では、さらに次のアクションを実行できます


- Azure Automation サービスから構成をプルするために、Linux VM を Azure Automation アカウントに登録する (Register ExtensionAction)
- MOF 構成を Linux VM にプッシュする (Push ExtensionAction)
- ノード構成をプルするために、メタ MOF 構成を Linux VM に適用してプル サーバーを構成する (Pull ExtensionAction)
- カスタム DSC モジュールを Linux VM にインストールする (Install ExtensionAction)
- カスタム DSC モジュールを Linux VM から削除する (Remove ExtensionAction)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

DSC Linux 拡張機能では、以下を除き、[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)がすべてサポートされます。

| ディストリビューション | バージョン |
|---|---|
| Debian | すべてのバージョン |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>インターネット接続

DSCForLinux 拡張機能では、ターゲット仮想マシンがインターネットに接続されている必要があります。 たとえば、Register 拡張機能では、Automation サービスへの接続が必要です。 その他のアクション (Pull、Pull、Install など) では、Azure ストレージ/GitHub への接続が必要です。 これはお客様の設定によって異なります。

## <a name="extension-schema"></a>拡張機能のスキーマ

### <a name="11-public-configuration"></a>1.1 パブリック構成

ここでは、サポート対象のパブリック構成パラメーターをすべて紹介します。

* `FileUri`: (省略可能、文字列) MOF ファイル/メタ MOF ファイル/カスタム リソース ZIP ファイルの URI。
* `ResourceName`: (省略可能、文字列) カスタム リソース モジュールの名前
* `ExtensionAction`: (省略可能、文字列) 拡張機能で実行されるアクションを指定します。 有効な値は次のとおりです。Register、Push、Pull、Install、Remove。 これは、指定されていない場合に既定で Push アクションと見なされます。
* `NodeConfigurationName`: (省略可能、文字列) 適用するノード構成の名前。
* `RefreshFrequencyMins`: (省略可能、整数) プル サーバーからの構成の取得が DSC によって試行される頻度 (分単位) を指定します。 
       プル サーバー上の構成がターゲット ノード上の現在の構成と異なる場合、その構成は保留中ストアにコピーされてから適用されます。
* `ConfigurationMode`: (省略可能、文字列) DSC によって構成が適用される方法を指定します。 有効な値は次のとおりです。ApplyOnly、ApplyAndMonitor、ApplyAndAutoCorrect。
* `ConfigurationModeFrequencyMins`: (省略可能、整数) DSC によって構成が望ましい状態にされる頻度 (分単位) を指定します。

> [!NOTE]
> 2.3 未満のバージョンを使用している場合、モード パラメーターは ExtensionAction と同じです。 モードは多義な用語であるようです。 そのため、混乱を避ける目的で、バージョン 2.3 から ExtensionAction が使用されています。 下位互換性を実現するために、拡張機能ではモードと ExtensionAction の両方がサポートされます。 
>

### <a name="12-protected-configuration"></a>1.2 保護された構成

ここでは、サポート対象の保護された構成パラメーターをすべて紹介します。

* `StorageAccountName`: (省略可能、文字列) ファイルが含まれているストレージ アカウントの名前
* `StorageAccountKey`: (省略可能、文字列) ファイルが含まれているストレージ アカウントのキー
* `RegistrationUrl`: (省略可能、文字列) Azure Automation アカウントの URL
* `RegistrationKey`: (省略可能、文字列) Azure Automation アカウントのアクセス キー


## <a name="scenarios"></a>シナリオ

### <a name="register-to-azure-automation-account"></a>Azure Automation アカウントに登録する
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>(Azure ストレージ アカウント内の) MOF 構成ファイルを VM に適用する

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>(パブリック ストレージ内の) MOF 構成ファイルを VM に適用する

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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>(Azure ストレージ アカウント内の) メタ MOF 構成ファイルを VM に適用する

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

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>カスタム リソース モジュール (Azure ストレージ アカウント内の ZIP ファイル) を VM にインストールする
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>カスタム リソース モジュール (パブリック ストレージ内の ZIP ファイル) を VM にインストールする
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

Azure Resource Manager テンプレートの詳細については、[Azure Resource Manager テンプレートの作成](../../azure-resource-manager/resource-group-authoring-templates.md)に関するページを参照してください。


## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

### <a name="21-using-azure-cliazure-cli"></a>2.1. **[Azure CLI]**[azure-cli の使用
DSCForLinux 拡張機能をデプロイする前に、セクション 3. のさまざまなシナリオに応じて、`public.json` と `protected.json` を構成する必要があります。

#### <a name="211-classic"></a>2.1.1. クラシック
クラシック モードは、Azure サービス管理モードとも呼ばれます。 これに切り替えるには、次を実行します。
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

#### <a name="212-resource-manager"></a>2.1.2. リソース マネージャー
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
> Azure Resource Manager モードでは現在、`azure vm extension list` を使用できません。
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. **[Azure PowerShell]**[azure-powershell の使用

#### <a name="221-classic"></a>2.2.1. クラシック

Azure アカウント (Azure サービス管理モード) にログインするには、次を実行します。

```powershell>
Add-AzureAccount
```

次を実行して DSCForLinux 拡張機能をデプロイします。

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

上記のセクションのさまざまなシナリオに応じて、$privateConfig と $publicConfig の内容を変更する必要があります 
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

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2. Resource Manager

Azure アカウント (Azure Resource Manager モード) にログインするには、次を実行します。

```powershell>
Login-AzAccount
```

Azure Resource Manager で Azure PowerShell を使用する方法について詳しく確認するには、[**こちら**](../../azure-resource-manager/manage-resources-powershell.md)をクリックしてください。

次を実行して DSCForLinux 拡張機能をデプロイできます。

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

上記のセクションのさまざまなシナリオに応じて、$privateConfig と $publicConfig の内容を変更する必要があります 
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

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

エラー コード:51 は、サポートされていないディストリビューションまたはサポートされていない拡張機能アクションのいずれかを表します。
場合によっては、既に高いバージョンの OMI がマシン内に存在するときに、DSC Linux 拡張機能で OMI のインストールが失敗します  [エラー応答:(000003) ダウングレードは許可されていません]



### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。
