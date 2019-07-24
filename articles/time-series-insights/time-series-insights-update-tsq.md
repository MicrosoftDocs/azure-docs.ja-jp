---
title: Azure Time Series Insights プレビューでのデータのクエリ | Microsoft Docs
description: Azure Time Series Insights プレビューでのデータのクエリです。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7885473d029556e10663675a9886c7ea3b9c709c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555423"
---
# <a name="data-querying"></a>データ照会

Azure Time Series Insights プレビューでは、パブリック サーフェス API を使用して、環境に格納されているイベントおよびメタデータのデータのクエリを実行できます。 これらの API は、[Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)でも使用されます。

Time Series Insights では、3 つの主要な API カテゴリを使用できます。

* **Environment API**:Time Series Insights 環境自体のクエリを行うことができます。 クエリの例は、呼び出し元がアクセス権を持っている環境や環境のメタデータの一覧です。

* **Time Series Model-Query (TSM-Q) API**:タイム シリーズ モデルの環境部分に格納されているメタデータの作成、読み取り、更新、削除操作を実行できます。 たとえば、インスタンス、種類、階層などです。

* **Time Series Query (TSQ) API**:ソース プロバイダーから記録されたイベント データを取得できます。 これらの API では、タイム シリーズ データの変換、結合、計算操作を実行できます。

[Time Series Expression (TSX) 言語](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)は、4 番目の強力なカテゴリです。 タイム シリーズ モデルを使用して、高度な計算を構成できます。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights プレビューのコア API

次のコア API がサポートされています。

![tsq][1]

### <a name="environment-apis"></a>Environment API

次の Environment API を使用できます。

* [Get Environment API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api):呼び出し元がアクセスを許可されている環境の一覧が返されます。
* [Get Environment Availability API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api):イベントのタイムスタンプ `$ts` に対するイベント数の分布が返されます。 この API は、イベントの数を返すことによって (ある場合) タイムスタンプにイベントが存在するかどうかを判断するのに役立ちます。
* [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api):特定の検索範囲に対するイベント スキーマのメタデータが返されます。 この API は、特定の検索範囲に対してスキーマで使用できるすべてのメタデータとプロパティを取得するのに役立ちます。

### <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model-Query (TSM-Q) API

次の Time Series Model-Query API を使用できます。

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api):環境の既定の種類とモデル名を取得および修正できます。
* [Types API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api):タイム シリーズの種類とそれに関連する変数に対して CRUD を実行できます。
* [Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api):タイム シリーズの階層とそれに関連するフィールド パスに対して CRUD を実行できます。
* [Instances API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api):タイム シリーズのインスタンスとそれに関連するインスタンス フィールドに対して CRUD を実行できます。

### <a name="time-series-query-tsq-apis"></a>Time Series Query (TSQ) API

次の Time Series Query API を使用できます。

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api):ソース プロバイダーから Time Series insights に記録されているイベントからの Time Series Insights データのクエリと取得を実行できます。

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api):ネットワーク上で記録されたデータを使用してキャプチャされたイベントからの Time Series Insights データのクエリと取得を実行できます。 返される値は、モデルで定義されているかインラインで提供されている変数に基づきます。

    >[!NOTE]
    > Aggregation 句は、モデルで指定されているかインラインで提供されていても無視されます。

  Get Series API では、各間隔の各変数に対するタイム シリーズの値が返されます。 タイム シリーズの値は、Time Series Insights でクエリからの出力 JSON に使用される形式です。 返される値は、タイム シリーズ ID と提供された変数のセットに基づきます。

* [Aggregate Series API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api):記録されたデータをサンプリングおよび集計することによって、キャプチャされたイベントからの Time Series Insights データのクエリと取得を実行できます。

  Aggregate Series API では、各間隔の各変数に対するタイム シリーズの値が返されます。 値は、タイム シリーズ ID と提供された変数のセットに基づきます。 Aggregate Series API では、タイム シリーズ モデルに格納されている変数またはインラインで提供された変数を使用してデータを集計またはサンプリングすることにより、削減が実現されます。

  サポートされている集計の種類は、`Min`、`Max`、`Sum`、`Count`、`Average` です

## <a name="next-steps"></a>次の手順

以下について読む。

- [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)
- [データ モデリング](./time-series-insights-update-tsm.md)
- [タイム シリーズ ID を選択するときのベスト プラクティス](./time-series-insights-update-how-to-id.md)

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
