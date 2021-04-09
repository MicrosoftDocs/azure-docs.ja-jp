---
title: インクルード ファイル
description: インクルード ファイル
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726183"
---
アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| リソース | 既定の制限 | Note
| --- | --- | --- |
| 1 日あたりの合計データ量 | 100 GB | 上限を設定することでデータを削減できます。 さらにデータが必要な場合は、ポータルで上限を最大 1,000 GB まで引き上げることができます。 1,000 GB を超える容量については、AIDataCap@microsoft.com までメールでご連絡ください。
| Throttling | 32,000 イベント/秒 | 制限は 1 分以上にわたって測定されます。
| データ保持 (ログ) | [30 日から 730 日](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | このリソースは[ログ](../articles/azure-monitor/logs/log-query-overview.md)用です。
| データ保持 (メトリック) | 90 日間| このリソースは[メトリックス エクスプローラー](../articles/azure-monitor/essentials/metrics-charts.md)用です。
| [可用性の複数手順のテスト](../articles/azure-monitor/app/availability-multistep.md)の詳細な結果の保持 | 90 日間 | このリソースは、各手順の詳細な結果を提供します。
| テレメトリ項目の最大サイズ | 64 KB |
| バッチあたりの最大テレメトリ項目数 | 64 K |
| プロパティとメトリック名の長さ | 150 | [型スキーマ](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| プロパティ値の文字列の長さ | 8,192  | [型スキーマ](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| トレースおよび例外のメッセージ長 | 32,768  | [型スキーマ](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| アプリあたりの[可用性テスト](../articles/azure-monitor/app/monitor-web-app-availability.md)の数 | 100 |
| [プロファイラー](../articles/azure-monitor/app/profiler.md)のデータ保持期間 | 5 日 |
| [プロファイラー](../articles/azure-monitor/app/profiler.md)の 1 日あたりの送信データ | 10 GB |

詳細については、[Application Insights の価格とクォータ](../articles/azure-monitor/app/pricing.md)に関するページを参照してください。