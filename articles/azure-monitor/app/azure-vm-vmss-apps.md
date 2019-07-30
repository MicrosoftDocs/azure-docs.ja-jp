---
title: Azure VM および Azure 仮想マシン スケール セットでホストされているアプリケーション パフォーマンスを監視する | Microsoft Docs
description: Azure VM および Azure 仮想マシン スケール セットに対するアプリケーション パフォーマンス監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: 69ae6ab6caedd9e89d277b92558931685d2ea320
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877417"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Azure VM および Azure 仮想マシン スケール セットでホストされているアプリケーション パフォーマンスを監視する

[Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) および [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) 上で実行されている .NET ベースの Web アプリケーションに対する監視を有効にすることが従来より簡単になりました。 コードを変更することなく、Application Insights を使用する利点のすべてが得られます。

この記事では、ApplicationMonitoringWindows 拡張機能を使用した Application Insights 監視の有効化について説明した後、大規模なデプロイのプロセスを自動化するための事前ガイダンスを提供します。

> [!IMPORTANT]
> Azure ApplicationMonitoringWindows 拡張機能は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure VM および Azure 仮想マシン スケール セットでホストされたアプリケーションに対するアプリケーション監視を有効にする方法には、次の 2 つがあります。

* **エージェント ベースのアプリケーション監視** (ApplicationMonitoringWindows 拡張機能)。
    * 有効にするにはこの方法が最も簡単であり、高度な構成は不要です。 多くの場合、これは "ランタイム" 監視と呼ばれます。 Azure VM および Azure 仮想マシン スケール セットの場合は、少なくともこのレベルの監視を有効にすることをお勧めします。 その後、特定のシナリオに基づいて、手動のインストルメンテーションが必要かどうかを評価できます。
    * 現在は、.Net IIS でホストされたアプリケーションのみがサポートされています。

* Application Insights SDK をインストールし、コードを介して**手動でアプリケーションをインストルメント化する方法**。

    * このアプローチはカスタマイズできる部分がはるかに多いのですが、[Application Insights SDK NuGet パッケージへの依存関係を追加](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)する必要があります。 また、この方法では、最新バージョンのパッケージへの更新を自分で管理する必要があります。

    * エージェントベースの監視の既定ではキャプチャされないイベント/依存関係を追跡するためにカスタム API 呼び出しを行う必要がある場合は、この方法を使用する必要があります。 詳細については、[カスタムのイベントとメトリックのための API に関する記事](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)を参照してください。

> [!NOTE]
> エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 このチェックアウトの詳細については、以下の「[トラブルシューティング](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-apps#troubleshooting)」セクションを参照してください。

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>PowerShell を使用して VM 上の .NET アプリケーションに対するエージェント ベースの監視を管理する

VM に対するアプリケーション監視拡張機能をインストールまたは更新する
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

VM からアプリケーション監視拡張機能をアンインストールする
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

VM に対するアプリケーション監視拡張機能の状態にクエリを実行する
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

VM に対してインストールされている拡張機能の一覧を取得する
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>PowerShell を使用して Azure 仮想マシン スケール セット上の .NET アプリケーションに対するエージェント ベースの監視を管理する

Azure 仮想マシン スケール セットに対するアプリケーション監視拡張機能をインストールまたは更新する
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 仮想マシン スケール セットからアプリケーション監視拡張機能をアンインストールする
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 仮想マシン スケール セットに対するアプリケーション監視拡張機能の状態にクエリを実行する
```powershell
# Not supported by extensions framework
```

Azure 仮想マシン スケール セットに対してインストールされている拡張機能の一覧を取得する
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>トラブルシューティング

Azure VM および Azure 仮想マシン スケール セット上で実行されている .NET アプリケーションに対する拡張機能ベースの監視のステップ バイ ステップのトラブルシューティング ガイドを次に示します。

> [!NOTE]
> .NET Core、Java、および Node.js アプリケーションは、手動の SDK ベースのインストルメンテーションを通して Azure VM および Azure 仮想マシン スケール セット上でのみサポートされているため、下の手順はこれらのシナリオには適用されせん。

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>次の手順
* [Azure 仮想マシン スケール セットにアプリケーションをデプロイする](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)方法について学習します。
* エンドポイントがダウンしている場合に警告を受信するようにするには、[可用性 Web テストを設定](monitor-web-app-availability.md)します。
