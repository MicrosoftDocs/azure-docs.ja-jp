---
title: Azure VM のパフォーマンスを監視する - Azure Application Insights
description: Azure VM および Azure 仮想マシン スケール セットに対するアプリケーション パフォーマンス監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711483"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure 仮想マシンと Azure 仮想マシン スケール セットに Azure Monitor Application Insights エージェントをデプロイする

[Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)と [Azure 仮想マシン スケール セット](../../virtual-machine-scale-sets/index.yml)上で実行されている .NET または Java ベースの Web アプリケーションに対する監視を有効にすることが、従来より簡単になりました。 コードを変更することなく、Application Insights を使用する利点のすべてが得られます。

この記事では、Application Insights エージェントを使用した Application Insights 監視の有効化について説明した後、大規模なデプロイのプロセスを自動化するための事前ガイダンスを提供します。
> [!IMPORTANT]
> Azure VM と VMSS で実行されている **Java** ベースのアプリケーションは、 **[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)** (一般公開) で監視されます。

> [!IMPORTANT]
> **Azure VM と VMSS** で実行される ASP.NET アプリケーション用の Azure Application Insights エージェントは、現在パブリック プレビューの段階にあります。 **オンプレミス** で実行されている ASP.Net アプリケーションを監視するには、[オンプレミス サーバー用の Azure Application Insights エージェント](./status-monitor-v2-overview.md)を使用します。これは一般提供されていて完全にサポートされます。
> Azure VM と VMSS のプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めしません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure 仮想マシンと Azure 仮想マシン スケール セットでホストされるアプリケーションに対するアプリケーション監視を有効にする方法には、次の 2 つがあります。

### <a name="auto-instrumentation-via-application-insights-agent"></a>Application Insights エージェントを使用した自動インストルメンテーション

* 有効にするにはこの方法が最も簡単であり、高度な構成は不要です。 多くの場合、これは "ランタイム" 監視と呼ばれます。

* Azure 仮想マシンと Azure 仮想マシン スケール セットの場合は、少なくともこのレベルの監視を有効にすることをお勧めします。 その後、特定のシナリオに基づいて、手動のインストルメンテーションが必要かどうかを評価できます。

> [!NOTE]
> 自動インストルメンテーションは、現在、.NET IIS でホストされているアプリケーションと Java でのみ使用できます。 Azure 仮想マシンと仮想マシン スケール セット上でホストされている ASP.NET Core、Node.js、Python アプリケーションをインストルメント化するには、SDK を使用します。


#### <a name="net"></a>.NET

  * Application Insights エージェントでは、.NET SDK と同じ依存関係のシグナルを既定で自動的に収集します。 詳細については、「[依存関係の自動収集](./auto-collect-dependencies.md#net)」を参照してください。
        
#### <a name="java"></a>Java
  * Java の場合、 **[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)** を使用することをお勧めします。 最も一般的なライブラリとフレームワーク、およびログと依存関係は、[自動収集](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics)され、[追加の構成](./java-standalone-config.md)が多数あります。

### <a name="code-based-via-sdk"></a>コードベース (SDK を使用)
    
#### <a name="net"></a>.NET
  * .NET アプリの場合、このアプローチはカスタマイズできる部分がはるかに多いのですが、[Application Insights SDK NuGet パッケージへの依存関係を追加](./asp-net.md)する必要があります。 また、この方法では、最新バージョンのパッケージへの更新を自分で管理する必要があります。

  * エージェントベースの監視の既定ではキャプチャされないイベント/依存関係を追跡するためにカスタム API 呼び出しを行う必要がある場合は、この方法を使用する必要があります。 詳細については、[カスタムのイベントとメトリックのための API に関する記事](./api-custom-events-metrics.md)を参照してください。

    > [!NOTE]
    > .NET アプリの場合のみ - エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 このチェックアウトの詳細については、以下の「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

#### <a name="net-core"></a>.NET Core
.NET Core アプリケーションを監視するには、[SDK](./asp-net-core.md) を使用します。 

#### <a name="java"></a>Java 

Java アプリケーションに対して追加のカスタム テレメトリが必要な場合は、[使用可能なもの](./java-in-process-agent.md#send-custom-telemetry-from-your-application)の確認、[カスタム ディメンション](./java-standalone-config.md#custom-dimensions)の追加、または[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md)の使用のいずれかを行います。 

#### <a name="nodejs"></a>Node.js

Node.js アプリケーションをインストルメント化するには、[SDK](./nodejs.md) を使用します。

#### <a name="python"></a>Python

Python アプリを監視するには、[SDK](./opencensus-python.md) を使用します。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell を使用して、Azure 仮想マシン上で .NET アプリケーションの Application Insights エージェントを管理する

> [!NOTE]
> Application Insights エージェントをインストールする前に、接続文字列が必要になります。 [新しい Application Insights リソースを作成する](./create-new-resource.md)か、既存の Application Insights リソースから接続文字列をコピーします。

> [!NOTE]
> PowerShell の新機能 [使用開始ガイド](/powershell/azure/get-started-azureps)を確認してください。

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
> .NET Core、Node.js、および Python アプリケーションは、手動の SDK ベースのインストルメンテーションを通して Azure 仮想マシンおよび Azure 仮想マシン スケール セット上でのみサポートされているため、下の手順はこれらのシナリオには適用されせん。

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>次の手順
* [Azure 仮想マシン スケール セットにアプリケーションをデプロイする](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)方法について学習します。
* エンドポイントがダウンしている場合に警告を受信するようにするには、[可用性 Web テストを設定](monitor-web-app-availability.md)します。