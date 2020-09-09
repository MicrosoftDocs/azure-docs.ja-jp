---
title: Azure VM のパフォーマンスを監視する - Azure Application Insights
description: Azure VM および Azure 仮想マシン スケール セットに対するアプリケーション パフォーマンス監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 8b025c5196d65234a632bd1f939bc1116b72dce0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024634"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure 仮想マシンと Azure 仮想マシン スケール セットに Azure Monitor Application Insights エージェントをデプロイする

[Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)と [Azure 仮想マシン スケール セット](../../virtual-machine-scale-sets/index.yml)上で実行されている .NET ベースの Web アプリケーションに対する監視を有効にすることが、従来より簡単になりました。 コードを変更することなく、Application Insights を使用する利点のすべてが得られます。

この記事では、Application Insights エージェントを使用した Application Insights 監視の有効化について説明した後、大規模なデプロイのプロセスを自動化するための事前ガイダンスを提供します。

> [!IMPORTANT]
> .NET 用の Azure Application Insights エージェントは、現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure 仮想マシンと Azure 仮想マシン スケール セットでホストされるアプリケーションに対するアプリケーション監視を有効にする方法には、次の 2 つがあります。

* **コード不要** (Application Insights エージェントを使用)
    * 有効にするにはこの方法が最も簡単であり、高度な構成は不要です。 多くの場合、これは "ランタイム" 監視と呼ばれます。

    * Azure 仮想マシンと Azure 仮想マシン スケール セットの場合は、少なくともこのレベルの監視を有効にすることをお勧めします。 その後、特定のシナリオに基づいて、手動のインストルメンテーションが必要かどうかを評価できます。

    * Application Insights エージェントでは、.NET SDK と同じ依存関係のシグナルを既定で自動的に収集します。 詳細については、「[依存関係の自動収集](./auto-collect-dependencies.md#net)」を参照してください。
        > [!NOTE]
        > 現在は、.Net IIS でホストされたアプリケーションのみがサポートされています。 SDK を使用して、Azure 仮想マシンと仮想マシン スケール セット上でホストされている ASP.NET Core、Java、Node.js アプリケーションをインストルメント化します。

* **コードベース** (SDK を使用)

    * このアプローチはカスタマイズできる部分がはるかに多いのですが、[Application Insights SDK NuGet パッケージへの依存関係を追加](./asp-net.md)する必要があります。 また、この方法では、最新バージョンのパッケージへの更新を自分で管理する必要があります。

    * エージェントベースの監視の既定ではキャプチャされないイベント/依存関係を追跡するためにカスタム API 呼び出しを行う必要がある場合は、この方法を使用する必要があります。 詳細については、[カスタムのイベントとメトリックのための API に関する記事](./api-custom-events-metrics.md)を参照してください。

> [!NOTE]
> エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 このチェックアウトの詳細については、以下の「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell を使用して、Azure 仮想マシン上で .NET アプリケーションの Application Insights エージェントを管理する

> [!NOTE]
> Application Insights エージェントをインストールする前に、接続文字列が必要になります。 [新しい Application Insights リソースを作成する](./create-new-resource.md)か、既存の Application Insights リソースから接続文字列をコピーします。

> [!NOTE]
> PowerShell の新機能については、 [使用開始ガイド](/powershell/azure/get-started-azureps?view=azps-2.5.0)を確認してください。

Application Insights エージェントを Azure 仮想マシンの拡張機能としてインストールまたは更新する
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Application Insights エージェントは、PowerShell ループを使用して、複数の仮想マシンに大規模にまたがる拡張機能としてインストールまたは更新することができます。

Azure 仮想マシンから Application Insights エージェント拡張機能をアンインストールする
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 仮想マシンに対する Application Insights エージェント拡張機能の状態にクエリを実行する
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Azure 仮想マシンに対してインストールされている拡張機能の一覧を取得する
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
また、ポータルの [Azure 仮想マシン ブレード](../../virtual-machines/extensions/overview.md)で、インストールされている拡張機能を表示することもできます。

> [!NOTE]
> インストールを確認するには、Application Insights エージェント拡張機能のデプロイに使用した接続文字列に関連付けられている、Application Insights リソース内の Live Metrics Stream をクリックします。 複数の仮想マシンからデータを送信する場合は、[サーバー名] でターゲット Azure 仮想マシンを選択します。 データのフローが開始されるまでに最大で 1 分かかる場合があります。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>PowerShell を使用して、Azure 仮想マシン スケール セットで .NET アプリケーションの Application Insights エージェントを管理する

Application Insights エージェントを Azure 仮想マシン スケール セットの拡張機能としてインストールまたは更新する
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Azure 仮想マシンと仮想マシン スケール セット上で実行されている、.NET アプリケーション用の Application Insights 監視エージェント拡張機能のトラブルシューティングに関するヒントを見つけます。

> [!NOTE]
> .NET Core、Java、および Node.js アプリケーションは、手動の SDK ベースのインストルメンテーションを通して Azure 仮想マシンおよび Azure 仮想マシン スケール セット上でのみサポートされているため、下の手順はこれらのシナリオには適用されせん。

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>次のステップ
* [Azure 仮想マシン スケール セットにアプリケーションをデプロイする](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)方法について学習します。
* エンドポイントがダウンしている場合に警告を受信するようにするには、[可用性 Web テストを設定](monitor-web-app-availability.md)します。
