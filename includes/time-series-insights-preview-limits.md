---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/06/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: be46a0dda24dc990e895a3e983c730337f9cf31d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74981281"
---
### <a name="general-availability-and-preview-comparison"></a>一般提供とプレビューの比較

次の表に、Azure Time Series Insights の一般提供 (GA) とプレビュー インスタンスの主な違いの一部をまとめます。

| | GA | プレビュー |
| --- | --- | ---|
| 第一級オブジェクト | イベント中心 | 時系列中心 |
| セマンティック推論 | 低レベル (参照データ) | 高レベル (モデル) |
| データのコンテキスト化 | 非デバイス レベル | デバイスおよび非デバイス レベル |
| コンピューティング ロジック ストレージ | いいえ | モデルの型変数部分に格納 |
| ストレージとアクセス制御 | いいえ | モデルを使用することで有効 |
| 集計/サンプリング | いいえ | イベントの重み付けと時間の重み付け |
| シグナルの再構築 | いいえ | 補間 |
| 派生時系列の作成 | いいえ | はい、マージと結合 |
| 言語の柔軟性 | 非コンポーザブル | コンポーザブル |
| 式言語 | 述語文字列 | 時系列式 (述語文字列、値、式、および関数) |

### <a name="property-limits"></a>プロパティの制限

Time Series Insights のプロパティ制限は、GA の上限である 800 個から 1,000 個に引き上げられました。 提供されているイベント プロパティには、対応する JSON、CSV、およびグラフの列があり、[Time Series Insights プレビュー エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)内で確認できます。

| SKU | 最大のプロパティ |
| --- | --- |
| プレビュー PAYG | 1,000 個のプロパティ (列) |
| GA S1 | 600 個のプロパティ (列) |
| GA S2 | 800 個のプロパティ (列) |

### <a name="event-sources"></a>イベント ソース

インスタンスごとに最大 2 つのイベント ソースがサポートされています。 

* [イベント ハブ ソースを追加する](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)方法を学習します。
* [IoT ハブ ソース](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)を構成します。

### <a name="api-limits"></a>API の制限

Time Series Insights プレビューの REST API の制限については、[REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)を参照してください。