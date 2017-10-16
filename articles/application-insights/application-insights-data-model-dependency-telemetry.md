---
title: "Azure Application Insights Telemetry のデータ モデル - 依存関係テレメトリ | Microsoft Docs"
description: "依存関係テレメトリ用の Application Insights データ モデル"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: bwren
ms.openlocfilehash: 2e97c3f951f46c32802aea543b93d5ab1bb76228
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="dependency-telemetry-application-insights-data-model"></a>依存関係テレメトリ: Application Insights データ モデル

依存関係テレメトリ ([Application Insights](app-insights-overview.md)) は、監視対象のコンポーネントと、SQL や HTTP エンドポイントのようなリモート コンポーネントとのやりとりを表します。

## <a name="name"></a>名前

この依存関係呼び出しで開始されたコマンドの名前。 小さな基数の値です。 例として、ストアド プロシージャ名と URL パス テンプレートがあります。

## <a name="id"></a>ID

依存関係呼び出しインスタンスの識別子。 この依存関係の呼び出しに対応する要求テレメトリ項目の相関付けに使用されます。 詳細については、[相関付け](application-insights-correlation.md)に関するページを参照してください。

## <a name="data"></a>データ

この依存関係呼び出しによって開始されるコマンド。 例: すべてのクエリ パラメーターを使用する SQL ステートメントと HTTP URL。

## <a name="type"></a>型

依存関係の種類の名前。 依存関係を論理的にグループ化するための小さな基数の値と、commandName や resultCode のようなフィールドの変換です。 例: SQL、Azure テーブル、HTTP。

## <a name="target"></a>ターゲット

依存関係呼び出しのターゲット サイト。 例: サーバー名、ホスト アドレス。 詳細については、[相関付け](application-insights-correlation.md)に関するページを参照してください。

## <a name="duration"></a>時間

`DD.HH:MM:SS.MMMMMM` 形式の要求時間。 `1000` 日未満である必要があります。

## <a name="result-code"></a>結果コード

依存関係呼び出しの結果コード。 例: SQL エラー コードと HTTP 状態コード。

## <a name="success"></a>成功

呼び出しの成功または失敗を示す値。

## <a name="custom-properties"></a>カスタム プロパティ

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>カスタム測定値

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>次のステップ

- [.NET](app-insights-asp-net-dependencies.md) の依存関係追跡を設定します。
- [Java](app-insights-java-agent.md) の依存関係追跡を設定します。
- [カスタム依存関係テレメトリを記述します](app-insights-api-custom-events-metrics.md#trackdependency)。
- Application Insights の型とデータ モデルについては、[データ モデル](application-insights-data-model.md)に関するページを参照してください。
- Application Insights でサポートされている[プラットフォーム](app-insights-platforms.md)を確認します。
