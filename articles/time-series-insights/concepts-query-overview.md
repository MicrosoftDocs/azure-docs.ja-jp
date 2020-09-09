---
title: データのクエリ - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 のデータのクエリの概念と REST API の概要
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: cbf1c678307d76361e6083f4e2231fb9e48a112f
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855156"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 からのデータのクエリ

Azure Time Series Insights Gen2 では、パブリック サーフェス API を使用して、環境に格納されているイベントおよびメタデータのデータのクエリを実行できます。 これらの API は、[Azure Time Series Insights TSI Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer) でも使用されます。

Azure Time Series Insights Gen2 では、3 つの主要な API カテゴリを使用できます。

* **Environment API**:これらの API により、Azure Time Series Insights Gen2 環境自体に対してクエリを実行できます。 これらを使用すると、呼び出し元がアクセスできる環境の一覧と環境メタデータを収集できます。
* **Time Series Model-Query (TSM-Q) API**:環境のタイム シリーズ モデルに保管されるメタデータの作成、読み取り、更新、削除 (CRUD) 操作を実行できます。 これらを使用して、インスタンス、型、および階層にアクセスして編集できます。
* **Time Series Query (TSQ) API**:テレメトリまたはイベントのデータをソース プロバイダーからの記録時に取得できます。また、高度なスカラー関数および集計関数を使用して、データに対して計算および集計を実行できます。

Azure Time Series Insights Gen2 では、機能が豊富な文字列ベースの式言語である [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) を、[Time Series 変数](./concepts-variables.md)での計算の記述に使用します。

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure Time Series Insights Gen2 API の概要

次のコア API がサポートされています。

[![Time Series Query の概要](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

* [Environment API の取得](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment):呼び出し元がアクセスを許可されている環境の一覧が返されます。
* [Environments Availability API の取得](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability):イベントのタイムスタンプ `$ts` に対するイベント数の分布が返されます。 この API は、イベントが存在する場合に、時間間隔ごとにイベントの数を返すため、環境にイベントがあるかどうかを判断するのに役立ちます。
* [Get Event Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema):特定の検索範囲に対するイベント スキーマのメタデータが返されます。 この API は、特定の検索範囲に対してスキーマで使用できるすべてのメタデータとプロパティを取得するのに役立ちます。

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series Model-Query (TSM-Q) API

これらの API のほとんどでバッチ実行操作がサポートされるため、複数のタイム シリーズ モデル エンティティに対してバッチ CRUD 操作を実行できます。

* [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis):環境の既定の型とモデル名を *GET* および *PATCH* できます。
* [Types API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api):タイム シリーズの種類とそれに関連する変数に対して CRUD を実行できます。
* [Hierarchies API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api):タイム シリーズの階層とそれに関連するフィールド パスに対して CRUD を実行できます。
* [Instances API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api):タイム シリーズのインスタンスとそれに関連するインスタンス フィールドに対して CRUD を実行できます。 さらに、Instances API は次の操作もサポートしています。
  * [Search](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search):インスタンス属性に基づいて、時系列インスタンス検索のヒットの部分リストを取得します。
  * [Suggest](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest):インスタンス属性に基づいて、時系列インスタンス検索のヒットの部分リストを検索しサジェストします。

## <a name="time-series-query-tsq-apis"></a>Time Series Query (TSQ) API

これらの API は、多層ストレージ ソリューションの両方のストア (ウォームとコールド) 全体で利用できます。 クエリ URL パラメーターは、クエリを実行する[ストアの種類](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)を指定するために使用されます。

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents):未加工のイベントおよび関連するイベント タイムスタンプのクエリと取得を実行できます。これらは、ソース プロバイダーから Azure Time Series Insights Gen2 に記録されるためです。 この API を使用すると、特定の時系列 ID と検索範囲に対する未加工のイベントを取得できます。 この API は、改ページ位置の自動修正をサポートしており、選択した入力に対する完全な応答データセットを取得できます。

  > [!IMPORTANT]

  > * [JSON のフラット化とエスケープの規則への今後の変更](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)の一部として、配列が**動的**型として格納されます。 この型として格納されているペイロード プロパティは、**Get Events API** を通じてのみアクセスできます。

* [Get Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries):未加工イベントの変数によって定義された計算を適用することで、計算値と関連するイベント タイムスタンプのクエリと取得を実行できます。 これらの変数は、タイム シリーズ モデルで定義することもクエリでインラインで指定することもできます。 この API は、改ページ位置の自動修正をサポートしており、選択した入力に対する完全な応答データセットを取得できます。

* [Aggregate Series API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries):未加工イベントの変数によって定義された計算を適用することで、集計値と関連する間隔タイムスタンプのクエリと取得を実行できます。 これらの変数は、タイム シリーズ モデルで定義することもクエリでインラインで指定することもできます。 この API は、改ページ位置の自動修正をサポートしており、選択した入力に対する完全な応答データセットを取得できます。
  
  この API は、指定された検索範囲と間隔で、タイム シリーズ ID の変数ごとの各間隔で集計された応答を返します。 応答データセット内の間隔の数は、エポック ティック (Unix エポック - 1970 年 1 月 1 日以降の経過時間 (ミリ秒)) をカウントし、クエリで指定された間隔のサイズでティックを割ることによって計算されます。

  応答セットで返されるタイムスタンプは、間隔からサンプリングされたイベントのものではなく、残された間隔境界のものです。

## <a name="next-steps"></a>次のステップ

* [タイム シリーズ モデル](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)で定義できるさまざまな変数について詳細を確認する。
* [Azure Time Series Insights TSI Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer) からデータのクエリを実行する方法について詳細を確認する。
