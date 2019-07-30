---
title: 'Azure Status Monitor v2 API リファレンス: トレースの開始 | Microsoft Docs'
description: Status Monitor v2 API リファレンス。 トレースの開始。 Status Monitor と Application Insights SDK から ETW のログを収集します。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 186324e6a93f90fd04323074e5dc883e3e271725
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479132"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v031-alpha"></a>Status Monitor v2 API:Start-ApplicationInsightsMonitoringTrace (v0.3.1 アルファ)

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="description"></a>説明

コードなしの接続ランタイムから [ETW イベント](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)を収集します。 このコマンドレットは、[PerfView](https://github.com/microsoft/perfview) を実行する代わりの手段です。

収集されたイベントは、リアルタイムでコンソールに出力され、ETL ファイルに保存されます。 出力 ETL ファイルは、さらに調査するために、[PerfView](https://github.com/microsoft/perfview) で開くことができます。

このコマンドレットは、タイムアウト期間 (既定値は 5 分) に達するまで実行されるか、手動で停止します (`Ctrl + C`)。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。

## <a name="examples"></a>例

### <a name="how-to-collect-events"></a>イベントの収集方法

通常、ご利用のアプリケーションがインストルメント化されている理由を調査するには、イベントの収集が求められます。

コードなしの接続ランタイムによって、IIS が起動されとき、およびアプリケーションが起動されたときの ETW イベントが出力されます。

これらのイベントを収集するには
1. 管理者特権のコマンド コンソールで、`iisreset /stop` を実行して IIS とすべての Web アプリをオフにします。
2. このコマンドレットを実行します。
3. 管理者特権のコマンド コンソールで、`iisreset /start` を実行して IIS を開始します。
4. アプリの参照を試みます。
5. ご利用のアプリで読み込みが完了した後、手動で停止したり (`Ctrl + C`)、タイムアウトするまで待機したりすることができます。

### <a name="what-events-to-collect"></a>収集するイベント

イベントを収集するときに、3 つのオプションが用意されています。
1. スイッチ `-CollectSdkEvents` を使用して、Application Insights SDK から出力されたイベントを収集します。
2. スイッチ `-CollectRedfieldEvents` を使用して、Status Monitor と Redfield Runtime によって出力されたイベントを収集します。 これらのログは、IIS とアプリケーションの起動を診断するときに役立ちます。
3. 両方のスイッチを使用して、両方のイベントの種類を収集します。
4. 既定では、スイッチが指定されていない場合、両方のイベントの種類が収集されます。


## <a name="parameters"></a>parameters

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**省略可能。** このパラメーターを使用して、このスクリプトでイベントが収集される時間を設定します。 既定値は 5 分です。

### <a name="-logdirectory"></a>-LogDirectory
**省略可能。** このスイッチを使用して、ETL ファイルの出力ディレクトリを設定します。 既定では、このファイルは PowerShell Modules ディレクトリに作成されます。 スクリプトの実行中は、完全なパスが表示されます。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**省略可能。** このスイッチを使用して、Application Insights SDK イベントを収集します。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**省略可能。** このスイッチを使用して、Status Monitor と Redfield Runtime からイベントを収集します。

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>アプリケーションの起動ログの例
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>次の手順

その他のトラブルシューティング:

- その他のトラブルシューティングの手順をここで確認する: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 見落としている可能性があるパラメーターについては、[API リファレンス](status-monitor-v2-overview.md#powershell-api-reference)に関する記事を確認してください。
- その他のヘルプが必要な場合は、[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) 上で Microsoft にお問い合わせください。



 Status Monitor v2 の活用:
 - ガイドを使用して、Status Monitor v2 の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行います。
 - 設定が正しく記録されたことを確認するために[構成を取得](status-monitor-v2-api-get-config.md)します。
 - 監視を検査するために[状態を取得](status-monitor-v2-api-get-status.md)します。
