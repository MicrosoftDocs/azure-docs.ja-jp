---
title: 新しい Azure Time Series Insights Gen2 API バージョンへの移行 | Microsoft Docs
description: 一般公開されている新しいバージョンを使用するように Azure Time Series Insights Gen2 環境を更新する方法。
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97740557"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>新しい Azure Time Series Insights Gen2 API バージョンへの移行

## <a name="overview"></a>概要

パブリック プレビュー段階のとき (2020 年 7 月 16 日より前) に Azure Time Series Insights Gen2 環境を作成していた場合は、この記事で説明されている手順に従って、一般公開されている新しいバージョンの API を使用するように TSI 環境を更新してください。 この変更は、Azure Time Series Insights の Gen1 バージョンを使用しているユーザーには影響しません。

> [!IMPORTANT]
> この記事に記載されている更新では、お使いの TSI 環境で使用されている API バージョンのみがアップグレードされます。 この変更は、Gen2 環境向けに導入された新しい [JSON のフラット化とエスケープの規則](./concepts-json-flattening-escaping-rules.md) とは関係ありません。

新しい API バージョンは `2020-07-31` であり、更新された[タイム シリーズ式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax)を使用します。

ユーザーは、それぞれの環境の[タイム シリーズ モデル変数](./concepts-variables.md)、保存されたクエリ、Power BI のクエリ、および API エンドポイントの呼び出しを行うすべてのカスタム ツールを移行する必要があります。 移行プロセスに関してご不明な点や懸念事項がある場合は、Azure portal を通じてサポート チケットを送信し、この情報をお伝えください。

> [!IMPORTANT]
> プレビュー API バージョン `2018-11-01-preview` は、2020 年 10 月 31 日までは引き続きサポートされます。 それまでにこの移行の適用可能な手順をすべて完了して、サービスの中断が起きないようにしてください。

## <a name="migrate-time-series-model-and-saved-queries"></a>タイム シリーズ モデルと保存されたクエリを移行する

