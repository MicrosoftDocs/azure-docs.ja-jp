---
title: Azure Application Insights Agent に関するトラブルシューティングと既知の問題 | Microsoft Docs
description: Application Insights Agent の既知の問題とトラブルシューティングの例。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: be202a6016842ee82b3230c2841d416d3b53ea15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505581"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights Agent (旧称 Status Monitor v2) のトラブルシューティング

監視を有効にしたとき、データ収集を妨げる問題が発生する可能性があります。
この記事では、既知の問題をすべて一覧にまとめ、トラブルシューティングの例を示します。

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

### <a name="powershell-versions"></a>PowerShell のバージョン
この製品は、PowerShell v5.1 を使用して作成およびテストされています。
このモジュールは、PowerShell バージョン 6 または 7 との互換性がありません。
PowerShell v5.1 は、新しいバージョンと併せて使用することをお勧めします。 詳細については、「[PowerShell 7 を Windows PowerShell 5.1 とサイド バイ サイドで使用する](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51)」を参照してください。

### <a name="conflict-with-iis-shared-configuration"></a>IIS 共有構成との競合

Web サーバーのクラスターがある場合、[共有構成](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)を使っている可能性があります。
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

### <a name="iis-nested-applications"></a>IIS の入れ子になったアプリケーション

バージョン 1.0 では、IIS の入れ子になったアプリケーションをインストルメント化しません。

### <a name="advanced-sdk-configuration-isnt-available"></a>高度な SDK 構成を使用できません。

バージョン 1.0 では SDK 構成はエンド ユーザーに公開されていません。

    
    
## <a name="troubleshooting"></a>トラブルシューティング
    
### <a name="troubleshooting-powershell"></a>PowerShell のトラブルシューティング

#### <a name="determine-which-modules-are-available"></a>使用できるモジュールを特定する
`Get-Module -ListAvailable` コマンドを使用すると、インストールされているモジュールを特定できます。

#### <a name="import-a-module-into-the-current-session"></a>現在のセッションにモジュールをインポートする
PowerShell セッションにモジュールが読み込まれていない場合は、`Import-Module <path to psd1>` コマンドを使って手動で読み込むことができます。


### <a name="troubleshooting-the-application-insights-agent-module"></a>Application Insights Agent モジュールのトラブルシューティング

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Application Insights Agent モジュールで使用できるコマンドの一覧を表示する
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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Application Insights Agent モジュールの現在のバージョンを確認する
モジュールに関する以下の情報を表示するには、`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` コマンドを実行します。
   - PowerShell モジュールのバージョン
   - Application Insights SDK のバージョン
   - PowerShell モジュールのファイル パス
    
このコマンドレットの使用方法の詳細については、[API リファレンス](status-monitor-v2-api-reference.md)に関する記事を参照してください。


### <a name="troubleshooting-running-processes"></a>実行中のプロセスのトラブルシューティング

インストルメント化されたコンピューターでプロセスを調べて、すべての DLL が読み込まれているかどうかを確認できます。
監視が機能している場合は、少なくとも 12 個の DLL が読み込まれる必要があります。

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` コマンドを使用して DLL を確認します。

このコマンドレットの使用方法の詳細については、[API リファレンス](status-monitor-v2-api-reference.md)に関する記事を参照してください。


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView を使用して ETW ログを収集する

#### <a name="setup"></a>セットアップ

1. PerfView.exe と PerfView64.exe を [GitHub](https://github.com/Microsoft/perfview/releases) からダウンロードします
2. PerfView64.exe を起動します。
3. **[詳細オプション]** を展開します。
4. 次のチェック ボックスをオフにします。
    - **[Zip]**
    - **[マージ]**
    - **.NET Symbol Collection (.NET シンボル収集)**
5. 次の **[追加プロバイダー]** を設定します。`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>ログの収集

1. 管理者特権のコマンド コンソールで、`iisreset /stop` コマンドを実行して、IIS とすべての Web アプリを無効にします。
2. PerfView で、**[Start Collection]\(収集の開始\)** を選択します。
3. 管理者特権のコマンド コンソールで、`iisreset /start` コマンドを実行して IIS を起動します。
4. アプリの参照を試みます。
5. アプリが読み込まれたら、PerfView に戻り、**[Stop Collection]\(収集の停止\)** を選択します。



## <a name="next-steps"></a>次の手順

- その他のパラメーターについては、[API リファレンス](status-monitor-v2-overview.md#powershell-api-reference)に関する記事を参照してください。