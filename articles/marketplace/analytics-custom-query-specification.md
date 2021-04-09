---
title: カスタム クエリの仕様
description: カスタム クエリを使用して、Microsoft コマーシャル マーケットプレースにあるプランの分析テーブルからプログラムによってデータを抽出する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583583"
---
# <a name="custom-query-specification"></a>カスタム クエリの仕様

パートナーは、このクエリの仕様を使用して、分析テーブルからデータを抽出するためのカスタム クエリを簡単に作成できます。 クエリを使用すると、特定の条件に一致する目的の列とメトリックのみを選択できます。 言語仕様の中核となるのは、カスタム クエリを記述する基礎となるデータセット定義です。

## <a name="datasets"></a>データセット

テーブルと列を含むデータベースに対して一部のクエリが実行されるのと同じように、カスタム クエリは列とメトリックを持つデータセットに対して動作します。 クエリを作成するために使用できるデータセットの完全な一覧は、データセット API を使用して取得できます。

JSON として表示されるデータセットの例をこちらに示します。

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>データセットの各部分

- データセット名は、データベース テーブル名に似ています。 たとえば、ISVUsage のようになります。 データセットには、MarketplaceSubscriptionId など、選択できる列の一覧があります。
- データセットには、データベース内の集計関数に似たメトリックもあります。 たとえば、NormalizedUsage のようになります。
- データをエクスポートできる一定の期間があります。

### <a name="formulating-a-query-on-a-dataset"></a>データセットに対するクエリの作成

これらは、さまざまな種類のデータを抽出する方法を示すサンプル クエリです。

| クエリ | 説明 |
| ------------ | ------------- |
| **SELECT** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | このクエリでは、過去 1 か月のすべての一意の `MarketplaceSubscriptionId` と、対応する `CustomerId` が取得されます。 |
| **SELECT** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | このクエリでは、各サブスクリプションで販売されているライセンス数の上位 10 件のサブスクリプションが、降順で取得されます。 |
| **SELECT** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **ORDER BY** NormalizedUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | このクエリでは、NormalizedUsage が 10 万より大きいすべての顧客の NormalizedUsage と RawUsage が取得されます。 |
| **SELECT** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **FROM** ISVUsage **WHERE** CustomerId IN (‘2a31c234-1f4e-4c60-909e-76d234f93161’, ‘80780748-3f9a-11eb-b378-0242ac130002’) | このクエリでは、`2a31c234-1f4e-4c60-909e-76d234f93161` および `80780748-3f9a-11eb-b378-0242ac130002` という 2 つの `CustomerId` 値によって毎月生成される `MarketplaceSubscriptionId` および収益が取得されます。 |
|||

## <a name="query-specification"></a>クエリの仕様

このセクションでは、クエリの定義と構造について説明します。

### <a name="grammar-reference"></a>文法リファレンス

この表では、クエリで使用される記号を説明します。

| Symbol | 意味 |
| ------------ | ------------- |
| ? | オプション |
| * | 0 個以上 |
| + | 1 個以上 |
| &#124; | または/リストの中の 1 つ |
| | |

### <a name="query-definition"></a>[クエリ定義]

クエリ ステートメントには、SelectClause、FromClause、WhereClause、OrderClause、LimitClause、および TimeSpan の各句があります。

- **SelectClause**: **SELECT** ColumOrMetricName (, ColumOrMetricName)*
    - **ColumOrMetricName**: データセット内で定義される列とメトリック
- **FromClause**: **FROM** DatasetName
    - **DatasetName**: データセット内で定義されるデータセット名
- **WhereClause**: **WHERE** FilterCondition (**AND** FilterCondition)*
    - **FilterCondition**: ColumOrMetricName Operator Value
        - **Operator**:  = | > | < | >= | <= | != | LIKE | NOT LIKE | IN | NOT IN
        - **Value**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Number**: -? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**:  ' [a-zA-Z0-9_]*'
            - **MultiNumberList**: (Number  (,Number)*)
            - **MultiStringList**: (StringLiteral (,StringLiteral)*)
- **OrderClause**: **ORDER BY** OrderCondition (,OrderCondition)*
    - **OrderCondition**: ColumOrMetricName (**ASC** | **DESC**)*
    - **LimitClause**: **LIMIT** [0-9]+
    - **TimeSpan**: **TIMESPAN** ( TODAY | YESTERDAY | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | LIFETIME)

### <a name="query-structure"></a>クエリ構造

レポート クエリは次の複数の部分で構成されます。

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

各部分について、下で説明します。

#### <a name="select"></a>SELECT

クエリのこの部分では、エクスポートする列を指定します。 選択できる列は、データセットの `selectableColumns` と `availableMetrics` のセクションに一覧表示されているフィールドです。 最終的にエクスポートされた行には、選択した列の個別の値が常に含まれます。 たとえば、エクスポートされたファイルには重複する行が存在しません。 メトリックは、選択した列の個別の組み合わせごとに計算されます。

**例**:
- **SELECT** `OfferName`, `NormalizedUsage`

#### <a name="from"></a>FROM

クエリのこの部分は、データをエクスポートする必要があるデータセットを示します。 ここで指定するデータセット名は、データセット API によって返される有効なデータセット名である必要があります。

**例**:
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

クエリのこの部分は、データセットのフィルター条件を指定するために使用されます。 この句に示されているすべての条件に一致する行のみが、最終的にエクスポートされたファイルに存在します。 フィルター条件は、`selectableColumns` および `availableMetrics` にリストされている任意の列に設定できます。 演算子が `IN` または `NOT IN` の場合、フィルター条件に指定する値は、数値のリストまたは文字列のリストにのみすることができます。 値は常にリテラル文字列として指定でき、列のネイティブ型に変換されます。 複数のフィルター条件は、`AND` 演算子で区切る必要があります。

**例**:

- MarketplaceSubscriptionId = ‘868368da-957d-4959-8992-3c12dc7e6260’
- CustomerName **LIKE** ‘%Contosso%’
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrderQuantity=100
- OrderQuantity=‘100’
    - MarketplaceSubscriptionId=’7b487ac0-ce12-b732-dcd6-91a1e4e74a50’ AND CustomerId=’ 0f8b7fa0-eb83-a183-1225-ca153ef807aa’

#### <a name="order-by"></a>ORDER BY

クエリのこの部分では、エクスポートされる行の順序付け条件を指定します。 順序を定義できる列は、データセットの `selectableColumns` および `availableMetrics` からのものである必要があります。 順序付けの方向が指定されていない場合は、既定で列の `DESC` に設定されます。 条件をコンマで区切ることで、複数の列に対して順序付けを定義できます。

**例**:

- **ORDER BY** NormalizedUsage **ASC**, EstimatedExtendedCharge(CC) **DESC**
- **ORDER BY** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

クエリのこの部分では、エクスポートする行の数を指定します。 指定する数値は、0 以外の正の整数である必要があります。

#### <a name="timespan"></a>TIMESPAN

クエリのこの部分では、データをエクスポートする必要がある期間を指定します。 使用可能な値は、データセット定義の `availableDateRanges` フィールドから指定する必要があります。

### <a name="case-sensitivity-in-query-specification"></a>クエリの仕様における大文字と小文字の区別

この仕様では、大文字と小文字はいっさい区別されません。 定義済みのキーワードや列の名前および値は、大文字または小文字を使用して指定できます。

## <a name="see-also"></a>関連項目

- [システム クエリの一覧](analytics-system-queries.md)