ユーザーがそれぞれの[タイム シリーズ モデル変数](./concepts-variables.md)と保存されたクエリを移行できるように、[Azure Time Series Insights Explorer](https://insights.timeseries.azure.com) から使用できる組み込みツールがあります。 移行する環境に移動して、以下の手順に従ってください。 **移行を部分的に実行し、後で戻って完了することができます。ただし、どの更新も元に戻すことはできません。**

> [!NOTE]
> タイム シリーズ モデルと保存されたクエリを更新するには、その環境の共同作成者である必要があります。 共同作成者でない場合は、個人が保存したクエリのみを移行できます。 続行する前に、[環境のアクセス ポリシー](./concepts-access-policies.md)とお使いのアクセス レベルを確認してください。

1. タイム シリーズ モデル変数と保存されたクエリで使用された構文を更新するように、エクスプローラーによって求められます。

    [![プロンプト](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    誤って通知を閉じた場合は、通知パネルに表示されます。

1. **[Show Updates] (更新プログラムの表示)** をクリックして、移行ツールを開きます。

1. **[Download types] (型のダウンロード)** をクリックします。 移行によって現在の型が上書きされ、変数の構文が変更されるため、現在の型のコピーを保存するよう求められます。 型がダウンロードされると、ツールによって通知されます。

    [![型のダウンロード](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **[Update variables] (変数の更新)** をクリックします。 変数が更新されると、ツールによって通知されます。

    > [!IMPORTANT]
    > 型を更新する場合、古い API バージョン (`2018-11-01-preview`) を使用しているカスタム アプリケーションでは、新しい API バージョン (`2020-07-31`) を使用して作業を続行する必要があります。 使用している API のバージョンがわからない場合は、更新する前に管理者にご確認ください。 移行ツールを閉じて、後でこれらの手順に戻ることができます。 [カスタム アプリケーションの移行方法 ](#migrate-custom-applications)に関する詳細をご覧ください。

    [![変数の更新](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **[Update saved queries] (保存されたクエリの更新)** をクリックし ます。 保存されたクエリが更新されると、ツールによって通知されます。

    [![保存されたクエリの更新](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **[Done]** をクリックします。

    [![完了した移行](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

新しく作成された変数と保存されたクエリのいくつかをグラフ化して、更新された環境を確認します。 グラフ作成中に予期しない動作が発生した場合は、エクスプローラーのフィードバック ツールを使用してフィードバックをお送りください。

## <a name="migrate-power-bi-queries"></a>Power BI のクエリを移行する

Power BI コネクタを使用してクエリを生成していた場合、それらはプレビュー API バージョンと古いタイム シリーズ式の構文を使用して Azure Time Series Insights を呼び出しています。 プレビュー API が非推奨になるまで、これらのクエリは引き続きデータを正常に取得します。

新しい API バージョンと新しいタイム シリーズ式の構文を使用するようにクエリを更新するには、エクスプローラーからクエリを再生成する必要があります。 [Power BI コネクタを使用してクエリを作成する](./how-to-connect-power-bi.md)方法に関する詳細をご覧ください。

> [!NOTE]
> 2020 年 7 月バージョン以降の Power BI Desktop を使用する必要があります。 そうでない場合は、"invalid query payload version" (クエリのペイロード バージョンが無効です) エラーが表示されることがあります。

## <a name="migrate-custom-applications"></a>カスタム アプリケーションを移行する

カスタム アプリケーションで次の REST エンドポイントを呼び出している場合は、URI で API バージョンを `2020-07-31` に更新するだけで十分です。

- タイム シリーズ モデル API
  - モデルの設定 API
    - [Get](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [アップデート](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - インスタンス API:
    - [すべてのバッチ操作](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [一覧](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Search](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Suggest](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - 階層 API
    - [すべてのバッチ操作](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [一覧](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - 型 API
    - [Delete、Get 操作](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [一覧](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

次の REST エンドポイントについては、URI で API バージョンを `2020-07-31` に更新し、`tsx` プロパティのすべての出現箇所で、更新された[タイム シリーズ式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax)が使用されていることを確認する必要があります。

- 型 API
  - [Put 操作](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- クエリ API
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>例

#### <a name="typesbatchput"></a>TypesBatchPut

古い要求本文 (`2018-11-01-preview` で使用):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

更新された要求本文 (`2020-07-31` で使用):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

あるいは、`filter` を `$event.Mode.String = 'outdoor'` にすることもできます。 `value` では、角かっこを使用して特殊文字 (`_`) をエスケープする必要があります。

#### <a name="getevents"></a>GetEvents

古い要求本文 (`2018-11-01-preview` で使用):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

更新された要求本文 (`2020-07-31` で使用):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

あるいは、`filter` を `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` にすることもできます。

#### <a name="getseries"></a>GetSeries

古い要求本文 (`2018-11-01-preview` で使用):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

更新された要求本文 (`2020-07-31` で使用):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

あるいは、`value` を `$event['Bar-Pressure-Offset'].Double` にすることもできます。 データ型が指定されていない場合、データ型は常に Double と見なされます。 特殊文字 (`-`) をエスケープするには、角かっこ表記を使用する必要があります。

#### <a name="aggregateseries"></a>AggregateSeries

古い要求本文 (`2018-11-01-preview` で使用):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

更新された要求本文 (`2020-07-31` で使用):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

あるいは、`value` を `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` にすることもできます。

### <a name="potential-errors"></a>発生する可能性のあるエラー

#### <a name="invalidinput"></a>InvalidInput

次のエラーが表示される場合は、新しい API バージョン (`2020-07-31`) を使用していますが、TSX の構文が更新されていません。 [タイム シリーズ式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax)と、上記の移行の例を参照してください。 API 要求を再送信する前に、すべての `tsx` プロパティが正しく更新されていることを確認してください。

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>次の手順

- [Azure Time Series Insights Explorer](./concepts-ux-panels.md) またはカスタム アプリケーションを使用して環境をテストします。