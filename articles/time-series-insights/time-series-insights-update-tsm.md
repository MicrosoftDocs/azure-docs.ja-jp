---
title: Azure Time Series Insights プレビューでのタイム シリーズ モデル | Microsoft Docs
description: Azure Time Series Insights のタイム シリーズ モデルについて。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6126a9d56059435be32299bcd1f03050f031f81b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507819"
---
# <a name="time-series-model"></a>タイム シリーズ モデル

この記事では、Azure Time Series Insights プレビューのタイム シリーズ モデル部分について説明します。 モデル自体やその機能について説明すると共に、独自のモデルの構築や更新を開始する方法についても説明します。

これまで、IoT デバイスから収集されたデータにはコンテキスト情報が不足していたため、センサーをすばやく検索して分析することが困難でした。 タイム シリーズ モデルの主な目的は、IoT データの検索と分析を簡素化することです。 この目的は、時系列データのキュレーション、メンテナンス、エンリッチメントを可能にし、コンシューマー対応データセットを簡単に準備できるようにすることで達成されます。 

タイム シリーズ モデルは、クエリやナビゲーションにおいて重要な役割を果たします。デバイス エンティティや非デバイス エンティティをコンテキスト化することができるためです。 タイム シリーズ モデル内で保持されているデータについては、それらに格納された数式を利用できるので、時系列クエリの計算が強化されます。

![tsm][1]

## <a name="key-capabilities"></a>主な機能

時系列データのコンテキスト化をシンプルかつ簡単に管理できるようにするため、タイム シリーズ モデルでは、Time Series Insights プレビューで次の機能が使用できるようになっています。 これは以下のことに役立ちます。

* 計算や数式の作成と管理、スカラー関数を活用したデータの変換、操作の集計などを行う。

* 親子関係を定義してナビゲーションと参照を可能にし、時系列テレメトリにコンテキストを提供する。

* "*インスタンス フィールド*" のインスタンス部分に関連付けられたプロパティを定義し、それらを使用して階層を作成する。

## <a name="times-series-model-key-components"></a>タイム シリーズ モデルの主要コンポーネント

タイム シリーズ モデルには、次の 3 つの主要コンポーネントがあります。

* タイム シリーズ モデルの "*型*"
* タイム シリーズ モデルの "*階層*"
* タイム シリーズ モデルの "*インスタンス*"

## <a name="time-series-model-types"></a>タイム シリーズ モデルの型

タイム シリーズ モデルの "*型*" は、計算を行うための変数や数式を定義するのに役立ちます。 型は、特定の Time Series Insights インスタンスに関連付けられます。 1 つの型は、1 つまたは複数の変数を持つことができます。 たとえば、Time Series Insights インスタンスの型が "*温度センサー*" の場合は、その型を、"*平均温度*"、"*最低温度*"、"*最高温度*" という変数から構成することができます。 Time Series Insights はデータが最初に送信される際に、既定の型を作成します。 既定の型は、モデル設定から取得したり更新することができます。 既定の型には、イベントの数をカウントする変数が含まれています。

## <a name="time-series-model-type-json-example"></a>タイム シリーズ モデルの型の JSON サンプル

サンプル:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

タイム シリーズ モデルの種類について詳しくは、[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)をご覧ください。

## <a name="variables"></a>変数

Time Series Insights の型には変数があります。これは、イベントからの値に対する名前付き計算です。 Time Series Insights の変数の定義には、数式と計算の規則が含まれます。 変数の定義には、"*種類*"、"*値*"、"*フィルター*"、"*削減*"、および "*境界*" が含まれます。 変数は、タイム シリーズ モデル内の型の定義に保存されます。クエリ API を通じて変数をインラインで指定すると、保存されている定義をオーバーライドすることができます。

次の表は、変数の定義の凡例です。

![table][2]

### <a name="variable-kind"></a>変数の種類

次の種類の変数がサポートされています。

* *数値*
* *集計*

### <a name="variable-filter"></a>変数のフィルター

