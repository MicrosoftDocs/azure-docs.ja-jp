---
title: 'Azure Status Monitor v2 API リファレンス: インストルメンテーション エンジンを有効にする |Microsoft Docs'
description: Status Monitor v2 API リファレンス。 Enable-InstrumentationEngine。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3c0f4e015d3e01e86daaf101c15e16857540a520
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033159"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>Status Monitor v2 API:Enable-InstrumentationEngine

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

## <a name="description"></a>説明

いくつかのレジストリ キーを設定することで、インストルメンテーション エンジンを有効にします。
変更を有効にするために IIS を再起動してください。

インストルメンテーション エンジンは、.NET SDK によって収集されたデータを補完できます。
これは、マネージド プロセスの実行を説明するイベントおよびメッセージを収集します。 これらのイベントとメッセージには、依存関係の結果コード、HTTP 動詞、[SQL コマンド テキスト](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)が含まれます。

インストルメンテーション エンジンは、次の場合に有効にします。
- Enable コマンドレットを使用して監視を既に有効にしているが、インストルメンテーション エンジンは有効にしていなかった場合。
- .NET SDK を使用して自分のアプリを手動でインストルメント化しており、追加のテレメトリを収集したい場合。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。

> [!NOTE] 
> - このコマンドレットでは、ライセンスおよびプライバシーに関する声明を確認して同意する必要があります。
> - インストルメンテーション エンジンを使用するとオーバーヘッドが増加します。これは、既定ではオフになっています。

## <a name="examples"></a>例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>parameters

### <a name="-acceptlicense"></a>-AcceptLicense
**省略可能。** ヘッドレス インストールでライセンスおよびプライバシーに関する声明に同意する場合は、このスイッチを使用します。

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に有効化された場合の出力例

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>次の手順

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
- より高度なクエリのために[分析](../../azure-monitor/app/analytics.md)を使用します。
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。
 
 Status Monitor v2 の活用:
 - ガイドを使用して、Status Monitor v2 の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行います。
 - 設定が正しく記録されたことを確認するために[構成を取得](status-monitor-v2-api-get-config.md)します。
 - 監視を検査するために[状態を取得](status-monitor-v2-api-get-status.md)します。
