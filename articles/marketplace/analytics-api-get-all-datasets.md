---
title: すべてのデータセットの取得 API
description: この API を使用して、コマーシャル マーケットプレース分析で使用可能なすべてのデータセットを取得します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583554"
---
# <a name="get-all-datasets-api"></a>すべてのデータセットの取得 API

すべてのデータセットの取得 API は、使用可能なすべてのデータセットを取得します。 データセットには、テーブル、列、メトリック、および時間範囲が一覧表示されます。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |

**パス パラメーター**

なし

**Query parameter (クエリ パラメーター)**

| **パラメーター名** | **Type** | **必須** | **説明** |
| --- | --- | --- | --- |
| `datasetName` | string | いいえ | フィルターを適用して、1 つのデータセットの詳細のみを取得します |

**要求ペイロード**

なし

**用語集**

None

**応答**

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロードの例:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**用語集**

次の表に、応答の主な要素を定義します。

| **パラメーター** | **説明** |
| --- | --- |
| `DatasetName` | この配列オブジェクトによって定義されるデータセットの名前 |
| `SelectableColumns` | 列の選択で指定できる未加工の列 |
| `AvailableMetrics` | 列の選択で指定できる集計またはメトリック列の名前 |
| `AvailableDateRanges` | データセットのレポート クエリで使用できる日付範囲 |
| `NextLink` | データがページ分割された場合の次のページへのリンク |
| `TotalCount` | Value 配列内のデータセットの数 |
| `StatusCode` | 結果コード。 200、400、401、403、500 の値になる可能性があります |
