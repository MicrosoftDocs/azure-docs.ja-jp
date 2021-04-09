---
title: Azure の Desired State Configuration の概要
description: PowerShell Desired State Configuration (DSC) の Microsoft Azure 拡張機能ハンドラーの使用方法について学習します。 この記事には、前提条件、アーキテクチャ、およびコマンドレットが含まれています。
services: virtual-machines
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: dcdc325633aff5ab828cb1c82f4bb2d8becee967
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560040"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Desired State Configuration 拡張機能ハンドラーの概要

Azure VM エージェントとそれに関連付けられた拡張機能は、Microsoft Azure インフラストラクチャ サービスの一部です。 VM 拡張機能は、VM の機能を拡張し、さまざまな VM の管理操作を簡略化するソフトウェア コンポーネントです。

Azure Desired State Configuration (DSC) 拡張機能の主な用途は、[Azure Automation State Configuration (DSC) サービス](../../automation/automation-dsc-overview.md)への VM のブートストラップです。
このサービスには、VM 構成の継続的な管理や、Azure Monitoring などの他の操作ツールとの統合を含む[メリット](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service)があります。
この拡張機能を使用して VM をこのサービスに登録することで、複数の Azure のサブスクリプションで機能する柔軟なソリューションが提供されます。

DSC 拡張機能を Automation DSC サービスから独立して使用することができます。
ただし、この場合、構成が VM にプッシュされるだけです。
VM 内でローカルに使用する場合を除き、継続的なレポート管理は利用できません。

この記事には、Automation のオンボード用の DSC 拡張機能を使用することと、Azure SDK を使用して VM に構成を割り当てるためのツールとして DSC 拡張機能を使用することの両方のシナリオに関する情報が含まれています。

## <a name="prerequisites"></a>前提条件

- **ローカル マシン**:Azure VM 拡張機能を利用するには、Azure portal または Azure PowerShell SDK のいずれかを使用する必要があります。
- **ゲスト エージェント**:DSC 構成で構成する Azure VM は、Windows Management Framework (WMF) 4.0 以降をサポートする OS である必要があります。 サポートされている OS バージョンの詳細な一覧については、 [DSC 拡張機能のバージョン履歴](../../automation/automation-dsc-extension-history.md)を参照してください。

## <a name="terms-and-concepts"></a>用語と概念

このガイドでは、読者が次の概念を理解していることを想定しています。

- **構成**:DSC 構成ドキュメント。
- **ノード**:DSC 構成のターゲット。 このドキュメントでは、*ノード* は常に Azure VM を指します。
- **構成データ**:構成に関する環境データが格納されている .psd1 ファイル。

## <a name="architecture"></a>Architecture

Azure DSC 拡張機能は、Azure VM エージェント フレームワークを使用して、Azure VM で実行される DSC 構成の配布、適用、およびレポート作成を行います。 DSC 拡張機能は、構成ドキュメントと一連のパラメーターを受け取ります。 ファイルが指定されていない場合、[既定の構成スクリプト](#default-configuration-script)が拡張機能に埋め込まれています。 既定の構成スクリプトは、[ローカル構成マネージャー](/powershell/scripting/dsc/managing-nodes/metaConfig)にメタデータを設定するためにのみ使用されます。

拡張機能が初めて呼び出されると、次のロジックに従って、あるバージョンの WMF がインストールされます。

- Azure VM の OS が Windows Server 2016 の場合は、処理は行われません。 Windows Server 2016 では既に PowerShell の最新バージョンがインストールされてるためです。
- **wmfVersion** プロパティを指定した場合は、そのバージョンに VM の OS と互換性がない場合を除いて、そのバージョンの WMF がインストールされます。
- **wmfVersion** プロパティを指定しなかった場合は、WMF の適用可能な最新バージョンがインストールされます。

WMF をインストールするには、再起動が必要です。 再起動後、**modulesUrl** プロパティで指定された .zip ファイルが拡張機能によってダウンロードされます (指定した場合)。 その場所が Azure Blob ストレージ内の場合は、 **sasToken** プロパティに SAS トークンを指定して、ファイルにアクセスできます。 .zip がダウンロードされて展開された後、**configurationFunction** で定義されている構成関数が実行され、.mof ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)) ファイルが生成されます。 その後、拡張機能によって、生成された .mof ファイルを使用して `Start-DscConfiguration -Force` が実行されます。 拡張機能は、この出力を取得して Azure の状態チャネルに書き込みます。

### <a name="default-configuration-script"></a>既定の構成スクリプト

