---
title: タイム シリーズ モデル - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 のタイム シリーズ モデルについて学習します。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 18212bf92304e75c702c51ff12628cd670755bb0
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855206"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 のタイム シリーズ モデル

この記事ではタイム シリーズ モデルに関して、機能、および Azure Time Series Insights Gen2 環境で独自のモデルのビルドと更新を開始する方法について説明します。

> [!TIP]
>
> * タイム シリーズ モデルのライブによる実例については、 [Contoso Wind Farm デモ](https://insights.timeseries.azure.com/preview/samples)環境を参照してください。
> * Azure Time Series Insights TSI Explorer を使用して、[タイム シリーズ モデルを操作する方法](/azure/time-series-insights/how-to-edit-your-model)について学習します。

## <a name="summary"></a>まとめ

これまで、IoT デバイスから収集されたデータにはコンテキスト情報が不足していたため、センサーをすばやく検索して分析することが困難でした。 タイム シリーズ モデルの主な目的は、IoT データまたはタイム シリーズ データの検索と分析を簡素化することです。 この目的は、時系列データのキュレーション、メンテナンス、エンリッチメントを可能にし、分析用のコンシューマー対応データセットを簡単に準備できるようにすることで達成されます。

## <a name="scenario-contosos-new-smart-oven"></a>シナリオ:Contoso の新しいスマート オーブン

**Contoso のスマート オーブンについての架空のシナリオを考えてみましょう。** このシナリオでは、各 Contoso スマート オーブンには 5 つの温度センサーがあり、4 つのトップ バーナーのそれぞれに 1 つずつと、オーブン自体に 1 つあると仮定します。 最近までは、各 Contoso の温度センサーは、データを個別に送信、格納、視覚化していました。 キッチンのアプライアンス監視では、Contoso は個々のセンサーごとに 1 つの基本的なグラフを使用していました。

Contoso は初期データと視覚化ソリューションに満足していましたが、いくつかの制限が明らかになっています。

* お客様は、トップ バーナーのほとんどがオンになったときに、オーブン全体がどの程度加熱されるかを知りたいと考えました。 Contoso にとって、オーブン全体の状況について統一された回答を分析して提示するのは困難でした。
* Contoso のエンジニアは、トップ バーナーの同時稼働により、効率の悪い電力消費が起こらないことを確認する必要がありました。 どの温度センサーと電圧センサーが相互に関連付けられているか、およびそれらをストア内で特定する方法について、相互参照が困難でした。
* Contoso 品質保証チームは、2 つのセンサー バージョン間の履歴を監査し、比較する必要がありました。 どのデータがどのセンサー バージョンに属しているかを判断するのは困難でした。

包括的なスマート オーブン タイム シリーズ モデルを構築、整理、および定義する機能がなければ、各温度センサーは切り離されて分離された、情報が少ないデータ ポイントとなります。 これらのデータ ポイントをアクション可能な洞察に変えることは、各データセットが互いに独立しているため、より困難です。

これらの制限により、Contoso の新しいオーブンに付随するスマート データ集計および視覚化ツールの重要性が明らかになります。

* データの視覚化は、データを関連付けて便利なビューに結合できる場合に便利です。 1 つの例として、電圧センサーと温度センサーを示しています。
* 複数のエンティティの多次元データを比較、ズーム、時間範囲の機能と共に管理することは実現困難な場合があります。

この架空の例で発生する多くのシナリオに対して、**タイム シリーズ モデルは便利なソリューションを提供します**。

[![タイム シリーズ モデルのスマート オーブン グラフの例](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* タイム シリーズ モデルは、クエリとナビゲーションで重要な役割を果たします。これは、時間の範囲やセンサーとデバイスの種類の間で比較を描画できるようにすることで、データをコンテキスト化するためです。 (**A**)
* タイム シリーズ モデルで保持されるデータは、タイム シリーズ クエリの計算を変数として保持し、クエリ時にそれらを再利用するため、データはさらにコンテキスト化します。
* タイム シリーズ モデルは、視覚化と管理能力を向上させるためにデータを整理して集計します。 (**B**)

### <a name="key-capabilities"></a>主な機能

タイム シリーズ データのコンテキスト化をシンプルかつ簡単に管理できるようにするため、タイム シリーズ モデルでは、Time Series Insights Gen2 で次の機能が使用できるようになっています。 これは以下のことに役立ちます。

* スカラー関数を活用した計算や数式の作成と管理、操作の集計などを行う。
* 親子関係を定義して、ナビゲーション、検索、および参照を有効にする。
* *インスタンス フィールド*として定義されたインスタンスに関連付けられたプロパティを定義し、それらを使用して階層を作成する。

### <a name="components"></a>コンポーネント

タイム シリーズ モデルには、次の 3 つの主要なコンポーネントがあります。

* [タイム シリーズ モデルのインスタンス](#time-series-model-instances)
* [タイム シリーズ モデルの階層](#time-series-model-hierarchies)
* [タイム シリーズ モデルの型](#time-series-model-types)

これらのコンポーネントは、タイム シリーズ モデルを指定し、データを整理するために組み合わされています。

[![タイム シリーズ モデルの概要グラフ](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

タイム シリーズ モデルは、[Azure Time Series Insights TSI Explorer](/azure/time-series-insights/concepts-model-overview) を使用して作成および管理できます。 タイム シリーズ モデルの設定は、[モデルの設定 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis) を使用して管理できます。

## <a name="time-series-model-instances"></a>タイム シリーズ モデルのインスタンス

タイム シリーズ モデルの *インスタンス* は、時系列自体の仮想表現です。

ほとんどの場合、インスタンスは、時系列 ID として保存されている **deviceId** または **assetId**によって一意に識別されます。

インスタンスには、時系列 ID、型、名前、説明、階層、インスタンス フィールドなどの*インスタンス プロパティ*と呼ばれる説明情報が含まれています。 インスタンス プロパティには、少なくとも階層情報が含まれます。

*インスタンス フィールド*は、階層レベルの値、製造元、演算子などを含めることができる説明情報のコレクションです。

Azure Time Series Insights Gen2 環境に対してイベント ソースが構成された後、インスタンスが自動的に検出され、タイム シリーズ モデルで作成されます。 インスタンスは、タイム シリーズ モデルのクエリを使用して Azure Time Series Insights TSI Explorer で作成または更新できます。

[Contoso Wind Farm デモ](https://insights.timeseries.azure.com/preview/samples)には、いくつかのライブ インスタンスの例が用意されています。

[![タイム シリーズ モデルのインスタンスの例](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>インスタンスのプロパティ

インスタンスは、**timeSeriesId**、**typeId**、**name**、**description**、**hierarchyIds**、および **instanceFields** によって定義されます。 各インスタンスは、1 つのみの*型*、および 1 つ以上の*階層*にマップされます。

| プロパティ | 説明 |
| --- | ---|
| timeSeriesId | インスタンスが関連付けられている時系列の一意の ID。 ほとんどの場合、インスタンスは、deviceId や assetId などのプロパティによって一意に識別されます。 場合によっては、最大 3 つのプロパティを組み合わせたより具体的な複合 ID を使用できます。 |
| typeId | インスタンスが関連付けられているタイム シリーズ モデルの種類の一意の文字列 ID (大文字と小文字を区別します)。 既定では、検出されたすべての新しいインスタンスは既定の型に関連付けられます。
| name | **name** プロパティは、オプションであり、大文字小文字が区別されます。 **name** が使用できない場合は、既定では **timeSeriesId** になります。 名前が指定されている場合は、**timeSeriesId** は [ウェル](time-series-insights-update-explorer.md#4-time-series-well)でも使用できます。 |
| description | インスタンスの説明テキスト。 |
| hierarchyIds | インスタンスが属する階層を定義します。 |
| instanceFields | インスタンスのプロパティ、およびインスタンスを定義する任意の静的データ。 これらによって、階層プロパティや非階層プロパティの値が定義されます。また、検索操作を実行するためのインデックスを作成することもできます。 |

> [!NOTE]
> 階層は、インスタンス フィールドを使用して構築されます。 その他のインスタンス プロパティの定義には、別の **instanceFields** を追加できます。

インスタンスには、次の JSON 表現があります。

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> インスタンス API と作成、読み取り、更新、削除 (CRUD) のサポートについては、[データのクエリ](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)に関する記事と、[インスタンス API REST に関するドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api)を参照してください。

## <a name="time-series-model-hierarchies"></a>タイム シリーズ モデルの階層

タイム シリーズ モデルの*階層*を使用すると、プロパティ名とそのリレーションシップを指定して、インスタンスを整理できます。

特定の Azure Time Series Insights Gen2 環境で複数の階層を構成できます。 タイム シリーズ モデルのインスタンスは、単一の階層または複数の階層にマップできます (多対多のリレーションシップ)。

[Contoso Wind Farm デモ](https://insights.timeseries.azure.com/preview/samples)には、標準のインスタンスと型の階層が表示されます。

[![タイム シリーズ モデルの階層の例](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>階層の定義

階層は、階層の **id**、**名前**、および **source** によって定義されます。

| プロパティ | 説明 |
| ---| ---|
| id | 階層の一意識別子。たとえば、インスタンスを定義するときに使用されます。 |
| name | 階層の名前を指定するために使用される文字列。 |
| source | 組織階層またはパスを指定します。これは、ユーザーが作成する階層の上から下までの親子の順序です。 親子関係のプロパティは、インスタンス フィールドにマップします。 |

階層は、JSON で次のように表されます。

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

前の JSON の例では、次のようになります。

* `Location` は親である `states` と子である `cities` を持つ階層を定義します。 各 `location` は複数の `states` を持つことができ、それぞれさらに複数の `cities` を持つことができます。
* `ManufactureDate` は親である `year` と子である `month` を持つ階層を定義します。 各 `ManufactureDate` は複数の `years` を持つことができ、それぞれさらに複数の `months` を持つことができます。

> [!TIP]
> 階層 API と作成、読み取り、更新、削除 (CRUD) のサポートについては、[データのクエリ](concepts-query-overview.md#time-series-model-query-tsm-q-apis)に関する記事と、[階層 API REST に関するドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api)を参照してください。

### <a name="hierarchy-example"></a>階層の例

例を考えてみます。ここで、階層 **H1** が `building`、`floor`、および `room` を **instanceFieldNames** 定義の一部として持つとします。

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

前の定義で使用されているインスタンス フィールドといくつかの時系列によって、階層の属性と値が次の表に示すように表されます。

| タイム シリーズ ID | インスタンス フィールド |
| --- | --- |
| ID1 | "building" = "1000"、"floor" = "10"、"room" = "55"  |
| ID2 | "building" = "1000"、"room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000"、"floor" = "10"  |
| ID5 | "building"、"floor"、"room" のどれも設定されていない。 |

タイム シリーズ **ID1** と **ID4** は、階層 **H1** の一部として [Azure Time Series Insights TSI Explorer](time-series-insights-update-explorer.md) に表示されます。それは完全に定義され、適切に並べられた *building*、*floor*、および *room* パラメーターを持つためです。

他のものは、指定されたデータ階層に準拠していないため、*親が未設定のインスタンス*に分類されます。

## <a name="time-series-model-types"></a>タイム シリーズ モデルの型

タイム シリーズ モデルの "*型*" は、計算を行うための変数や数式を定義するのに役立ちます。 型は特定のインスタンスに関連付けられています。

1 つの型は、1 つまたは複数の変数を持つことができます。 たとえば、タイム シリーズ モデル インスタンスの型が "*温度センサー*" の場合は、その型を、"*平均温度*"、"*最低温度*"、"*最高温度*" という変数から構成することができます。

[Contoso Wind Farm デモ](https://insights.timeseries.azure.com/preview/samples)は、それぞれのインスタンスに関連付けられている複数のタイム シリーズ モデルの型を視覚化します。

[![タイム シリーズ モデルの種類の例](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 型 API と作成、読み取り、更新、削除 (CRUD) のサポートについては、[データのクエリ](concepts-query-overview.md#time-series-model-query-tsm-q-apis)に関する記事と、[型 API REST に関するドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api)を参照してください。

### <a name="type-properties"></a>型のプロパティ

タイム シリーズ モデルの型は、**id**、**名前**、**description**、および **variables** によって定義されます。

| プロパティ | 説明 |
| ---| ---|
| id | 種類の一意の文字列 ID (大文字と小文字を区別します)。 |
| name | 型の名前を指定するために使用される文字列。 |
| description | 型の文字列の説明。 |
| variables | 型に関連付けられている変数を指定します。 |

型は、次の JSON の例に準拠しています。

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
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
      }
    }
  ]
}
```

タイム シリーズ モデルの型には、イベントに対する式および計算ルールを指定する多数の変数を含めることができます。 タイム シリーズ モデル変数を定義する方法の詳細については、[こちら](./concepts-variables.md)を参照してください

## <a name="next-steps"></a>次のステップ

* API を使用してモデルを編集する方法の詳細について、[タイム シリーズ モデル](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis)のリファレンス ドキュメントを参照します。

* [タイム シリーズ モデル変数](./concepts-variables.md)を使用して作成できる数式と計算を確認します

* Azure Time Series Insights Gen2 での[データのクエリ](concepts-query-overview.md)について学習します
