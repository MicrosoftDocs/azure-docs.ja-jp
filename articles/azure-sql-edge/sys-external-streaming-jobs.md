---
title: sys.external_streaming_jobs (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での sys.external_streaming_jobs の使用方法について説明します
keywords: sys.external_streaming_jobs、SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900333"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

データベースのスコープ内で作成された外部ストリーミング ジョブごとに 1 行のデータを返します。

|列名|データ型|説明|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|ストリームの名前。 データベース内で一意です。|
|**object_id**|**int**|ストリーム オブジェクトのオブジェクト ID 番号。 データベース内で一意です。|
|**principal_id**|**int**|このアセンブリを所有しているプリンシパルの ID|
|**schema_id**|**int**| このオブジェクトを含むスキーマの ID。|
|**parent_object_id**|**id**| このストリームの親オブジェクトのオブジェクト ID 番号。 現在の実装では、この値は常に null 値です|
|**type**|**char(2)**|オブジェクトの種類です。 ストリーム オブジェクトの場合、種類は常に 'EJ' になります|
|**type_desc**|**nvarchar(60)**| オブジェクトの種類の説明。 ストリーム オブジェクトの場合、種類は常に 'EXTERNAL_STREAMING_JOB' になります|
|**create_date**|**datetime**| オブジェクトが作成された日付です。|
|**modify_date**|**datetime**| 現在の実装では、この値はストリーム オブジェクトの create_date と同じです |
|**is_ms_shipped**|**bit**| 内部コンポーネントによって作成されたオブジェクトです。|  
|**is_published**|**bit**| オブジェクトがパブリッシュされます。|  
|**is_schema_published**|**bit**|オブジェクトのスキーマのみがパブリッシュされることを示します。|
|**uses_ansi_nulls**|**bit**| SET ANSI_NULLS データベース オプションが ON でストリーム オブジェクトが作成されました|
|**statement**|**varchar(max)**| ストリーミング ジョブの Stream Analytics クエリ テキスト。 詳細については、[sp_create_streaming_job](overview.md) に関する記事をご覧ください |
|**status**|**int**| ストリーミング ジョブの現在の状態。 可能な値は次のとおりです <br /><br /> **作成済み** = 0。 ストリーミング ジョブは作成されましたが、まだ開始されていません。 <br /><br /> **開始中** = 1。 ストリーミング ジョブは開始フェーズになっています。 <br /><br /> **失敗** = 6。 ストリーミング ジョブは失敗しました。 これは、通常、処理中に致命的なエラーが発生したことを示します。 <br /><br /> **停止済み** = 4。 ストリーミング ジョブは停止されました。 <br /><br /> **アイドル状態** = 7。 ストリーミング ジョブは実行中ですが、処理する入力がありません。 <br /><br /> **処理中** = 8。 ストリーミング ジョブは実行中であり、入力を処理しています。 この状態は、ストリーミング ジョブが正常な状態であることを示します。 <br /><br /> **デグレード** = 9。 ストリーミング ジョブは実行中ですが、入力処理中に、致命的でない入出力のシリアル化または逆シリアル化エラーが発生しました。 入力ジョブは実行を続けますが、エラーが発生する入力は削除されます。|

## <a name="permissions"></a>アクセス許可

カタログ ビューでのメタデータの表示が、ユーザーが所有しているかそのユーザーが権限を許可されている、セキュリティ保護可能なメタデータに制限されます。 詳細については、「 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)」を参照してください。

## <a name="see-also"></a>関連項目

- [T-SQL ストリーミングのカタログ ビュー](overview.md)
- [カタログ ビュー (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [システム ビュー (Transact-SQL)](/sql/t-sql/language-reference/)

