---
title: Azure Application Insights Telemetry のデータ モデル | Microsoft Docs
description: Application Insights データ モデルの概要
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 749b4077b457eff836ec515f21d97e892e663156
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120667"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights Telemetry のデータ モデル

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) は、アプリケーションのパフォーマンスと使用状況を分析できるように、Web アプリケーションから Azure Portal にテレメトリを送信します。 テレメトリ モデルを標準化して、プラットフォームと言語に依存しない監視を作成できます。 

Application Insights で収集されたデータは、典型的なアプリケーション実行パターンをモデル化します。

![Application Insights のアプリケーション モデル](./media/data-model/application-insights-data-model.png)

次の種類のテレメトリは、アプリの実行を監視するために使用されます。 通常、Application Insights SDK によって、次の 3 種類が Web アプリケーション フレームワークから自動的に収集されます。

* [**要求**](data-model-request-telemetry.md) - アプリが受け取る要求をロギングするために生成されます。 たとえば、Application Insights Web SDK は、Web アプリが受け取る HTTP 要求ごとに要求テレメトリ項目を自動的に生成します。 

    **操作**は、要求を処理する実行のスレッドです。 [コードを記述](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)して、Web ジョブの "ウェイク アップ" または定期的にデータを処理する関数など、他の種類の操作を監視することもできます。  各操作には ID があります。 この ID を使用すると、アプリが要求を処理する際に生成されるすべてのテレメトリを[グループ分け](../../azure-monitor/app/correlation.md)できます。 各操作は成功するか失敗します。また、一定の時間がかかります。
* [**例外**](data-model-exception-telemetry.md) - 通常は、操作が失敗する原因になった例外を表します。
* [**依存関係**](data-model-dependency-telemetry.md) - アプリから外部のサービスまたはストレージへの呼び出しを表します (REST API や SQL など)。 ASP.NET では、SQL の依存関係呼び出しは `System.Data` で定義されます。 HTTP エンドポイントの呼び出しは `System.Net` で定義されます。 

Application Insights では、カスタム テレメトリ用にさらに 3 つのデータ型が提供されます。

* [トレース](data-model-trace-telemetry.md) - 直接またはアダプターを介して使用され、ユーザーが慣れているインストルメンテーション フレームワーク (`Log4Net` や `System.Diagnostics` など) を使用して診断ログを実装します。
* [イベント](data-model-event-telemetry.md) - 通常、ユーザーとサービスのやり取りをキャプチャして、使用状況のパターンを分析するために使用されます。
* [メトリック](data-model-metric-telemetry.md) - 定期的なスカラー測定を報告するために使用されます。

すべてのテレメトリ項目は、アプリケーションのバージョンやユーザー セッション ID のような[コンテキスト情報](data-model-context.md)を定義できます。コンテキストは、特定のシナリオのブロックを解除する、一連の厳密に型指定されたフィールドです。 アプリケーションのバージョンが適切に初期化されると、Application Insights は、再デプロイに関連付けられたアプリケーション動作の新しいパターンを検出できます。 セッション ID を使用して、障害やユーザーへの問題の影響を計算できます。 特定の失敗した依存関係、エラー トレース、重大な例外に対するセッション ID 値のカウントを個別に計算すると、影響をよく理解できます。

Application Insights Telemetry モデルは、テレメトリをそれが属する操作に[関連付ける](../../azure-monitor/app/correlation.md)方法を定義します。 たとえば、要求で SQL Database を呼び出し、診断情報を記録できます。 これらのテレメトリ項目の相関関係コンテキストを設定し、要求テレメトリに関連付けることができます。

## <a name="schema-improvements"></a>スキーマの強化

Application Insights データ モデルはシンプルで基本的でありながら、アプリケーション テレメトリをモデル化するのに十分に強力な方法です。 基本的なシナリオをサポートするモデルをシンプルかつスリムに維持しながら、高度な用途に対してスキーマを拡張できるよう努めています。

データ モデルまたはスキーマの問題やご提案を報告するには、GitHub の[ApplicationInsights ホーム](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) リポジトリを使用してください。

## <a name="next-steps"></a>次の手順

- [カスタム テレメトリを記述します](../../azure-monitor/app/api-custom-events-metrics.md)。
- [テレメトリの拡張とフィルター処理](../../azure-monitor/app/api-filtering-sampling.md)を行う方法を確認します。
- [サンプリング](../../azure-monitor/app/sampling.md)を使用して、データ モデルに基づいてテレメトリの量を最小限に抑えます。
- Application Insights でサポートされている[プラットフォーム](../../azure-monitor/app/platforms.md)を確認します。
