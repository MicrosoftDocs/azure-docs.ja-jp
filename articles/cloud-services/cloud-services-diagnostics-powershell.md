---
title: PowerShell を使用した Azure Cloud Services での診断の有効化 | Microsoft Docs
description: PowerShell を使用して Cloud Services の診断を有効にする方法について説明します。
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: 4beed4dd874c23c36e125b5855e2e8380859ef83
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359167"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>PowerShell を使用した Azure Cloud Services での診断の有効化
Azure Diagnostics 拡張機能を使用して、クラウド サービスからアプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。 この記事では、PowerShell を使用して Cloud Service の Azure Diagnostics 拡張機能を有効にする方法について説明します。  この記事で求められる前提条件については、 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) に関するページを参照してください。

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Cloud Service のデプロイの一環としての診断拡張機能の有効化
この方法は、クラウド サービスのデプロイの一環として診断拡張機能を有効にすることができる継続的インテグレーション型のシナリオに適用できます。 新しい Cloud Service のデプロイを作成するときに、*ExtensionConfiguration* パラメーターを [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) コマンドレットに渡して診断拡張機能を有効にすることができます。 *ExtensionConfiguration* パラメーターは、 [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) コマンドレットを使用して作成できるさまざまな診断構成を受け取ります。

次の例は、診断構成がそれぞれ異なる WebRole と WorkerRole を含むクラウド サービスの診断を有効にする方法を示しています。

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

診断構成ファイルでストレージ アカウント名を設定した `StorageAccount` 要素を指定すると、そのストレージ アカウントが `New-AzureServiceDiagnosticsExtensionConfig` コマンドレットによって自動的に使用されます。 これを機能させるには、ストレージ アカウントが、デプロイしているクラウド サービスと同じサブスクリプションに属している必要があります。

Azure SDK 2.6 からは、MSBuild の発行先への出力によって生成された拡張機能の構成ファイルに、サービス構成ファイル (.cscfg) で指定された診断の構成文字列に基づいて、ストレージ アカウント名が含まれるようになります。 次のスクリプトは、発行先に出力された拡張機能の構成ファイルを解析し、クラウド サービスのデプロイ時に各ロールの診断拡張機能を構成する方法を示しています。

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online では、Cloud Services の自動デプロイに診断拡張機能と同様の方法が使用されます。 完全な例については、「 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) 」を参照してください。

診断構成で `StorageAccount` を指定しなかった場合は、*StorageAccountName* パラメーターをコマンドレットに渡す必要があります。 *StorageAccountName* パラメーターを指定すると、診断構成ファイルに指定されたストレージ アカウントではなく、このパラメーターに指定されたストレージ アカウントがコマンドレットで常に使用されます。

診断ストレージ アカウントがクラウド サービスと異なるサブスクリプションに属している場合は、*StorageAccountName* パラメーターと *StorageAccountKey* パラメーターをコマンドレットに明示的に渡す必要があります。 診断ストレージ アカウントが同じサブスクリプションに属している場合、診断拡張機能を有効にすると、コマンドレットによってキー値が自動的に照会され、設定されるので、 *StorageAccountKey* パラメーターは不要です。 ただし、診断ストレージ アカウントが別のサブスクリプションに属している場合は、コマンドレットでキーを自動的に取得できないので、 *StorageAccountKey* パラメーターでキーを明示的に指定する必要があります。

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>既存の Cloud Service での診断拡張機能の有効化
[Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) コマンドレットを使用して、既に実行されているクラウド サービスで診断構成を有効にしたり、更新したりできます。

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>診断拡張機能の現在の構成の取得
Cloud Service の現在の診断構成を取得するには、 [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) コマンドレットを使用します。

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>診断拡張機能の削除
Cloud Service で診断を無効にするには、[Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) コマンドレットを使用します。

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

*Role* パラメーターを指定せずに *Set-AzureServiceDiagnosticsExtension* または *New-AzureServiceDiagnosticsExtensionConfig* を使用して診断拡張機能を有効にした場合は、*Role* パラメーターを指定せずに *Remove-AzureServiceDiagnosticsExtension* を使用して拡張機能を削除できます。 拡張機能を有効にするときに *Role* パラメーターを使用した場合は、拡張機能を削除するときにもこのパラメーターを使用する必要があります。

個々のロールから診断拡張機能を削除するには、次のコマンドを実行します。

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>次の手順
* Azure Diagnostics と他の手法を使用した問題のトラブルシューティングに関するその他のガイダンスについては、「 [Azure Cloud Services および Virtual Machines の診断機能](cloud-services-dotnet-diagnostics.md)」を参照してください。
* [診断構成スキーマ](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) に関するページでは、診断拡張機能の各種 xml 構成オプションについて説明しています。
* Virtual Machines の診断拡張機能を有効にする方法については、「 [Create a Windows Virtual machine with monitoring and diagnostics using Azure Resource Manager Template (Azure リソース マネージャー テンプレートを使用した監視および診断機能を備えた Windows 仮想マシンの作成)](../virtual-machines/windows/extensions-diagnostics-template.md)
