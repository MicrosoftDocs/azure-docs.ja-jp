---
title: Azure PowerShell を使用して Windows VM で診断を有効にする
services: virtual-machines-windows
documentationcenter: ''
description: PowerShell を使用して、Windows を実行している仮想マシンで Azure Diagnostics を有効にする方法について説明します
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 61b94e95c5292b4013409deed6565a90890b66d1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892636"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>PowerShell を使用して Windows を実行している仮想マシンで Azure Diagnostics を有効にする

Azure Diagnostics は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 診断拡張機能を使用して、Windows を実行している Azure 仮想マシン (VM) から、アプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Resource Manager デプロイ モデルを使用している場合の診断拡張機能の有効化
Azure Resource Manager デプロイ モデルを使用して Windows VM を作成するときに、リソース マネージャー テンプレートに拡張機能構成を追加することで、診断拡張機能を有効にすることができます。 「[Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](diagnostics-template.md)」をご覧ください。

Resource Manager デプロイ モデルを使用して作成された既存の VM で診断拡張機能を有効にするには、下に示すように、[Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell コマンドレットを使用できます。

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* は、後の[サンプル](#sample-diagnostics-configuration)で説明するように、XML での診断構成が含まれているファイルへのパスです。  

診断構成ファイルでストレージ アカウント名を含む **StorageAccount** 要素が指定されている場合は、*Set-AzVMDiagnosticsExtension* スクリプトによって、そのストレージ アカウントに診断データを送信するように診断拡張機能が自動的に設定されます。 これを機能させるには、ストレージ アカウントが VM と同じサブスクリプションに属している必要があります。

診断構成で **StorageAccount** を指定しなかった場合は、 *StorageAccountName* パラメーターをコマンドレットに渡す必要があります。 *StorageAccountName* パラメーターを指定すると、診断構成ファイルに指定されたストレージ アカウントではなく、このパラメーターに指定されたストレージ アカウントがコマンドレットで常に使用されます。

診断ストレージ アカウントが VM と異なるサブスクリプションに属している場合は、*StorageAccountName* パラメーターと *StorageAccountKey* パラメーターをコマンドレットに明示的に渡す必要があります。 診断ストレージ アカウントが同じサブスクリプションに属している場合、診断拡張機能を有効にすると、コマンドレットによってキー値が自動的に照会され、設定されるので、 *StorageAccountKey* パラメーターは不要です。 ただし、診断ストレージ アカウントが別のサブスクリプションに属している場合は、コマンドレットでキーを自動的に取得できないので、 *StorageAccountKey* パラメーターでキーを明示的に指定する必要があります。  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

VM で診断拡張機能が有効になったら、[Get-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) コマンドレットを使用して現在の設定を取得できます。

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

コマンドレットから返される *PublicSettings* には診断構成が含まれます。 サポートされている構成は WadCfg と xmlCfg の 2 種類です。 WadCfg は JSON 構成で、xmlCfg は Base64 でエンコードされた形式の XML 構成です。 XML を読み取るには、デコードする必要があります。

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) コマンドレットを使用すると、VM から診断拡張機能を削除できます。  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用している場合の診断拡張機能の有効化
クラシック デプロイ モデルを使用して作成した VM で診断拡張機能を有効にするには、 [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) コマンドレットを使用します。 次の例は、診断拡張機能を有効にしたクラシック デプロイ モデルを使用して、新しい VM を作成する方法を示しています。

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

クラシック デプロイ モデルを使用して作成された既存の VM で診断拡張機能を有効にするには、まず、 [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) コマンドレットを使用して VM 構成を取得します。 次に、 [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) コマンドレットを使用して、診断拡張機能を含めるように VM 構成を更新します。 最後に、 [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm)を使用して、更新された構成を VM に適用します。

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>サンプルの診断構成
上記のスクリプトを使用した診断パブリック構成には、以下の XML を使用できます。 このサンプル構成では、各種パフォーマンス カウンターが診断ストレージ アカウントに転送されます。また、Windows イベント ログのアプリケーション、セキュリティ、およびシステム チャネルのエラーと、診断インフラストラクチャ ログのエラーも転送されます。

以下を含めるように構成を更新する必要があります。

* *Metrics* 要素の **resourceID** 属性を VM のリソース ID で更新する必要があります。
  
  * リソース ID は、"/subscriptions/{*VM が実行されているサブスクリプションのサブスクリプション ID*}/resourceGroups/{*VM のリソース グループ名*}/providers/Microsoft.Compute/virtualMachines/{*VM 名*}" のパターンを使用して作成できます。
  * たとえば、VM が実行されているサブスクリプションのサブスクリプション ID が **11111111-1111-1111-1111-111111111111**、リソース グループのリソース グループ名が **MyResourceGroup**、VM 名が **MyWindowsVM** の場合、*resourceID* の値は次のようになります。
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * パフォーマンス カウンターとメトリックの構成に基づいてメトリックが生成されるしくみの詳細については、「 [ストレージの Azure Diagnostics メトリック テーブル](diagnostics-template.md#wadmetrics-tables-in-storage)」をご覧ください。
* 診断ストレージ アカウントの名前を使用して、 **StorageAccount** 要素を更新する必要があります。
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>次の手順
* Azure Diagnostics 機能と他の手法を使用した問題のトラブルシューティングに関するその他のガイダンスについては、「 [Azure Cloud Services および Virtual Machines での診断の有効化](../../cloud-services/cloud-services-dotnet-diagnostics.md)」をご覧ください。
* [Azure 診断構成スキーマ](https://msdn.microsoft.com/library/azure/mt634524.aspx) 」では、診断拡張機能の各種 XML 構成オプションについて説明しています。

