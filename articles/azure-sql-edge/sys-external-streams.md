---
title: sys.external_streams (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での sys.external_streams の使用方法について説明します
keywords: sys.external_streams、SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 04950f01c06bc3c8ed3bb11a790310c2319a0579
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900312"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

データベースのスコープ内で作成された外部ストリーム オブジェクトごとに 1 行のデータを返します。

|列名|データ型|説明|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|ストリームの名前。 データベース内で一意です。|
|**object_id**|**int**|ストリーム オブジェクトのオブジェクト ID 番号。 データベース内で一意です。|
|**principal_id**|**int**|このアセンブリを所有しているプリンシパルの ID|
|**schema_id**|**int**| このオブジェクトを含むスキーマの ID。|
|**parent_object_id**|**id**| このストリームの親オブジェクトのオブジェクト ID 番号。 現在の実装では、この値は常に null 値です|
|**type**|**char(2)**|オブジェクトの種類です。 ストリーム オブジェクトの場合、種類は常に 'ES' になります|
|**type_desc**|**nvarchar(60)**| オブジェクトの種類の説明。 ストリーム オブジェクトの場合、種類は常に 'EXTERNAL_STREAM' になります|
|**create_date**|**datetime**| オブジェクトが作成された日付です。|
|**modify_date**|**datetime**| オブジェクトが ALTER ステートメントを使用して最後に変更された日付です。|
|**is_ms_shipped**|**bit**| 内部コンポーネントによって作成されたオブジェクトです。|  
|**is_published**|**bit**|オブジェクトがパブリッシュされます。|  
|**is_schema_published**|**bit**|オブジェクトのスキーマのみがパブリッシュされることを示します。|
|**max_column_id_used**|**bit**| この列は内部目的で使用され、将来的には削除されます|  
|**uses_ansi_nulls**|**bit**| SET ANSI_NULLS データベース オプションが ON でストリーム オブジェクトが作成されました|
|**data_source_id**|**int**| ストリーム オブジェクトによって表される外部データ ソースのオブジェクト ID |  
|**file_format_id**|**int**| ストリーム オブジェクトによって使用される外部ファイル形式のオブジェクト ID。 外部ストリームによって参照されるデータの実際のレイアウトを指定するには、外部ファイル形式が必要です| 
|**location**|**varchar(max)**| 外部ストリーム オブジェクトのターゲット。 詳細については、[外部ストリームの作成](overview.md)に関する記事を参照してください |
|**input_option**|**varchar(max)**| 外部ストリームの作成中に使用される入力オプション。 詳細については、[外部ストリームの作成](overview.md)に関する記事を参照してください |
|**output_option**|**varchar(max)**| 外部ストリームの作成中に使用される出力オプション。 詳細については、[外部ストリームの作成](overview.md)に関する記事を参照してください | 

## <a name="permissions"></a>アクセス許可

カタログ ビューでのメタデータの表示が、ユーザーが所有しているかそのユーザーが権限を許可されている、セキュリティ保護可能なメタデータに制限されます。 詳細については、「 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)」を参照してください。

## <a name="see-also"></a>関連項目

- [カタログ ビュー (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [システム ビュー (Transact-SQL)](/sql/t-sql/language-reference/)
