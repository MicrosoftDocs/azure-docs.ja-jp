---
title: Azure Status Monitor v2 のトラブルシューティングと既知の問題 | Microsoft Docs
description: Status Monitor v2 の既知の問題とトラブルシューティングの例です。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807027"
---
# <a name="troubleshooting-status-monitor-v2"></a>Status Monitor v2 のトラブルシューティング

監視を有効にしたとき、データ収集を妨げる問題が発生する可能性があります。
この記事では、既知の問題をすべて一覧にまとめ、トラブルシューティングの例を示します。
この一覧にない問題が発生した場合は、[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) でお問い合わせください。


> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="known-issues"></a>既知の問題

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>アプリの bin ディレクトリでの DLL の競合

次のいずれかの DLL が bin ディレクトリに存在する場合、監視が失敗することがあります。

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

これらの DLL の一部は、アプリで使われない場合でも、Visual Studio の既定のアプリ テンプレートに含まれます。
トラブルシューティング ツールを使用して症状の動作を確認できます。

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset とアプリの読み込み (テレメトリなし)。 Sysinternals で調べます (Handle.exe と ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>IIS 共有構成との競合

Web サーバーのクラスターがある場合、[共有構成](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)を使っている可能性があります。
HttpModule をこの共有構成に挿入することはできません。
各 Web サーバー上で Enable コマンドを実行して、各サーバーの GAC に DLL をインストールします。

Enable コマンドを実行した後、次の手順を実行します。
1. 共有構成ディレクトリに移動して、applicationHost.config ファイルを見つけます。
2. 次の行を構成の modules セクションに追加します。
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>トラブルシューティング
    
### <a name="troubleshooting-powershell"></a>PowerShell のトラブルシューティング

#### <a name="determine-which-modules-are-available"></a>使用できるモジュールを特定する
`Get-Module -ListAvailable` コマンドを使用すると、インストールされているモジュールを特定できます。

#### <a name="import-a-module-into-the-current-session"></a>現在のセッションにモジュールをインポートする
PowerShell セッションにモジュールが読み込まれていない場合は、`Import-Module <path to psd1>` コマンドを使って手動で読み込むことができます。


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Status Monitor v2 モジュールのトラブルシューティング

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Status Monitor v2 モジュールで使用できるコマンドを一覧表示する
使用できるコマンドを取得するには、`Get-Command -Module Az.ApplicationMonitor` コマンドを実行します。

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Status Monitor v2 モジュールの現在のバージョンを確認する
モジュールに関する以下の情報を表示するには、`Get-ApplicationInsightsMonitoringStatus` コマンドを実行します。
   - PowerShell モジュールのバージョン
   - Application Insights SDK のバージョン
   - PowerShell モジュールのファイル パス
    
このコマンドレットの使用方法の詳細については、[API リファレンス](status-monitor-v2-api-get-status.md)に関する記事を参照してください。


### <a name="troubleshooting-running-processes"></a>実行中のプロセスのトラブルシューティング

インストルメント化されたコンピューターでプロセスを調べて、すべての DLL が読み込まれているかどうかを確認できます。
監視が機能している場合は、少なくとも 12 個の DLL が読み込まれる必要があります。

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` コマンドを使用して DLL を確認します。

このコマンドレットの使用方法の詳細については、[API リファレンス](status-monitor-v2-api-get-status.md)に関する記事を参照してください。


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView を使用して ETW ログを収集する

#### <a name="setup"></a>セットアップ

1. PerfView.exe と PerfView64.exe を [GitHub](https://github.com/Microsoft/perfview/releases) からダウンロードします
2. PerfView64.exe を起動します。
3. **[詳細オプション]** を展開します。
4. 次のチェック ボックスをオフにします。
    - **[Zip]**
    - **[マージ]**
    - **[.NET Symbol Collection]**
5. 次の **[追加プロバイダー]** を設定します。`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>ログの収集

1. 管理者特権のコマンド コンソールで、`iisreset /stop` コマンドを実行して、IIS とすべての Web アプリを無効にします。
2. PerfView で、 **[Start Collection]\(収集の開始\)** を選択します。
3. 管理者特権のコマンド コンソールで、`iisreset /start` コマンドを実行して IIS を起動します。
4. アプリの参照を試みます。
5. アプリが読み込まれたら、PerfView に戻り、 **[Stop Collection]\(収集の停止\)** を選択します。



## <a name="next-steps"></a>次の手順

- その他のパラメーターについては、[API リファレンス](status-monitor-v2-overview.md#powershell-api-reference)に関する記事を参照してください。
- この一覧にない問題が発生した場合は、[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues) でお問い合わせください。