変数のフィルターでは、計算で考慮される行の数を条件に応じて制限するための、オプションのフィルター句を指定します。

### <a name="variable-value"></a>変数の値

変数の値は計算で使用されます。 これは、参照するイベント内の列です。

### <a name="variable-aggregation"></a>変数の集計

変数の集計関数を使用すると、一部の計算が実行可能になります。 Time Series Insights では、通常の集計がサポートされています ("*最小*"、"*最大*"、"*平均*"、"*合計*"、"*カウント*")。

## <a name="time-series-model-hierarchies"></a>タイム シリーズ モデルの階層

階層を使用すると、プロパティ名とそのリレーションシップを指定して、インスタンスを整理できます。 階層は 1 つまたは複数使用できます。 階層はデータの現行部分である必要はありませんが、各インスタンスは特定の階層にマップする必要があります。 タイム シリーズ モデルのインスタンスは、1 つまたは複数の階層にマップできます。

階層は、"*階層 ID*"、"*名前*"、および "*ソース*" によって定義されます。 階層にはパスがあります。これは、ユーザーが作成する階層の、上から下までの親子の順序を示すものです。 親子関係のプロパティは、"*インスタンス フィールド*" にマップします。

### <a name="time-series-model-hierarchy-json-example"></a>タイム シリーズ モデルの階層の JSON サンプル

サンプル:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

タイム シリーズ モデルの階層について詳しくは、[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)をご覧ください。

### <a name="hierarchy-definition-behavior"></a>階層定義の動作

次の例では、階層 H1 の定義に、*building* (建物)、*floor* (フロア)、*room* (部屋) が含まれています。

```plaintext
 H1 = [“building”, “floor”, “room”]
```

階層の属性と値は、"*インスタンス フィールド*" に応じて、次の表のように表示されます。

| タイム シリーズ ID | インスタンス フィールド |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | "building"、"floor"、"room" のどれも設定されていない |

上記の例の場合、ID1 と ID4 は Azure Time Series Insights エクスプローラーで階層 H1 の一部として表示され、残りの階層は、指定されたデータ階層に一致しないため、"*親のないインスタンス*" に分類されます。

## <a name="time-series-model-instances"></a>タイム シリーズ モデルのインスタンス

インスタンスは、タイム シリーズ自体を表します。 ほとんどの場合、*deviceId* または *assetId* が、環境内での資産の一意識別子となります。 インスタンスには説明情報が関連付けられます。これは、インスタンス プロパティと呼ばれます。 インスタンス プロパティには、少なくとも階層情報が含まれます。 製造元、運用者、最終保守日など、有用な説明データを含めることもできます。

インスタンスは、*typeId*、*timeSeriesId*、*name*、*description*、*hierarchyIds*、および *instanceFields* によって定義されます。 各インスタンスは、1 つの "*種類*" だけにマップし、1 つまたは複数の階層を持ちます。 インスタンスは、階層からすべてのプロパティを継承します。*instanceFields* を追加して、インスタンス プロパティの定義を拡張することもできます。

*instanceFields* は、インスタンスのプロパティと、インスタンスを定義する任意の静的データです。 これらによって、階層プロパティや非階層プロパティの値が定義されます。また、検索操作を実行するためのインデックスを作成することもできます。

*name* プロパティは、オプションであり、大文字小文字が区別されます。 *name* は、使用できない場合、既定でタイム シリーズ ID になります。 *name* を指定しても、タイム シリーズ ID も Well で使用可能になります (エクスプローラーのチャートの下のグリッド)。 

## <a name="time-series-model-instance-json-example"></a>タイム シリーズ モデルのインスタンスの JSON サンプル

サンプル:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

タイム シリーズ モデルのインスタンスについて詳しくは、[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)をご覧ください。

## <a name="time-series-model-settings-example"></a>タイム シリーズ モデルの設定の例

サンプル:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

タイム シリーズ モデルの設定について詳しくは、[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)をご覧ください。

## <a name="next-steps"></a>次の手順

「[Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)」をご覧ください。

新しい[タイム シリーズ モデル](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)をご覧ください。

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
