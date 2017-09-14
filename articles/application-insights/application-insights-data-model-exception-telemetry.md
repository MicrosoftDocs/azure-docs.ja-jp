---
title: "Azure Application Insights Telemetry のデータ モデル - 例外テレメトリ | Microsoft Docs"
description: "例外テレメトリ用の Application Insights データ モデル"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 49d2429c01fe3fde2e51e4344904f55e43eea1cd
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017

---
# <a name="exception-telemetry-application-insights-data-model"></a>例外テレメトリ: Application Insights データ モデル

[Application Insights](app-insights-overview.md)で、例外のインスタンスは、監視対象のアプリケーションの実行中に発生したハンドルされるまたはハンドルされない例外を表します。

## <a name="problem-id"></a>問題 ID

コードで例外がスローされた場所の識別子。 例外をグループ化するために使用されます。 通常は、例外の種類と呼び出し履歴の関数の組み合わせです。

最大長: 1,024 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="exception-details"></a>例外の詳細

(拡張予定)

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>次のステップ

- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- [Application Insights で Web アプリの例外を診断する](app-insights-asp-net-exceptions.md)方法を確認します。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。

