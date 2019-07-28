---
title: 'Azure Status Monitor v2 API リファレンス: 構成を設定する | Microsoft Docs'
description: Status Monitor v2 API リファレンス。 Set-ApplicationInsightsMonitoringConfig。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: ms-timothymothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734987"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Status Monitor v2 API:Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

このドキュメントでは、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="description"></a>説明

再インストールの全手順を実行せずに、構成ファイルを設定します。
変更を有効にするために IIS を再起動してください。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。


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
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ComputerName" は、指定された名前のコンピューターとのみ一致します。
- **AppFilter** は、コンピューターまたは VM 名に必要な C# の正規表現です。
    - ".*" はすべてに一致します
    - "ApplicationName" は、指定された名前の IIS アプリとのみ一致します。
- **InstrumentationKey** は上記の 2 つのフィルターに一致するアプリの監視を有効にするために必要です。
    - 監視を除外する規則を定義したい場合は、この値を null のままにします。


### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを表示するにはこのスイッチを使用します。


## <a name="output"></a>Output

既定では出力はありません。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey を使用して構成ファイルを設定した場合の詳細出力の例

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap を使用して構成ファイルを設定した場合の詳細出力の例

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
