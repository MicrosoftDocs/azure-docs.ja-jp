---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728984"
---
カスタム測定値のコレクション。 テレメトリ項目に関連付けられている指定された測定値を報告するには、このコレクションを使用します。 一般的なユース ケースは次のとおりです。
- 依存関係テレメトリのペイロードのサイズ
- 要求テレメトリによって処理されるキュー項目の数
- 顧客がウィザードの手順を完了するのにかかった時間 (手順完了イベント テレメトリ)

Application Analytics で[カスタム測定値](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)を照会できます。

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > カスタム測定値は、属するテレメトリ項目に関連付けられています。 その測定値を含むテレメトリ項目のサンプリング対象となります。 他のテレメトリの種類から独立した値を持つ測定値を追跡するには、[メトリック テレメトリ](../articles/azure-monitor/app/api-custom-events-metrics.md)を使用します。

キーの最大長:150
