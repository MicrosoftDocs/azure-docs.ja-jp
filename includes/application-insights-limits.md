---
title: インクルード ファイル
description: インクルード ファイル
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982602"
---
アプリケーションごと (インストルメンテーション キーごと) のメトリックとイベントの数には制限があります。 制限は、選択する[料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)によって異なります。

| Resource | 既定の制限 | Note
| --- | --- | --- |
| 1 日あたりの合計データ量 | 100 GB | 上限を設定することでデータを削減できます。 さらにデータが必要な場合は、ポータルで上限を最大 1,000 GB まで引き上げることができます。 1,000 GB を超える容量については、AIDataCap@microsoft.com までメールでご連絡ください。
| Throttling | 32,000 イベント/秒 | 制限は 1 分以上にわたって測定されます。
| データの保持 | [30 日から 730 日](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | このリソースは、[Search](../articles/azure-monitor/app/diagnostic-search.md)、[Analytics](../articles/azure-monitor/app/analytics.md)、および[メトリックス エクスプローラー](../articles/azure-monitor/app/metrics-explorer.md)用です。
| [可用性の複数手順のテスト](../articles/azure-monitor/app/availability-multistep.md)の詳細な結果の保持 | 90 日間 | このリソースは、各手順の詳細な結果を提供します。
| テレメトリ項目の最大サイズ | 64 KB |
| バッチあたりの最大テレメトリ項目数 | 64 K |
| プロパティとメトリック名の長さ | 150 | [型スキーマ](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| プロパティ値の文字列の長さ | 8,192  | [型スキーマ](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| トレースおよび例外のメッセージ長 | 32,768  | [型スキーマ](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)に関するページを参照してください。
| アプリあたりの[可用性テスト](../articles/azure-monitor/app/monitor-web-app-availability.md)の数 | 100 |
| [プロファイラー](../articles/azure-monitor/app/profiler.md)のデータ保持期間 | 5 日 |
| [プロファイラー](../articles/azure-monitor/app/profiler.md)の 1 日あたりの送信データ | 10 GB |

詳細については、[Application Insights の価格とクォータ](../articles/azure-monitor/app/pricing.md)に関するページを参照してください。