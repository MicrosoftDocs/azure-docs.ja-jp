---
title: "Azure の Desired State Configuration の概要 | Microsoft Docs"
description: "PowerShell Desired State Configuration の Microsoft Azure 拡張機能ハンドラーの使用に関する概要。 前提条件、アーキテクチャ、コマンドレットなどについて説明します。"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3749d5aac5da83b43d5c6d6b7bd971eac7f07548
ms.lasthandoff: 03/10/2017


---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Desired State Configuration 拡張機能ハンドラーの概要
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure VM エージェントとそれに関連付けられた拡張機能は、Microsoft Azure インフラストラクチャ サービスの一部です。 VM 拡張機能は、VM の機能を拡張し、さまざまな VM の管理操作を簡略化するソフトウェア コンポーネントです。 たとえば、VMAccess 拡張機能を使用すると、管理者のパスワードをリセットできます。また、カスタム スクリプト拡張機能を使用すると、VM 上でスクリプトを実行できます。

この記事では、Azure PowerShell SDK に含まれる、Azure VM 用 PowerShell Desired State Configuration (DSC) 拡張機能について紹介します。 PowerShell DSC 拡張機能が有効になっている Azure VM で、新しいコマンドレットを使用して、PowerShell DSC 構成をアップロードして適用することができます。 PowerShell DSC 拡張機能は、PowerShell DSC を呼び出して、受け取った DSC 構成を VM に適用します。 この機能は、Azure ポータルを通して使用することもできます。

## <a name="prerequisites"></a>前提条件
**ローカル コンピューター** : Azure VM 拡張機能と対話するには、Azure Portal または Azure PowerShell SDK のいずれかを使用する必要があります。 

**ゲスト エージェント**: DSC 構成で構成する Azure VM は、Windows Management Framework (WMF) 4.0 または 5.0 をサポートする OS である必要があります。 サポートされている OS バージョンの詳細な一覧については、 [DSC 拡張機能のバージョン履歴](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)を参照してください。

## <a name="terms-and-concepts"></a>用語と概念
このガイドでは、読者が次の概念を理解していることを想定しています。

構成 - DSC 構成ドキュメント。 

ノード - DSC 構成のターゲット。 このドキュメントでは、"ノード" は常に Azure VM を指します。

構成データ - 構成に関する環境データが格納されている .psd1 ファイル。

## <a name="architectural-overview"></a>アーキテクチャの概要
Azure DSC 拡張機能は、Azure VM エージェント フレームワークを使用して、Azure VM で実行される DSC 構成の配布、適用、およびレポート作成を行います。 DSC 拡張機能では、1 つ以上の構成ドキュメントを含む .zip ファイルと、Azure PowerShell SDK または Azure ポータルで提供される一連のパラメーターが必要です。

拡張機能は、初めて呼び出されたときに、インストール プロセスを実行します。 このプロセスによって、次のロジックに従ってあるバージョンの Windows Management Framework (WMF) がインストールされます。

1. Azure VM の OS が Windows Server 2016 の場合は、処理は行われません。 Windows Server 2016 では既に PowerShell の最新バージョンがインストールされてるためです。
2. `wmfVersion` プロパティを指定した場合は、VM の OS と互換性がない場合を除いて、そのバージョンの WMF がインストールされます。
3. `wmfVersion` プロパティを指定しなかった場合は、WMF の適用可能な最新バージョンがインストールされます。

WMF をインストールした場合は再起動が必要になります。 再起動後、 `modulesUrl` プロパティで指定された .zip ファイルが拡張機能によってダウンロードされます。 その場所が Azure BLOB ストレージ内の場合は、 `sasToken` プロパティに SAS トークンを指定すると、ファイルにアクセスできます。 .zip がダウンロードされて展開された後、 `configurationFunction` で定義されている構成関数が実行され、MOF ファイルが生成されます。 その後、拡張機能によって、生成された MOF ファイルに対して `Start-DscConfiguration -Force` が実行されます。 拡張機能は、この出力を取得して Azure の状態チャネルに書き込みます。 これ以降は、DSC LCM が通常どおり監視と修正に対処します。 

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット
Azure Resource Manager またはクラシック デプロイメント モデルと共に PowerShell コマンドレットを使用すると、DSC 拡張機能のデプロイをパッケージ化、発行、監視できます。 以下に示すコマンドレットはクラシック デプロイメント モジュールですが、"Azure" を "AzureRm" に置き換えると Azure Resource Manager モデルを使用できます。 たとえば、`Publish-AzureVMDscConfiguration` ではクラシック デプロイメント モデルが使用されますが、`Publish-AzureRmVMDscConfiguration` では Azure Resource Manager が使用されます。 

`Publish-AzureVMDscConfiguration` は、構成ファイルを取り込み、依存する DSC リソースがあるかどうかを調べ、構成とその適用に必要な DSC リソースが含まれる .zip ファイルを作成します。 また、 `-ConfigurationArchivePath` パラメーターを使用して、パッケージをローカルに作成することもできます。 それ以外の場合は、Azure Blob Storage に .zip ファイルを発行し、SAS トークンを使用して保護します。

