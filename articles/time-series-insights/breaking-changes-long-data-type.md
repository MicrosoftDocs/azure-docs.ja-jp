---
title: Azure Time Series Insights Gen2 での Long データ型のサポート | Microsoft Docs
description: Azure Time Series Insights Gen2 での Long データ型のサポート。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780826"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 での Long データ型のサポートの追加

Long データ型のサポートの追加は、Azure Time Series Insights Gen2 環境でのみ、数値データを格納およびインデックスを付ける方法に影響します。 Gen1 環境がある場合は、これらの変更を無視することができます。

お客様のリージョンに応じて、2020 年 6 月 29 日または 6 月 30 日以降、データは **Long** および **Double** としてインデックス付けされるようになります。  この変更に関してご不明な点や懸念事項がある場合は、Azure portal を通じてサポート チケットを送信し、この情報を伝えてください。

次のいずれかのケースの影響を受ける場合は、推奨される変更を行ってください。

- **ケース 1**:現在タイム シリーズ モデル変数を使用していて、テレメトリ データで整数データ型のみを送信する。
- **ケース 2**:現在タイム シリーズ モデル変数を使用していて、テレメトリ データで整数データ型と非整数データ型の両方を送信する。
- **ケース 3**:カテゴリ変数を使用して、整数値をカテゴリにマップする。
- **ケース 4**:JavaScript SDK を使用して、カスタム フロントエンド アプリケーションを構築する。
- **ケース 5**:ウォーム ストアの 1,000 プロパティの名前制限に近づいており、整数データと非整数データの両方を送信している。 プロパティの数は、[Azure portal](https://portal.azure.com/) でメトリックとして表示できます。

いずれかのケースに該当する場合は、モデルに変更を加えてください。 変数定義の Time Series の式 (TSX) を、推奨される変更で更新します。 両方を更新します。

- Azure Time Series Insights Explorer
- Microsoft の API を使用する任意のカスタム クライアント

IoT ソリューションと制約によっては、Azure Time Series Insights Gen2 環境に送信されるデータを表示できない場合があります。 データが整数のみであるか、整数と非整数の両方であるかがわからない場合は、いくつかのオプションがあります。

- 機能がリリースされるまで待つことができます。 その後、エクスプローラー UI で未加工のイベントを調査して、2 つの別々の列に保存されているプロパティを把握することができます。
- 事前にすべての数値タグに対して推奨される変更を行うことができます。
- イベントのサブセットをストレージに一時的にルーティングして、スキーマについて十分に理解して調査することができます。

イベントを保存するには、Azure Event Hubs に対して[イベント キャプチャ](../event-hubs/event-hubs-capture-overview.md)を有効にするか、IoT Hub から Azure Blob Storage に[ルーティング](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)します。

データは、[Event Hub Explorer](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer) を使用して、または[イベント プロセッサ ホスト](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events)を使用して監視することもできます。

IoT Hub を使用する場合、組み込みのエンドポイントへのアクセス方法については、「[デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](../iot-hub/iot-hub-devguide-messages-read-builtin.md)」を参照してください。

> [!NOTE]
> 推奨される変更を加えないと、中断が発生する可能性があります。 たとえば、クエリ API または Time Series Insights エクスプローラーを介してアクセスされる、影響を受ける Time Series Insights 変数によって、**null** が返されます (つまり、エクスプローラーにデータが表示されません)。

## <a name="recommended-changes"></a>推奨される変更

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>ケース 1: タイム シリーズ モデル変数を使用していて、テレメトリ データで整数データ型のみを送信している

ケース 1 の推奨される変更は、ケース 2 の場合と同じです。 ケース 2 のセクションの指示に従ってください。

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>ケース 2: タイム シリーズ モデル変数を使用していて、テレメトリ データで整数型と非整数型の両方を送信している

現在、整数のテレメトリ データを送信している場合、データは次の 2 つの列に分割されます。

- **propertyValue_double**
- **propertyValue_long**

整数データは **propertyValue_long** に書き込まれます。 以前に **propertyValue_double** に取り込まれた (および今後取り込まれる) 数値データはコピーされません。

**propertyValue** プロパティに対してこれら 2 つの列のデータのクエリを実行する場合は、TSX で **coalesce()** スカラー関数を使用する必要があります。 この関数によって、同じ **DataType** の引数が受け取られ、引数リスト内の最初の null 以外の値が返されます。 詳細については、[Azure Time Series Insights Gen2 データ アクセスの概念](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)に関するページを参照してください。

#### <a name="variable-definition-in-tsx---numeric"></a>TSX の変数定義 - 数値

*前の変数の定義:*

[![スクリーンショットでは、数値の PropertyValue 変数に対する [新しい変数の追加] ダイアログ ボックスが示されています。](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新しい変数の定義:*

[![スクリーンショットでは、数値のカスタム値の PropertyValue 変数に対する [新しい変数の追加] ダイアログ ボックスが示されています。](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** を、カスタムの [Time Series 式](/rest/api/time-series-insights/reference-time-series-expression-syntax)として使用することもできます。

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>TSX クエリ API を使用したインライン変数の定義 - 数値

*前の変数の定義:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*新しい変数の定義:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** を、カスタムの [Time Series 式](/rest/api/time-series-insights/reference-time-series-expression-syntax)として使用することもできます。

> [!NOTE]
> これらの変数は、使用されている可能性があるすべての場所で更新することをお勧めします。 このような場所には、タイム シリーズ モデル、保存されたクエリ、Power BI コネクタのクエリなどがあります。

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>ケース 3: カテゴリ変数を使用した整数値のカテゴリへのマッピング

現時点で整数値をカテゴリにマップするカテゴリ変数を使用している場合は、**toLong** 関数を使用して **Double** 型から **Long** 型にデータを変換している可能性があります。 ケース 1 および 2 と同様に、**Double** と **Long** の **DataType** 列を結合する必要があります。

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Time Series エクスプローラーでの変数の定義 - カテゴリ

*前の変数の定義:*

[![スクリーンショットでは、カテゴリの PropertyValue 変数に対する [新しい変数の追加] ダイアログ ボックスが示されています。](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新しい変数の定義:*

[![スクリーンショットでは、カテゴリのカスタム値の PropertyValue 変数に対する [新しい変数の追加] ダイアログ ボックスが示されています。](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

**coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** を、カスタムの [Time Series 式](/rest/api/time-series-insights/preview#time-series-expression-and-syntax)として使用することもできます。

カテゴリ変数では、値を整数型にする必要があります。 **coalesce()** 内のすべての引数の **DataType** は、カスタムの [Time Series 式](/rest/api/time-series-insights/reference-time-series-expression-syntax)で **Long** 型である必要があります。

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>TSX クエリ API を使用したインライン変数の定義 - カテゴリ

*前の変数の定義:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*新しい変数の定義:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

カテゴリ変数では、値を整数型にする必要があります。 **coalesce()** 内のすべての引数の **DataType** は、カスタムの [Time Series 式](/rest/api/time-series-insights/reference-time-series-expression-syntax)で **Long** 型である必要があります。

> [!NOTE]
> これらの変数は、使用されている可能性があるすべての場所で更新することをお勧めします。 このような場所には、タイム シリーズ モデル、保存されたクエリ、Power BI コネクタのクエリなどがあります。

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>ケース 4:JavaScript SDK を使用したカスタム フロントエンド アプリケーションの構築

ケース 1 - 3 の影響を受けていて、カスタム アプリケーションを構築する場合は、前の例で示したように、**coalesce()** 関数を使用するようにクエリを更新する必要があります。

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>ケース 5: Warm ストアの 1,000 個のプロパティの上限に近づいている

多数のプロパティがあるウォーム ストアのユーザーで、この変更によって環境が 1,000 個のウォーム ストアのプロパティ名の制限を超える可能性がある場合は、Azure portal からサポート チケットを送信し、この通信を伝えてください。

## <a name="next-steps"></a>次のステップ

- [サポートされているデータ型](concepts-supported-data-types.md)の完全な一覧を表示します。
