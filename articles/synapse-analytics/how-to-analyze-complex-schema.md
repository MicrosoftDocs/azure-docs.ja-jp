---
title: 配列と入れ子構造を含むスキーマの分析
description: Apache Spark と SQL を使用して配列と入れ子構造を分析する方法
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fdf3dc56575a45ad0c9e716054184ba2691133ba
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831704"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Azure Synapse Analytics で複合データ型を分析する

この記事は、[ Synapse Link for Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md) の Parquet ファイルとコンテナーに関連しています。 ユーザーが Spark または SQL を使用して、配列や入れ子構造など、スキーマが複雑なデータの読み取りや変換を行う方法について説明しています。 以下の例は単一のドキュメントを使用して完了されていますが、Spark または SQL を使うと膨大な数のドキュメントに簡単にスケーリングできます。 この記事に含まれているコードでは、PySpark (Python) を使用しています。

## <a name="use-case"></a>ユース ケース

複雑なデータ型はますます一般的になっており、入れ子になったスキーマや配列を分析する際に時間のかかる複雑な SQL クエリが含まれる傾向があるため、データ エンジニアにとって課題になっています。 さらに、入れ子になった列のデータ型の名前変更やキャストが困難である可能性があります。 入れ子が深いオブジェクトを操作するときに、パフォーマンスの問題も発生します。

データ エンジニアは、複雑なデータ型を効率的に処理し、だれでも簡単にアクセスできるようにする方法を理解する必要があります。

次の例では、Synapse Spark を使用して、データ フレームを介してオブジェクトを読み取り、フラット構造に変換します。 このようなオブジェクトに直接クエリを実行し、その結果を通常のテーブルとして返すために Synapse SQL サーバーレスが使用されます。

## <a name="what-are-arrays-and-nested-structures"></a>配列と入れ子構造とは

次のオブジェクトは、[App Insight](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) のものです。 このオブジェクトには、入れ子構造と、入れ子構造を含む配列があります。

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>配列と入れ子構造のスキーマ例
このオブジェクトのデータ フレーム (**df**) のスキーマを `df.printschema` コマンドを使用して出力すると、次のように表示されます。

* 黄色は入れ子構造を表します
* 緑色は 2 つの要素を持つ配列を表します

[![スキーマの原形](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

ドキュメントが Azure Cosmos DB トランザクション ストアに取り込まれたときに、 **_rid**、 **_ts**、および **_etag** がシステムに追加されています。

上記のデータ フレームは、5 列と 1 行のみをカウントします。 変換後、選別されたデータ フレームは、13 列、2 行の表形式になります。

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Apache Spark を使って、入れ子構造をフラット化し、配列を分解する

Synapse Spark を使用すると、入れ子構造を列に、配列要素を複数の行に簡単に変換できます。 次の手順を実装で使用できます。

[![Spark 変換の手順](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**手順 1.** :入れ子になったスキーマをフラット化する関数を定義します。 この関数は、変更せずに使用できます。 次の関数を使用して、[PySpark ノートブック](quickstart-apache-spark-notebook.md)内にセルを作成します。

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**手順 2**:関数を使用して、データ フレーム (**df**) の入れ子になったスキーマを、新しいデータ フレーム `df_flat` にフラット化します。

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

display 関数から、10 列、1 行が返るはずです。 配列とその入れ子になった要素はまだそのままです。

**手順 3**:データ フレーム `df_flat` の配列 `context_custom_dimensions` を新しいデータ フレーム `df_flat_explode` に変換します。 次のコードで、選択する列も定義します。

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

display 関数から、10 列、2 行が返るはずです。 次の手順では、手順 1 で定義した関数を使用して、入れ子になったスキーマをフラット化します。

**手順 4**:関数を使用して、データ フレーム `df_flat_explode` の入れ子になったスキーマを、新しいデータ フレーム `df_flat_explode_flat` にフラット化します。
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

display 関数から、13 列、2 行が返るはずです。

データ フレーム `df_flat_explode_flat` の関数 `printSchema` によって、次の結果が返されます。

[![スキーマの最終形](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>SQL サーバーレスを使用して配列と入れ子構造を直接読み取る

SQL サーバーレスを使って、このようなオブジェクトに対してクエリを実行したり、ビューやテーブルを作成したりできます。

最初に、データがどのように格納されているかに応じて、ユーザーは次の分類法を使用する必要があります。 大文字部分はすべて、ユース ケースに固有です。

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet ' |'Parquet' (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB' (Synapse Link)|


> [!NOTE]
> SQL サーバーレスにより、Azure Synapse Link for Azure Cosmos および AAD パススルー用のリンクされたサービスがサポートされます。 この機能は現在、Synapse Link の限定的なプレビューの段階です。

各フィールドを次のように置き換えます。
* 'YOUR BULK ABOVE' = 接続先のデータ ソースの接続文字列
* 'YOUR TYPE ABOVE' = ソースへの接続に使用する形式

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

操作には、次の 2 種類があります。

次のコード行に、最初の操作の種類が示されています。ここでは入れ子になった要素を参照する `contextdataeventTime` という名前の列を定義します。Context.Data.eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

この行で、入れ子の要素を参照する contextdataeventTime という名前の列を定義します:Context>Data>eventTime

2 つ目の操作の種類では、`cross apply` を使用して配列の各要素に対して新しい行を作成します。その後、最初の箇条書きと同様に、入れ子になった各オブジェクトを定義します。 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

配列の要素が 5 つで、入れ子構造が 4 つの場合、SQL サーバーレスにより、5 行、4 列が返されます。 SQL サーバーレスを使用して、インプレースでクエリを実行し、配列を 2 行にマップして、すべての入れ子構造を列表示することができます。

## <a name="next-steps"></a>次のステップ

* [Spark を使って Synapse Link for Azure Cosmos DB に対してクエリを実行する方法を確認する](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet の入れ子にされた型に対してクエリを実行する](./sql/query-parquet-nested-types.md) 