Azure DSC 拡張機能には、VM を Azure Automation DSC サービスにオンボードするときに使用することを目的とした既定の構成スクリプトが含まれています。 スクリプト パラメーターは、[ローカル構成マネージャー](/powershell/scripting/dsc/managing-nodes/metaConfig)の構成可能なプロパティと合致しています。 スクリプト パラメーターについては、[Azure Resource Manager テンプレートでの Desired State Configuration 拡張機能](dsc-template.md)に関するページの[既定の構成スクリプト](dsc-template.md#default-configuration-script)に関する記事を参照してください。 完全なスクリプトについては、[GitHub の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)に関するページを参照してください。

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Azure Automation State Configuration (DSC) サービスに登録するための情報

DSC 拡張機能を使用してノードを State Configuration サービスに登録する場合、3 つの値を指定する必要があります。

- RegistrationUrl - Azure Automation アカウントの https アドレス
- RegistrationKey - ノードをサービスに登録するために使用される共有シークレット
- NodeConfigurationName - サーバー ロールを構成するためにサービスからプルするノード構成 (MOF) の名前

この情報は Azure portal で表示できますが、PowerShell を使用することもできます。

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

[ノード構成名] から、Azure State Configuration にノード構成が存在することを確認します。  存在しない場合、拡張機能のデプロイはエラーを返します。  また、構成ではなく、必ず "*ノード構成*" の名前を使用していることを確認してください。
構成は、[ノード構成 (MOF ファイル) をコンパイルする](../../automation/automation-dsc-compile.md) ために使用されるスクリプトに定義されています。
名前は常に、Configuration の後にピリオド `.` と `localhost` または特定のコンピューター名が続いたものになります。

## <a name="dsc-extension-in-resource-manager-templates"></a>Resource Manager テンプレートの DSC 拡張機能

ほとんどのシナリオでは、DSC 拡張機能を使用する場合に Resource Manager デプロイ テンプレートを利用します。 Resource Manager デプロイ テンプレートに DSC 拡張機能を含める方法の詳細と例については、[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](dsc-template.md)に関するページを参照してください。

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 拡張機能用 PowerShell コマンドレット

対話型のトラブルシューティングや情報収集のシナリオには、DSC 拡張機能を管理するために使用される PowerShell コマンドレットが最適です。 このコマンドレットを使用すると、DSC 拡張機能のデプロイをパッケージ化、発行、監視できます。 DSC 拡張機能用のコマンドレットは、[既定の構成スクリプト](#default-configuration-script)で動作するようにはまだ更新されていません。

**Publish-AzVMDscConfiguration** コマンドレットは構成ファイルを取り込み、構成ファイルをスキャンして依存 DSC リソースを探し、.zip ファイルを作成します。 .zip ファイルには、構成と、構成を適用するために必要な DSC リソースが含まれています。 コマンドレットは *-OutputArchivePath* パラメーターを使用してローカルでパッケージを作成することもできます。 それ以外の場合は、コマンドレットは Blob Storage に .zip ファイルを発行し、SAS トークンを使用して保護します。

このコマンドレットによって作成された .ps1 構成スクリプトは、アーカイブ フォルダーのルートの .zip ファイル内にあります。 モジュール フォルダーは、リソースのアーカイブ フォルダーに配置されます。

**Set-AzVMDscExtension** コマンドレットは、PowerShell DSC 拡張機能が必要とする設定を VM 構成オブジェクトに挿入します。

**Get-AzVMDscExtension** コマンドレットは、特定の VM の DSC 拡張機能の状態を取得します。

**Get-AzVMDscExtensionStatus** コマンドレットは、DSC 拡張機能ハンドラーによって適用された DSC 構成の状態を取得します。 この処理は、単一の VM または VM のグループに対して実行できます。

**Remove-AzVMDscExtension** コマンドレットは、特定の VM から拡張機能ハンドラーを削除します。 このコマンドレットによって、構成の削除、WMF のアンインストール、または VM に適用されている設定の変更が *行われることはありません*。 拡張機能ハンドラーが削除されるだけです。

Resource Manager DSC 拡張機能コマンドレットに関する重要な情報:

- Azure Resource Manager コマンドレットは動機的です。
- *ResourceGroupName*、*VMName*、*ArchiveStorageAccountName*、*Version*、および *Location* パラメーターはすべて必須です。
- *ArchiveResourceGroupName* は省略可能なパラメーターです。 このパラメーターを指定できるのは、VM が作成されたリソース グループとは別のリソース グループにストレージ アカウントが属している場合です。
- **AutoUpdate** スイッチを使用すると、最新バージョンが利用可能になったときに、拡張機能ハンドラーが自動的に更新されるようにできます。 WMF の新しいバージョンがリリースされると、このパラメーターによって VM で再起動が生じる可能性があります。

### <a name="get-started-with-cmdlets"></a>コマンドレットの概要

Azure DSC 拡張機能は DSC 構成ドキュメントを使用して、デプロイ時に Azure VM を直接構成することができます。 この手順ではノードが Automation に登録されません。 ノードは一元管理 *されません*。

単純な構成の例を次に示します。 構成を iisInstall.ps1 としてローカルに保存します。

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

次のコマンドは、指定した VM に iisInstall.ps1 スクリプトを配置します。 さらにコマンドは構成を実行し、状態を報告します。

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、DSC 拡張機能を既存の仮想マシンにデプロイすることができます。

Windows を実行する仮想マシンの場合:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Linux を実行する仮想マシンの場合:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure ポータルの機能

ポータルで DSC を設定するには、以下を実行します。

1. VM に移動します。
2. **[設定]** で **[拡張機能]** を選択します。
3. 作成される新しいページで、 **[+ 追加]** を選択し、 **[PowerShell Desired State Configuration]** を選びます。
4. 拡張機能の情報ページの下部にある **[作成]** をクリックします。

このポータルでは次の入力を収集します。

- **[Configuration Modules or Script]\(構成モジュールまたはスクリプト\)** :このフィールドは必須です (フォームは [既定の構成スクリプト](#default-configuration-script)用に更新されていません)。 構成モジュールおよびスクリプトは、構成スクリプトを含む .ps1 ファイルまたは .ps1 構成スクリプトがルートにある .zip ファイルが必要です。 .zip ファイルを使用する場合は、すべての依存リソースを .zip 内のモジュール フォルダーに含める必要があります。 Azure PowerShell SDK に含まれているコマンドレット **Publish-AzureVMDscConfiguration -OutputArchivePath** を使用して、.zip ファイルを作成することができます。 .zip ファイルはユーザーの Blob Storage にアップロードされ、SAS トークンによってセキュリティで保護されます。

- **[Module-qualified Name of Configuration]\(モジュールで修飾された構成の名前\)** :.ps1 ファイルに複数の構成関数を含めることができます。 .ps1 構成スクリプトの名前に続けて \\ と構成関数の名前を入力します。 たとえば、.ps1 スクリプトの名前が configuration.ps1 であり、構成が **IisInstall** であれば、**configuration.ps1\IisInstall** と入力します。

- **[Configuration Arguments]\(構成の引数\)** :構成関数が引数を受け取る場合は、**argumentName1=value1,argumentName2=value2** という形式でここに入力します。 この形式は、PowerShell コマンドレットまたは Resource Manager テンプレートで構成引数を受け取る方法とは異なる形式であることに注意してください。

- **[Configuration Data PSD1 File]\(構成データの PSD1 ファイル\)** :構成に `.psd1` の構成データ ファイルが必要な場合、このフィールドを使用してデータ ファイルを選択し、ユーザーの BLOB ストレージにアップロードします。 構成データ ファイルは、Blob Storage 内の SAS トークンによってセキュリティで保護されます。

- **[WMF Version]\(WMF のバージョン\)** :VM にインストールする Windows Management Framework (WMF) のバージョンを指定します。 このプロパティを latest に設定すると、WMF の最新バージョンがインストールされます。 現在、このプロパティに設定できる値は、4.0、5.0、5.1、latest のみです。 これらの設定できる値は更新される可能性があります。 既定値は **latest** です。

- **[Data Collection]\(データ収集\)** :拡張機能でテレメトリを収集するかどうかを決定します。 詳しくは、「[Azure DSC extension data collection (Azure DSC 拡張機能のデータ収集)](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)」をご覧ください。

- **バージョン**:インストールする DSC 拡張機能のバージョンを指定します。 バージョンの詳細については、[DSC 拡張機能のバージョン履歴](/powershell/scripting/dsc/getting-started/azuredscexthistory)に関するページを参照してください。

- **[自動アップグレードのマイナー バージョン]** :このフィールドは、コマンドレットの **AutoUpdate** スイッチにマップされ、インストール時に拡張機能を最新バージョンに自動的に更新できます。 **[はい]** の場合、利用可能な最新バージョンを使用するように拡張機能ハンドラーに指示します。 **[いいえ]** の場合は、指定された **バージョン** が強制的にインストールされます。 **[はい]** と **[いいえ]** のいずれも選択しないことは、 **[いいえ]** を選択することと同じです。

## <a name="logs"></a>ログ

拡張機能のログは、次の場所に格納されます。`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>次のステップ

- PowerShell DSC の詳細については、[PowerShell ドキュメント センター](/powershell/scripting/dsc/overview/overview)を参照してください。
- [DSC 拡張機能用の Resource Manager テンプレート](dsc-template.md)を確認します。
- PowerShell DSC を使用して管理できる機能とその他の DSC のリソースについては、[PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)を参照してください。
- 機密パラメーターを構成に渡す方法の詳細については、[DSC 拡張機能ハンドラーで資格情報を安全に管理する方法](dsc-credentials.md)に関するページを参照してください。
