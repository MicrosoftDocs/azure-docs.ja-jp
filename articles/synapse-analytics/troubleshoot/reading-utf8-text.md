---
title: トラブルシューティング:サーバーレス SQL プールを使用した CSV または PARQUET ファイルからの UTF-8 テキストの読み取り
description: Azure Synapse Analytics のサーバーレス SQL プールを使用した CSV または Parquet ファイルからの UTF-8 テキストの読み取り
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576419"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用して CSV または Parquet ファイルから UTF-8 テキストを読み取るためのトラブルシューティング

この記事では、Azure Synapse Analytics のサーバーレス SQL プールを使用して、CSV または Parquet ファイルから UTF-8 テキストを読み取るためのトラブルシューティング手順について説明します。

サーバーレス SQL プールを使用して CSV または PARQUET ファイルから UTF-8 テキストを読み取った場合、UTF8 以外の照合順序を使用する VARCHAR 列がクエリで返されると、ü や ö などの特殊文字が正しく変換されません。 これは SQL Server と Azure SQL の既知の問題です。 Synapse SQL では、UTF8 以外の照合順序が既定値であるため、顧客のクエリは影響を受けます。 標準の英字と拡張ラテン文字の一部のサブセットを使用しているお客様は、この変換エラーに気付かない場合もあります。 誤った変換について詳しくは、「[サーバーレス SQL プールで UTF-8 テキストを読み取るときに UTF-8 の照合順序を常に使用する](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)」を参照してください

## <a name="workaround"></a>回避策

この問題を回避するには、CSV ファイルまたは PARQUET ファイルから UTF-8 テキストを読み取るときに、UTF-8 照合順序を常に使用します。

- 多くの場合、データベースに対して UTF8 照合順序を設定すれば十分です (メタデータ操作)。

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- OPENROWSET または外部テーブルの VARCHAR 列では、照合順序を明示的に定義できます。

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- UTF8 データを読み取る外部テーブルに対して UTF8 照合順序を指定しなかった場合、影響を受ける外部テーブルを再作成し、VARCHAR 列に UTF8 照合順序を設定する必要があります (メタデータ操作)。


## <a name="next-steps"></a>次のステップ

* [Synapse SQL で Parquet ファイルに対してクエリを実行する](../sql/query-parquet-files.md)
* [Synapse SQL で CSV ファイルに対してクエリを実行する](../sql/query-single-csv-file.md)
* [Synapse SQL での CETAS](../sql/develop-tables-cetas.md)
* [クイックスタート: サーバーレス SQL プールを使用する](../quickstart-sql-on-demand.md)
