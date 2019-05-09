---
title: Azure Application Insights Telemetry のデータ モデル - メトリック テレメトリ | Microsoft Docs
description: メトリック テレメトリ用の Application Insights データ モデル
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
ms.openlocfilehash: 0973c86d055ff4ebbe7e5a3c4a2ca4e3dcabc6a0
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117692"
---
# <a name="metric-telemetry-application-insights-data-model"></a>メトリック テレメトリ:Application Insights データ モデル

[Application Insights](../../azure-monitor/app/app-insights-overview.md) でサポートされるメトリック テレメトリには、単一測定と事前集計メトリックの 2 種類があります。 単一測定は名前と値だけのテレメトリです。 事前集計メトリックは、集計間隔中のメトリックの最小値と最大値と、その標準偏差を指定します。

事前集計メトリック テレメトリは、その集計期間が 1 分間であることを前提としています。

Application Insights でサポートされているよく知られているいくつかのメトリック名があります。 これらのメトリックは、performanceCounters テーブルに配置されます。

システムとプロセスのカウンターを表すメトリック:

| **.NET の名前**             | **プラットフォームに依存しない名前** | **REST API の名前** | **説明**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | 作業中... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | コンピューターの CPU の合計
| `\Memory\Available Bytes`                 | 作業中... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | コンピューターで実行されているプロセスが利用できる物理メモリの量をバイト単位で表示します。 これは、ゼロ メモリ、空きメモリ、スタンバイ メモリの一覧の領域を合計することで計算されます。 空きメモリはメモリを使用する準備が整っているメモリです。ゼロ メモリはゼロで満たされたメモリのページで構成されており、後続のプロセスが、前のプロセスによって使用されたデータを見ることができないようにします。スタンバイ メモリは、ディスクへのルート上でプロセスのワーキング セット (プロセスの物理メモリ) から削除されたメモリですが、引き続き再度呼び出すことができます。 「[Memory Object (メモリ オブジェクト)](https://msdn.microsoft.com/library/ms804008.aspx)」を参照してください
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | 作業中... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | アプリケーションをホストするプロセスの CPU
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | 作業中... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | アプリケーションをホストするプロセスで使用されるメモリ
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | 作業中... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | アプリケーションをホストしているプロセスで実行される I/O 操作の速度
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | 作業中... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | アプリケーションによって処理された要求の割合 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | 作業中... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | アプリケーションによってスローされた例外の割合
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | 作業中... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | 平均要求実行時間
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | 作業中... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | キューで処理を待っている要求の数

## <a name="name"></a>Name

Application Insights ポータルと UI に表示するメトリックの名前。 

## <a name="value"></a>値

測定の単一の値。 集計での個別の測定値の合計。

## <a name="count"></a>Count

集計メトリックのメトリックの重み。 測定には設定しないでください。

## <a name="min"></a>Min

集計メトリックの最小値。 測定には設定しないでください。

## <a name="max"></a>max

集計メトリックの最大値。 測定には設定しないでください。

## <a name="standard-deviation"></a>標準偏差

集計メトリックの標準偏差。 測定には設定しないでください。

## <a name="custom-properties"></a>カスタム プロパティ

カスタム プロパティ `CustomPerfCounter` が `true` に設定されたメトリックは、メトリックが Windows パフォーマンス カウンターを表すことを示します。 これらのメトリックは、performanceCounters テーブルに配置されます。 customMetrics にはありません。 また、このメトリックの名前は、カテゴリ、カウンター、インスタンスの名前を抽出するために解析されます。

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>次の手順

- [カスタムのイベントとメトリックのための Application Insights API](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) の使用方法を確認します。
- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- Application Insights でサポートされている[プラットフォーム](../../azure-monitor/app/platforms.md)を確認します。