このコマンドレットによって作成された .zip ファイルでは、アーカイブ フォルダーのルートに .ps1 構成スクリプトがあります。 リソースのモジュール フォルダーは、アーカイブ フォルダーに配置されます。 

`Set-AzureVMDscExtension` は、PowerShell DSC 拡張機能に必要な設定を VM 構成オブジェクトに挿入します。 クラシック デプロイメント モデルでは、`Update-AzureVM` を使用して VM の変更を Azure VM に適用する必要があります。 

`Get-AzureVMDscExtension` は、特定の VM の DSC 拡張機能の状態を取得します。 

`Get-AzureVMDscExtensionStatus` は、DSC 拡張機能ハンドラーによって適用された DSC 構成の状態を取得します。 この処理は、単一の VM または VM のグループに対して実行できます。

`Remove-AzureVMDscExtension` は、特定の仮想マシンから拡張機能ハンドラーを削除します。 このコマンドレットによって、構成の削除、WMF のアンインストール、または仮想マシンに適用されている設定の変更が **行われることはありません** 。 拡張機能ハンドラーが削除されるだけです。 

**ASM コマンドレットと Azure Resource Manager コマンドレットの主な違い**

* Azure Resource Manager コマンドレットは動機的です。 ASM コマンドレットは非同期的です。
* ResourceGroupName、VMName、ArchiveStorageAccountName、Version、および Location はすべて、Azure Resource Manager の必須パラメーターです。
* ArchiveResourceGroupName は、Azure Resource Manager の新しい省略可能なパラメーターです。 このパラメーターを指定できるのは、仮想マシンが作成されたリソース グループとは別のリソース グループにストレージ アカウントが属している場合です。
* ConfigurationArchive は、Azure Resource Manager では ArchiveBlobName と呼ばれます。
* ContainerName は、Azure Resource Manager では ArchiveContainerName と呼ばれます。
* StorageEndpointSuffix は、Azure Resource Manager では ArchiveStorageEndpointSuffix と呼ばれます。
* AutoUpdate スイッチが Azure Resource Manager に追加されました。これにより、最新バージョンが利用可能になると、拡張機能ハンドラーが自動的に更新されるようにできます。 WMF の新しいバージョンがリリースされると、このパラメーターによって VM で再起動が生じる可能性があることに注意してください。 

## <a name="azure-portal-functionality"></a>Azure ポータルの機能
VM に移動します。 [設定]、[全般] の順に移動し、[拡張機能] をクリックします。 新しいウィンドウが作成されます。 [追加] をクリックし、[PowerShell DSC] を選択します。

ポータルでは、入力が必要になります。
**[Configuration Modules or Script (構成モジュールまたはスクリプト)]**: このフィールドは必須です。 構成スクリプトを含む .ps1 ファイルか、.ps1 構成スクリプトがルートに含まれ、すべての依存リソースがモジュール フォルダーに含まれる .zip ファイルが必要です。 これは、Azure PowerShell SDK に含まれている `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` コマンドレットを使用して作成できます。 zip ファイルは、SAS トークンによってセキュリティで保護された、ユーザーの Blob Storage にアップロードされます。 

**[Configuration Data PSD1 File (構成データ PSD1 ファイル)]**: このフィールドはオプションです。 .psd1 の構成データ ファイルが必要な構成では、このフィールドを使用して目的のファイルを選択し、SAS トークンによってセキュリティで保護されている、ユーザーの Blob Storage にアップロードします。 

**[構成のモジュール修飾名]**: .ps1 ファイルには、複数の構成関数を含めることができます。 .ps1 構成スクリプトの名前に続けて '\' と構成関数の名前を入力します。 たとえば、.ps1 スクリプトの名前が "configuration.ps1" であり、構成が "IisInstall" であれば、次のように入力します: `configuration.ps1\IisInstall`

**[構成引数]**: 構成関数が引数を受け取る場合は、`argumentName1=value1,argumentName2=value2` という形式でここに入力します。 この形式は、PowerShell コマンドレットまたは Resource Manager テンプレートで構成引数を受け取る方法とは異なる形式であることに注意してください。 

## <a name="getting-started"></a>使用の開始
Azure DSC 拡張機能は、DSC 構成ドキュメントを取り込み、それを Azure VM 上で適用します。 構成の簡単な例を次に示します。 これを "IisInstall.ps1" という名前でローカルに保存してください。

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

次の手順では、指定した VM に IisInstall.ps1 スクリプトを配置し、構成を実行した後、その状態に関するレポートを返します。
###<a name="classic-model"></a>クラシック モデル
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Azure Resource Manager モデル

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>ログの記録
ログは次の場所に記録されます。

C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[バージョン番号]

## <a name="next-steps"></a>次のステップ
PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 

[DSC 拡張機能用の Azure Resource Manager テンプレート](virtual-machines-windows-extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。 

PowerShell DSC で管理できる追加機能については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) で DSC リソースを検索してください。

機微なパラメーターを構成に渡す方法について詳しくは、[資格情報を Azure DSC 拡張機能ハンドラーに渡す方法](virtual-machines-windows-extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。


