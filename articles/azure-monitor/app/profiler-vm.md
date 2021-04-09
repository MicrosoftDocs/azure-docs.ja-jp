---
title: Azure VM 上の Web アプリをプロファイルする - Application Insights Profiler
description: Application Insights Profiler を使用して Azure VM 上の Web アプリをプロファイルします。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315837"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Application Insights Profiler を使用して、Azure 仮想マシンまたは仮想マシン スケール セットで実行されている Web アプリをプロファイルする

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>仮想マシンまたは仮想マシン スケール セットに Profiler をデプロイする
この記事では、Azure 仮想マシン (VM) または Azure 仮想マシン スケール セットで Application Insights Profiler を実行する方法を説明します。 Profiler は、VM 用の Azure Diagnostics 拡張機能と共にインストールされます。 Profiler を実行するように拡張機能を構成し、Application Insights SDK をお使いのアプリケーションにビルドします。

1. Application Insights SDK を [ASP.NET アプリケーション](./asp-net.md)に追加します。

   要求のプロファイルを表示するには、要求テレメトリを Application Insights に送信する必要があります。

1. Azure Diagnostics 拡張機能を VM にインストールします。 Resource Manager テンプレートの完全な例については、次を参照してください。  
   * [仮想マシン](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [仮想マシン スケール セット](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     重要な部分は、WadCfg の ApplicationInsightsProfilerSink です。 Microsoft Azure Diagnostics で Profiler がお使いの iKey にデータを送信できるようにするには、このセクションに他のシンクを追加します。
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. 変更された環境のデプロイ定義をデプロイします。  

   通常、変更の適用には、完全なテンプレートのデプロイか、PowerShell コマンドレットまたは Visual Studio を使用したクラウド サービス ベースの発行が含まれます。  

   次の PowerShell コマンドは、Azure Diagnostics 拡張機能のみを使用する既存の仮想マシン向けの代替アプローチです。 前述の ProfilerSink を、Get-AzVMDiagnosticsExtension コマンドによって返された構成に追加します。 そして更新された構成を Set-AzVMDiagnosticsExtension コマンドに渡します。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 経由で目的のアプリケーションが実行されている場合は、`IIS Http Tracing` Windows 機能を有効にします。

   a. この環境に対するリモート アクセスを確立し、[[Windows 機能の追加]](/iis/configuration/system.webserver/tracing/) ウィンドウ使用するか、 (管理者として) PowerShell で次のコマンドを実行します。  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. リモート アクセスの確立に問題がある場合は、[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して次のコマンドを実行できます。  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. アプリケーションをデプロイします。

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Azure Resource Explorer を使用して Profiler シンクを設定する
portal から Application Insights Profiler シンクを設定する方法はまだありません。 前述のように PowerShell を使用する代わりに、Azure Resource Explorer を使用してシンクを設定することができます。 ただし、VM を再びデプロイするとシンクは失われますので注意してください。 この設定を保存するために VM をデプロイするときに、使用する構成を更新する必要があります。

1. ご使用の仮想マシン用にインストールされている拡張機能を表示して、Windows Azure Diagnostics 拡張機能がインストールされていることを確認します。  

    ![WAD 拡張機能がインストールされているかどうか確認する][wadextension]

2. 使用する VM 用の VM 診断拡張機能を見つけます。 [https://resources.azure.com](https://resources.azure.com) に移動します。 リソース グループ、Microsoft.Compute virtualMachines、仮想マシン名、および拡張機能を展開します。  

    ![Azure Resource Explorer で WAD 構成に移動する][azureresourceexplorer]

3. Application Insights Profiler シンクを WadCfg の下の SinksConfig ノードに追加します。 SinksConfig セクションがまだない場合は、追加する必要がある場合があります。 必ず、適切な Application Insights iKey を設定に指定してください。 右上隅でエクスプローラーのモードを読み取り/書き込みに切り替えて、青色の [編集] ボタンを押す必要があります。

    ![Application Insights Profiler シンクを追加する][resourceexplorersinksconfig]

4. 構成の編集が完了したら、[PUT] を押します。 PUT が成功すると、画面の真ん中に緑色のチェック マークが表示されます。

    ![PUT 要求を送信して変更を適用する][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>オンプレミスのサーバーで Profiler を実行できますか。
オンプレミス サーバーで Application Insights Profiler をサポートすることは予定されていません。

## <a name="next-steps"></a>次のステップ

- アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
- Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
- Profiler の問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

