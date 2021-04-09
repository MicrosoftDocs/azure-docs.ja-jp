---
title: モデル変数 - Azure Time Series Insights Gen2 | Microsoft Docs
description: モデル変数
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: fd08dc98fa47617bbc7c8d1fff895377837a7327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736736"
---
# <a name="time-series-model-variables"></a>時系列モデルの変数

この記事では、イベントに関する式と計算ルールを指定する時系列モデルの変数について説明します。

各変数には、"*数値*"、"*カテゴリ別*"、および "*集計*" の 3 種類のうちのいずれかを指定できます。

* **数値** の種類では、連続する数値が処理されます。
* **カテゴリ別** の種類は、定義されている不連続値のセットを処理します。
* **集計** の種類では、1 種類の複数の変数 (すべて数値、またはすべてカテゴリ別) が結合されます。

次の表に、各変数の種類に関連するプロパティを示します。

[![タイム シリーズ モデルの変数テーブル](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>数値変数

| 変数のプロパティ | 説明 |
| --- | ---|
| 変数のフィルター | フィルターは、計算対象として考慮する行の数を制限するための、オプションの条件付きの句です。 |
| 変数の値 | デバイスまたはセンサーからの計算、またはタイム シリーズ式を使用して変換された計算に使用されるテレメトリ値。 数値の種類の変数は、*Double* 型である必要があります。|
| 変数の補間 | 補間は、既存のデータを使用してシグナルを再構築する方法を指定します。 *ステップ* と *線形* 補間オプションを数値変数に使用できます。 |
| 変数の集計 | [数値変数の種類に対してサポートされている集計関数](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind)を使用した計算を実行します。 |

変数は、次の JSON の例に準拠しています。

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>カテゴリ別変数

| 変数のプロパティ | 説明 |
| --- | ---|
| 変数のフィルター | フィルターは、計算対象として考慮する行の数を制限するための、オプションの条件付きの句です。 |
| 変数の値 | デバイスまたはセンサーからの計算に使用されるテレメトリ値。 カテゴリ別の種類の変数は、*Long* または *String* である必要があります。 |
| 変数の補間 | 補間は、既存のデータを使用してシグナルを再構築する方法を指定します。 *ステップ* 補間オプションは、カテゴリ別変数に使用できます。 |
| 変数のカテゴリ | カテゴリによって、デバイスまたはセンサーからの値とラベルの間のマッピングが作成されます。 |
| 変数の既定のカテゴリ | 既定のカテゴリは、"categories" プロパティでマップされていないすべての値を対象としています。 |

変数は、次の JSON の例に準拠しています。

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>集計変数

| 変数のプロパティ | 説明 |
| --- | ---|
| 変数のフィルター | フィルターは、計算対象として考慮する行の数を制限するための、オプションの条件付きの句です。 |
| 変数の集計 | [集計変数の種類に対してサポートされている集計関数](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind)を使用した計算を実行します。 |

変数は、次の JSON の例に準拠しています。

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

変数は、時系列モデルの型定義に格納され、API を使用してインラインで指定して、格納されている定義をオーバーライドしたり補完したりできます。

## <a name="next-steps"></a>次のステップ

* [時系列モデル](./concepts-model-overview.md)の詳細を確認します。

* [クエリ API](./concepts-query-overview.md) を使用してインラインで変数を定義する方法の詳細を確認します。
