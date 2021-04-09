---
title: sys.external_job_streams (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での sys.external_job_streams の使用方法について説明します
keywords: sys.external_job_streams、SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900355"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

外部ストリーミング ジョブにマップされた入出力外部ストリーム オブジェクトごとに 1 行を返します。

|列名|データ型|説明|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| ストリーミング ジョブ オブジェクトのオブジェクト ID 番号。 この列は sys.external_streaming_jobs の object_id 列にマップされます。|
|**stream_id**|**int**| ストリーム オブジェクトのオブジェクト ID 番号。 この列は sys.external_streams の object_id 列にマップされます。 |
|**is_input**|**bit**| ストリーム オブジェクトがストリーミング ジョブの入力として使用されている場合は 1。それ以外の場合は 0。|
|**is_output**|**bit**| ストリーム オブジェクトがストリーミング ジョブの出力として使用されている場合は 1。それ以外の場合は 0。|

## <a name="example"></a>例

このカタログ ビューは、`sys.external_streams` および `sys.external_streaming_jobs` カタログ ビューと共に使用されます。 サンプル クエリを次に示します

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>アクセス許可

カタログ ビューでのメタデータの表示が、ユーザーが所有しているかそのユーザーが権限を許可されている、セキュリティ保護可能なメタデータに制限されます。 詳細については、「 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)」を参照してください。

## <a name="see-also"></a>関連項目

- [カタログ ビュー (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [システム ビュー (Transact-SQL)](/sql/t-sql/language-reference/)
