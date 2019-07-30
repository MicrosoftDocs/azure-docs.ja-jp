---
title: 'Azure Status Monitor v2 API リファレンス: 監視を有効にする | Microsoft Docs'
description: Status Monitor v2 API リファレンス。 Enable-ApplicationInsightsMonitoring。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
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
ms.openlocfilehash: e0d5363e253e89b32b5eca14366504f0ace39043
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479640"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v031-alpha"></a>Status Monitor v2 API:Enable-ApplicationInsightsMonitoring (v0.3.1-alpha)

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="description"></a>説明

ターゲット コンピューターで IIS アプリのコード不要のアタッチ監視を有効にします。

このコマンドレットは、IIS applicationHost.config を変更し、いくつかのレジストリ キーを設定します。
また、applicationinsights.ikey.config ファイルも作成されます。これは、各アプリによって使用されるインストルメンテーション キーを定義します。
IIS は起動時に RedfieldModule を読み込みます。これにより、アプリケーションの起動時に、Application Insights SDK がアプリケーションに挿入されます。
変更を有効にするために IIS を再起動してください。

監視を有効にしたら、[ライブ メトリック](live-stream.md)を使用して、アプリがテレメトリを送信しているかどうかをすばやく確認することをお勧めします。


> [!NOTE] 
> - 開始するには、インストルメンテーション キーが必要です。 詳細については、[リソースの作成](create-new-resource.md#copy-the-instrumentation-key)に関するページを参照してください。
> - このコマンドレットでは、ライセンスおよびプライバシーに関する声明を確認して同意する必要があります。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限と昇格された実行ポリシーを備えた PowerShell セッションが必要です。 詳細については、[昇格された実行ポリシーを備えた管理者として PowerShell を実行する方法](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)に関するページを参照してください。

## <a name="examples"></a>例

### <a name="example-with-a-single-instrumentation-key"></a>単一のインストルメンテーション キーを使用した例
この例では、現在コンピューター上にあるすべてのアプリに、単一のインストルメンテーション キーが割り当てられます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>インストルメンテーション キー マップを使用した例
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


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必須。** このパラメーターを使用して、ターゲット コンピューター上のすべてのアプリによって使用される単一のインストルメンテーション キーを指定します。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必須。** このパラメーターを使用して、複数のインストルメンテーション キーのほか、各アプリによって使用されるインストルメンテーション キーのマッピングを指定します。
`MachineFilter` を設定することで、複数のコンピューター用に単一のインストール スクリプトを作成できます。

> [!IMPORTANT]
> アプリは、規則が指定された順番で規則と照合されます。 そのため、最も具体的な規則を最初に指定し、最も一般的な規則を最後に指定する必要があります。

#### <a name="schema"></a>スキーマ
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ComputerName" は、名前が指定されたものと完全に同じコンピューターとのみ一致します。
- **AppFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ApplicationName" は、名前が指定されたものと完全に同じ IIS アプリとのみ一致します。
- **InstrumentationKey** は上記の 2 つのフィルターに一致するアプリの監視を有効にするために必要です。
    - 監視を除外する規則を定義したい場合は、この値を null のままにします。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**省略可能。** インストルメンテーション エンジンを有効にして、マネージド プロセスの実行中に何が発生したかを示すイベントとメッセージを収集するには、このスイッチを使用します。 これらのイベントとメッセージには、依存関係の結果コード、HTTP 動詞、SQL コマンド テキストが含まれます。

インストルメンテーション エンジンを使用するとオーバーヘッドが増加します。これは、既定ではオフになっています。

### <a name="-acceptlicense"></a>-AcceptLicense
**省略可能。** ヘッドレス インストールでライセンスおよびプライバシーに関する声明に同意する場合は、このスイッチを使用します。

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Web サーバーのクラスターがある場合、[共有構成](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)を使用している可能性があります。
HttpModule をこの共有構成に挿入することはできません。
このスクリプトは失敗し、追加のインストール手順が必要であることを示すメッセージが表示されます。
このチェックを無視し、前提条件のインストールを続行するには、このスイッチを使用します。 詳細については、[IIS 共有構成との既知の競合](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)に関するページをご覧ください。

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを表示するにはこのスイッチを使用します。

### <a name="-whatif"></a>-WhatIf 
**共通パラメーター。** 実際に監視を有効にせずに、入力パラメーターをテストして検証する場合は、このスイッチを使用します。

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>正常に有効化された場合の出力例

```
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

## <a name="next-steps"></a>次の手順

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
- より高度なクエリのために[分析を使用](../../azure-monitor/app/analytics.md)します。
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。
 
 Status Monitor v2 の活用:
 - ガイドを使用して、Status Monitor v2 の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行います。
 - 設定が正しく記録されたことを確認するために[構成を取得](status-monitor-v2-api-get-config.md)します。
 - 監視を検査するために[状態を取得](status-monitor-v2-api-get-status.md)します。
