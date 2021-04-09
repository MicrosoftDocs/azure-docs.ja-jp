---
title: Azure Application Insights .Net エージェント API リファレンス
description: Application Insights エージェント API リファレンス。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2278b9d70e888fa546dc64da4743b2bf5b6c45e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587521"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights エージェント API リファレンス

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

> [!NOTE] 
> - 開始するには、インストルメンテーション キーが必要です。 詳細については、[リソースの作成](create-new-resource.md#copy-the-instrumentation-key)に関するページを参照してください。
> - このコマンドレットでは、ライセンスおよびプライバシーに関する声明を確認して同意する必要があります。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限と昇格された実行ポリシーを備えた PowerShell セッションが必要です。 詳細については、[昇格された実行ポリシーを備えた管理者として PowerShell を実行する方法](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)に関するページを参照してください。
> - このコマンドレットでは、ライセンスおよびプライバシーに関する声明を確認して同意する必要があります。
> - インストルメンテーション エンジンを使用するとオーバーヘッドが増加します。これは、既定ではオフになっています。


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

いくつかのレジストリ キーを設定することで、インストルメンテーション エンジンを有効にします。
変更を有効にするために IIS を再起動してください。

インストルメンテーション エンジンは、.NET SDK によって収集されたデータを補完できます。
これは、マネージド プロセスの実行を説明するイベントおよびメッセージを収集します。 これらのイベントとメッセージには、依存関係の結果コード、HTTP 動詞、[SQL コマンド テキスト](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)が含まれます。

インストルメンテーション エンジンは、次の場合に有効にします。
- Enable コマンドレットを使用して監視を既に有効にしているが、インストルメンテーション エンジンは有効にしていなかった場合。
- .NET SDK を使用して自分のアプリを手動でインストルメント化しており、追加のテレメトリを収集したい場合。

### <a name="examples"></a>例

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>パラメーター

#### <a name="-acceptlicense"></a>-AcceptLicense
**省略可。** ヘッドレス インストールでライセンスおよびプライバシーに関する声明に同意する場合は、このスイッチを使用します。

#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

### <a name="output"></a>出力


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に有効化された場合の出力例

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

ターゲット コンピューターで IIS アプリのコード不要のアタッチ監視を有効にします。

このコマンドレットは、IIS applicationHost.config を変更し、いくつかのレジストリ キーを設定します。
また、applicationinsights.ikey.config ファイルも作成されます。これは、各アプリによって使用されるインストルメンテーション キーを定義します。
IIS は起動時に RedfieldModule を読み込みます。これにより、アプリケーションの起動時に、Application Insights SDK がアプリケーションに挿入されます。
変更を有効にするために IIS を再起動してください。

監視を有効にしたら、[ライブ メトリック](live-stream.md)を使用して、アプリがテレメトリを送信しているかどうかをすばやく確認することをお勧めします。

### <a name="examples"></a>例

#### <a name="example-with-a-single-instrumentation-key"></a>単一のインストルメンテーション キーを使用した例
この例では、現在コンピューター上にあるすべてのアプリに、単一のインストルメンテーション キーが割り当てられます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>インストルメンテーション キー マップを使用した例
次の点に注意してください。
- `MachineFilter` では、`'.*'` ワイルドカードを使用して現在のコンピューターを一致させます。
- `AppFilter='WebAppExclude'` では、`null` のインストルメンテーション キーを設定します。 指定されたアプリはインストルメント化されません。
- `AppFilter='WebAppOne'` では、指定されたアプリに一意のインストルメンテーション キーを割り当てます。
- `AppFilter='WebAppTwo'` では、指定されたアプリに一意のインストルメンテーション キーを割り当てます。
- 最後に、`AppFilter` でも `'.*'` ワイルドカードを使用して、以前の規則で一致していないすべての Web アプリを一致させ、既定のインストルメンテーション キーを割り当てます。
- 読みやすくするためにスペースが追加されます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>パラメーター

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必須。** このパラメーターを使用して、ターゲット コンピューター上のすべてのアプリによって使用される単一のインストルメンテーション キーを指定します。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必須。** このパラメーターを使用して、複数のインストルメンテーション キーのほか、各アプリによって使用されるインストルメンテーション キーのマッピングを指定します。
`MachineFilter` を設定することで、複数のコンピューター用に単一のインストール スクリプトを作成できます。

> [!IMPORTANT]
> アプリは、規則が指定された順番で規則と照合されます。 そのため、最も具体的な規則を最初に指定し、最も一般的な規則を最後に指定する必要があります。

##### <a name="schema"></a>スキーマ
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ComputerName" は、名前が指定されたものと完全に同じコンピューターとのみ一致します。
- **AppFilter** は、IIS サイト名に必要な C# の正規表現です。 [get-iissite](/powershell/module/iisadministration/get-iissite) コマンドを実行することで、ご利用のサーバー上でサイトのリストを入手できます。
    - ".*" はすべてに一致します
    - "SiteName" は、指定されたものと完全に同じ名前を持つ IIS サイトとのみ一致します。
- **InstrumentationKey** は上記の 2 つのフィルターに一致するアプリの監視を有効にするために必要です。
    - 監視を除外する規則を定義したい場合は、この値を null のままにします。


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**省略可。** インストルメンテーション エンジンを有効にして、マネージド プロセスの実行中に何が発生したかを示すイベントとメッセージを収集するには、このスイッチを使用します。 これらのイベントとメッセージには、依存関係の結果コード、HTTP 動詞、SQL コマンド テキストが含まれます。

インストルメンテーション エンジンを使用するとオーバーヘッドが増加します。これは、既定ではオフになっています。

#### <a name="-acceptlicense"></a>-AcceptLicense
**省略可。** ヘッドレス インストールでライセンスおよびプライバシーに関する声明に同意する場合は、このスイッチを使用します。

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Web サーバーのクラスターがある場合、[共有構成](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)を使用している可能性があります。
HttpModule をこの共有構成に挿入することはできません。
このスクリプトは失敗し、追加のインストール手順が必要であることを示すメッセージが表示されます。
このチェックを無視し、前提条件のインストールを続行するには、このスイッチを使用します。 詳細については、[IIS 共有構成との既知の競合](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)に関するページをご覧ください。

#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを表示するにはこのスイッチを使用します。

#### <a name="-whatif"></a>-WhatIf 
**共通パラメーター。** 実際に監視を有効にせずに、入力パラメーターをテストして検証する場合は、このスイッチを使用します。

### <a name="output"></a>出力

#### <a name="example-output-from-a-successful-enablement"></a>正常に有効化された場合の出力例

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

いくつかのレジストリ キーを削除することで、インストルメンテーション エンジンを無効にします。
変更を有効にするために IIS を再起動してください。

### <a name="examples"></a>例

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>パラメーター 

#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

### <a name="output"></a>出力


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に無効化された場合の出力例

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

ターゲット コンピューターに対する監視を無効にします。
このコマンドレットでは、IIS applicationHost.config に対する編集を削除し、レジストリ キーを削除します。

### <a name="examples"></a>例

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>パラメーター 

#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを表示するにはこのスイッチを使用します。

### <a name="output"></a>出力


##### <a name="example-output-from-successfully-disabling-monitoring"></a>正常に監視を無効にした場合の出力例

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

構成ファイルを取得し、コンソールに値を出力します。

### <a name="examples"></a>例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>パラメーター

必須パラメーターはありません。

### <a name="output"></a>出力


##### <a name="example-output-from-reading-the-config-file"></a>構成ファイルの読み取りからの出力例

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

このコマンドレットは、Status Monitor に関するトラブルシューティング情報を提供します。
このコマンドレットを使用すると、監視状態、PowerShell モジュールのバージョン、実行中のプロセスを調べることができます。
このコマンドレットでは、監視に必要なバージョン情報と主要なファイルに関する情報をレポートします。

### <a name="examples"></a>例

#### <a name="example-application-status"></a>例:アプリケーション ステータス

Web サイトの監視状態を表示するには、`Get-ApplicationInsightsMonitoringStatus` コマンドを実行します。

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

この例では、以下が表示されています。
- **Machine Identifier** は、サーバーを一意に識別するために使用される匿名 ID です。 サポート リクエストを作成する場合に、Microsoft ではお使いのサーバーのログを検索する際にこの ID が必要になります。
- **Default Web Site** は、IIS で停止されています
- **DemoWebApp111** は、IIS で開始されていますが、まだ要求を受信していません。 このレポートは実行中のプロセスがないことを示しています (ProcessId: not found)。
- **DemoWebApp222** は実行中で、監視されています (Instrumented: true)。 ユーザーの構成に基づいて、インストルメンテーション キー xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 がこのサイトと一致しています。
- **DemoWebApp333** は、Application Insights SDK を使用して手動でインストルメント化されています。 SDK が検出されたため、Status Monitor ではこのサイトの監視は行いません。


#### <a name="example-powershell-module-information"></a>例:PowerShell モジュールの情報

現在のモジュールに関する情報を表示するには、`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` コマンドを実行します。

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>例:ランタイムの状態

インストルメント化されたコンピューターでプロセスを調べて、すべての DLL が読み込まれているかどうかを確認できます。 監視が機能している場合は、少なくとも 12 個の DLL が読み込まれる必要があります。

コマンド `Get-ApplicationInsightsMonitoringStatus -InspectProcess` を実行します。


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>パラメーター

#### <a name="no-parameters"></a>(パラメーターなし)

既定では、このコマンドレットによって Web アプリケーションの監視状態がレポートされます。
アプリケーションが正常にインストルメント化されているかどうかを確認するには、このオプションを使用します。
また、どのインストルメンテーション キーがサイトに一致していたかを確認することもできます。


#### <a name="-powershellmodule"></a>-PowerShellModule
**オプション**。 このスイッチを使用すると、監視に必要なバージョン番号と DLL のパスがレポートされます。
Application Insights SDK を含む任意の DLL のバージョンを特定する必要がある場合は、このオプションを使用します。

#### <a name="-inspectprocess"></a>-InspectProcess

**オプション**。 このスイッチを使用すると、IIS が実行されているかどうかがレポートされます。
また、外部のツールをダウンロードして、必要な DLL が IIS ランタイムに読み込まれているかどうかを特定します。


何らかの理由でこのプロセスが失敗した場合は、次のコマンドを手動で実行できます。
- iisreset.exe /status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**オプション**。 InspectProcess でのみ使用されます。 このスイッチは、追加のツールがダウンロードされる前に表示されるユーザー プロンプトをスキップするために使用します。


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

再インストールの全手順を実行せずに、構成ファイルを設定します。
変更を有効にするために IIS を再起動してください。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。


### <a name="examples"></a>例

#### <a name="example-with-a-single-instrumentation-key"></a>単一のインストルメンテーション キーを使用した例
この例では、現在コンピューター上にあるすべてのアプリに、単一のインストルメンテーション キーが割り当てられます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>インストルメンテーション キー マップを使用した例
次の点に注意してください。
- `MachineFilter` では、`'.*'` ワイルドカードを使用して現在のコンピューターを一致させます。
- `AppFilter='WebAppExclude'` では、`null` のインストルメンテーション キーを設定します。 指定されたアプリはインストルメント化されません。
- `AppFilter='WebAppOne'` では、指定されたアプリに一意のインストルメンテーション キーを割り当てます。
- `AppFilter='WebAppTwo'` では、指定されたアプリに一意のインストルメンテーション キーを割り当てます。
- 最後に、`AppFilter` でも `'.*'` ワイルドカードを使用して、以前の規則で一致していないすべての Web アプリを一致させ、既定のインストルメンテーション キーを割り当てます。
- 読みやすくするためにスペースが追加されます。

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>パラメーター

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**必須。** このパラメーターを使用して、ターゲット コンピューター上のすべてのアプリによって使用される単一のインストルメンテーション キーを指定します。

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必須。** このパラメーターを使用して、複数のインストルメンテーション キーのほか、各アプリによって使用されるインストルメンテーション キーのマッピングを指定します。
`MachineFilter` を設定することで、複数のコンピューター用に単一のインストール スクリプトを作成できます。

> [!IMPORTANT]
> アプリは、規則が指定された順番で規則と照合されます。 そのため、最も具体的な規則を最初に指定し、最も一般的な規則を最後に指定する必要があります。

##### <a name="schema"></a>スキーマ
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ComputerName" は、指定された名前のコンピューターとのみ一致します。
- **AppFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ApplicationName" は、指定された名前の IIS アプリとのみ一致します。
- **InstrumentationKey** は上記の 2 つのフィルターに一致するアプリの監視を有効にするために必要です。
    - 監視を除外する規則を定義したい場合は、この値を null のままにします。


#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを表示するにはこのスイッチを使用します。


### <a name="output"></a>出力

既定では出力はありません。

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey を使用して構成ファイルを設定した場合の詳細出力の例

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap を使用して構成ファイルを設定した場合の詳細出力の例

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

コードなしの接続ランタイムから [ETW イベント](/windows/desktop/etw/event-tracing-portal)を収集します。 このコマンドレットは、[PerfView](https://github.com/microsoft/perfview) を実行する代わりの手段です。

収集されたイベントは、リアルタイムでコンソールに出力され、ETL ファイルに保存されます。 出力 ETL ファイルは、さらに調査するために、[PerfView](https://github.com/microsoft/perfview) で開くことができます。

このコマンドレットは、タイムアウト期間 (既定値は 5 分) に達するまで実行されるか、手動で停止します (`Ctrl + C`)。

### <a name="examples"></a>例

#### <a name="how-to-collect-events"></a>イベントの収集方法

通常、ご利用のアプリケーションがインストルメント化されている理由を調査するには、イベントの収集が求められます。

コードなしの接続ランタイムによって、IIS が起動されとき、およびアプリケーションが起動されたときの ETW イベントが出力されます。

これらのイベントを収集するには
1. 管理者特権のコマンド コンソールで、`iisreset /stop` を実行して IIS とすべての Web アプリをオフにします。
2. このコマンドレットを実行します。
3. 管理者特権のコマンド コンソールで、`iisreset /start` を実行して IIS を開始します。
4. アプリの参照を試みます。
5. ご利用のアプリで読み込みが完了した後、手動で停止したり (`Ctrl + C`)、タイムアウトするまで待機したりすることができます。

#### <a name="what-events-to-collect"></a>収集するイベント

イベントを収集するときに、3 つのオプションが用意されています。
1. スイッチ `-CollectSdkEvents` を使用して、Application Insights SDK から出力されたイベントを収集します。
2. スイッチ `-CollectRedfieldEvents` を使用して、Status Monitor と Redfield Runtime によって出力されたイベントを収集します。 これらのログは、IIS とアプリケーションの起動を診断するときに役立ちます。
3. 両方のスイッチを使用して、両方のイベントの種類を収集します。
4. 既定では、スイッチが指定されていない場合、両方のイベントの種類が収集されます。


### <a name="parameters"></a>パラメーター

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**省略可。** このパラメーターを使用して、このスクリプトでイベントが収集される時間を設定します。 既定値は 5 分です。

#### <a name="-logdirectory"></a>-LogDirectory
**省略可。** このスイッチを使用して、ETL ファイルの出力ディレクトリを設定します。 既定では、このファイルは PowerShell Modules ディレクトリに作成されます。 スクリプトの実行中は、完全なパスが表示されます。


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**省略可。** このスイッチを使用して、Application Insights SDK イベントを収集します。

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**省略可。** このスイッチを使用して、Status Monitor と Redfield Runtime からイベントを収集します。

#### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。



### <a name="output"></a>出力


#### <a name="example-of-application-startup-logs"></a>アプリケーションの起動ログの例
```powershell
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

## <a name="next-steps"></a>次のステップ

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../essentials/metrics-charts.md)します。
- 問題を診断するために[イベントとログを検索](./diagnostic-search.md)します。
- より高度なクエリのために[分析](../logs/log-query-overview.md)を使用します。
- [ダッシュボードを作成](./overview-dashboard.md)します。
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](./javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](./asp-net.md)します。
 
 Application Insights エージェントをさらに活用する:
 - Application Insights エージェントのトラブルシューティングを行う場合は、[こちらのガイド](status-monitor-v2-troubleshoot.md)を使用してください。

