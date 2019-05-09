---
title: Azure Application Insights Telemetry のデータ モデル - 例外テレメトリ | Microsoft Docs
description: 例外テレメトリ用の Application Insights データ モデル
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
ms.openlocfilehash: efd7ad43ee9a2206f474621612eca7dfe5079f99
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117098"
---
# <a name="exception-telemetry-application-insights-data-model"></a>例外テレメトリ:Application Insights データ モデル

[Application Insights](../../azure-monitor/app/app-insights-overview.md)で、例外のインスタンスは、監視対象のアプリケーションの実行中に発生したハンドルされるまたはハンドルされない例外を表します。

## <a name="problem-id"></a>問題 ID

コードで例外がスローされた場所の識別子。 例外をグループ化するために使用されます。 通常は、例外の種類と呼び出し履歴の関数の組み合わせです。

最大長:1024 文字

## <a name="severity-level"></a>重大度レベル

重大度レベル。 値は、`Verbose``Information``Warning``Error` および `Critical` が可能です。

## <a name="exception-details"></a>例外の詳細

(拡張予定)

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>次の手順

- Application Insights の型とデータ モデルについては、[データ モデル](data-model.md)に関するページを参照してください。
- [Application Insights で Web アプリの例外を診断する](../../azure-monitor/app/asp-net-exceptions.md)方法を確認します。
- Application Insights でサポートされている[プラットフォーム](../../azure-monitor/app/platforms.md)を確認します。
