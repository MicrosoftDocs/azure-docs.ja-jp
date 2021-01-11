---
title: Synapse SQL での T-SQL 機能の違い
description: Synapse SQL で使用できる Transact-SQL 機能の一覧。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: ce7238aaa10b1e73f7fa3c061045f47cbc472e23
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378697"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Azure Synapse SQL でサポートされる Transact-SQL 機能

Azure Synapse SQL は、T-SQL 言語を使用してデータのクエリと分析を実行できるビッグ データ分析サービスです。 SQL Server と Azure SQL Database で使用される SQL 言語の標準の ANSI 準拠言語をデータ分析に使用できます。 

Transact-SQL 言語は、Synapse SQL のサーバーレスおよびプロビジョニング モデルで使用されます。これらでは参照できるオブジェクトが異なり、サポートされている機能のセットにいくつかの違いがあります。 このページでは、Synapse SQL の消費モデル間での Transact-SQL 言語の大まかな違いについて確認できます。

## <a name="database-objects"></a>データベース オブジェクト

Synapse SQL の消費モデルでは、さまざまなデータベース オブジェクトを使用できます。 サポートされているオブジェクトの種類を比較した表を次に示します。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **テーブル** | [はい](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | いいえ。サーバーレス モデルでは、[Azure Storage](#storage-options) に配置された外部データに対してのみクエリを実行できます |
| **ビュー** | [はい](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 ビューでは、プロビジョニング モデルで使用可能な[クエリ言語要素](#query-language)を使用できます。 | [はい](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 ビューでは、サーバーレス モデルで使用可能な[クエリ言語要素](#query-language)を使用できます。 |
| **スキーマ** | [はい](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [はい](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **一時テーブル** | [はい](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | いいえ |
| **手順** | [はい](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | いいえ |
| **関数** | [はい](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | はい。インライン テーブル値関数のみです。 |
| **トリガー** | いいえ | いいえ |
| **外部テーブル** | [はい](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 サポートされる[データ形式](#data-formats)をご確認ください。 | [はい](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 サポートされる[データ形式](#data-formats)をご確認ください。 |
| **クエリのキャッシュ** | はい。複数の形式 (SSD ベースのキャッシュ、インメモリ、結果セットのキャッシュ)。 さらに、マテリアライズドビューがサポートされます | いいえ |
| **テーブル変数** | [いいえ](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。一時テーブルを使用します | いいえ |
| **[テーブルの分散](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | はい | いいえ |
| **[テーブルのインデックス](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | はい | いいえ |
| **[テーブルのパーティション](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | はい | いいえ |
| **[統計](develop-tables-statistics.md)**            | はい | はい |
| **[ワークロード管理、リソース クラス、コンカレンシー制御](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | はい    | いいえ |

## <a name="query-language"></a>クエリ言語

Synapse SQL で使用されるクエリ言語では、従量課金モデルに応じてサポートされる機能が異なります。 次の表では、Transact-SQL 言語において最も重要なクエリ言語の違いについて説明します。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **SELECT ステートメント** | はい。 Transact-SQL クエリ句 [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) および [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) はサポートされていません。 | はい。 Transact-SQL クエリ句 [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、クエリ ヒントはサポートされていません。 [OFFSET、FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned)、[PIVOT、UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) は、システム オブジェクトのクエリにのみ使用できます (外部データには使用できません)。 |
| **INSERT ステートメント** | はい | いいえ |
| **UPDATE ステートメント** | はい | いいえ |
| **DELETE ステートメント** | はい | いいえ |
| **MERGE ステートメント** | いいえ | いいえ |
| **[トランザクション](develop-transactions.md)** | はい | いいえ |
| **[ラベル](develop-label.md)** | はい | いいえ |
| **データ読み込み** | はい。 推奨されるユーティリティは [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ステートメントです。ただし、データ読み込みでは一括読み込み (BCP) と [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) の両方がサポートされます。 | いいえ |
| **データ エクスポート** | はい。 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用。 | はい。 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用。 |
| **型** | はい。[cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[ntext、text、image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[空間型](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除くすべての Transact-SQL 型 | はい。[cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[ntext、text、image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[空間型](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、テーブル型を除くすべての Transact-SQL 型 |
| **複数データベース間のクエリ** | いいえ | はい。[USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ステートメントを含む。 |
| **組み込み関数 (分析)** | はい。[CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除くすべての Transact-SQL [分析](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、変換、[日付と時刻](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、論理、および[数学](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)関数 | はい。すべての Transact-SQL [分析](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、変換、[日付と時刻](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、論理、および[数学](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)関数。 |
| **組み込み関数 (テキスト)** | はい。 [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) と [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除くすべての Transact-SQL [文字列](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、および照合順序関数 | はい。 すべての Transact-SQL [文字列](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、および照合順序関数。 |
| **組み込みテーブル値関数** | はい。[OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除く [Transact-SQL 行セット関数](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions) | はい。[OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除く [Transact-SQL 行セット関数](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)  |
| **集計** |  [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) と [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除く Transact-SQL 組み込み集計 | [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除く Transact-SQL 組み込み集計 |
| **オペレーター** | はい。[!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) と [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除くすべての [Transact-SQL 演算子](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | はい。すべての [Transact-SQL 演算子](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **フロー制御** | はい。 [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を除くすべての [Transact-SQL フロー制御ステートメント](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | はい。 `WHILE (...)` 条件の [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) および SELECT クエリを除くすべての [Transact-SQL フロー制御ステートメント](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **DDL ステートメント (CREATE、ALTER、DROP)** | はい。 サポートされているオブジェクトの種類に適用できるすべての Transact-SQL DDL ステートメント | はい。 サポートされているオブジェクトの種類に適用できるすべての Transact-SQL DDL ステートメント |

## <a name="security"></a>Security

Synapse SQL では、組み込みのセキュリティ機能を使用し、データをセキュリティで保護してアクセスを制御できます。 次の表は、Synapse SQL 消費モデル間の大まかな違いを比較したものです。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **ログイン** | なし (データベースでサポートされるのは包含ユーザーのみ) | はい |
| **ユーザー** |  なし (データベースでサポートされるのは包含ユーザーのみ) | はい |
| **[包含ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | はい。 **注:** 制限のない管理者になれる Azure AD ユーザーは 1 人だけです | はい |
| **SQL ユーザー名とパスワードによる認証**| はい | はい |
| **Azure Active Directory (AAD) 認証**| はい。Azure AD ユーザー | はい。Azure AD ログインとユーザー |
| **Storage の Azure Active Directory (AAD) パススルー認証** | はい | はい |
| **Storage の SAS トークン認証** | いいえ | はい。[EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) またはインスタンスレベルの [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) で [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用。 |
| **Storage のアクセス キー認証** | はい。[EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) で [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用 | いいえ |
| **Storage の[マネージド ID](../security/synapse-workspace-managed-identity.md) 認証** | はい。[マネージド サービス ID 資格情報](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)を使用 | はい。`Managed Identity` 資格情報を使用。 |
| **Storage のアプリケーション ID 認証** | [はい](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | いいえ |
| **アクセス許可 - オブジェクトレベル** | はい。ユーザーへのアクセス許可の付与、拒否、取り消しを行う機能を含む | はい。サポートされているシステム オブジェクトでのユーザーまたはログインへのアクセス許可の付与、拒否、取り消しを行う機能を含む |
| **アクセス許可 - スキーマレベル** | はい。スキーマでのユーザーまたはログインへのアクセス許可の付与、拒否、取り消しを行う機能を含む | はい。スキーマでのユーザーまたはログインへのアクセス許可の付与、拒否、取り消しを行う機能を含む |
| **アクセス許可 - [データベースレベル](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | はい | はい |
| **アクセス許可 - [サーバーレベル](/sql/relational-databases/security/authentication-access/server-level-roles)** | いいえ | はい。sysadmin とその他のサーバーロールがサポートされています |
| **アクセス許可 - [列レベルのセキュリティ](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | はい | はい |
| **ロールまたはグループ** | はい (データベース スコープ) | はい (サーバーとデータベースの両方のスコープ) |
| **セキュリティと ID の関数** | 一部の Transact-SQL セキュリティ関数および演算子: `CURRENT_USER`、`HAS_DBACCESS`、`IS_MEMBER`、`IS_ROLEMEMBER`、`SESSION_USER`、`SUSER_NAME`、`SUSER_SNAME`、`SYSTEM_USER`、`USER`、`USER_NAME`、`EXECUTE AS`、`OPEN/CLOSE MASTER KEY` | 一部の Transact-SQL セキュリティ関数および演算子: `CURRENT_USER`、`HAS_DBACCESS`、`HAS_PERMS_BY_NAME`、`IS_MEMBER', 'IS_ROLEMEMBER`、`IS_SRVROLEMEMBER`、`SESSION_USER`、`SUSER_NAME`、`SUSER_SNAME`、`SYSTEM_USER`、`USER`、`USER_NAME`、`EXECUTE AS`、`REVERT`。 セキュリティ関数は、外部データのクエリを実行するために使用できません (クエリで使用できる変数に結果を格納します)。  |
| **DATABASE SCOPED CREDENTIAL** | はい | はい |
| **サーバー スコープの資格情報** | いいえ | はい |
| **行レベルのセキュリティ** | [はい](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | いいえ |
| **透過的なデータ暗号化 (TDE)** | [はい](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | いいえ | 
| **データの検出と分類** | [はい](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | いいえ |
| **脆弱性評価** | [はい](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | いいえ |
| **Advanced Threat Protection** | [はい](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **監査** | [はい](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | いいえ |
| **[ファイアウォール規則](../security/synapse-workspace-ip-firewall.md)**| はい | はい |
| **[プライベート エンドポイント](../security/synapse-workspace-managed-private-endpoints.md)**| はい | はい |

SQL プールと SQL オンデマンドでは、標準の Transact-SQL 言語を使用してデータのクエリを実行します。 詳細な相違点については、[Transact-SQL 言語のリファレンス](/sql/t-sql/language-reference)を参照してください。

## <a name="tools"></a>ツール

さまざまなツールを使用して Synapse SQL に接続し、データのクエリを実行できます。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **Synapse Studio** | はい。SQL スクリプト | はい。SQL スクリプト |
| **Power BI** | はい | [はい](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | はい | はい |
| **Azure Data Studio** | はい | はい。バージョン 1.18.0 以上。 SQL スクリプトと SQL ノートブックがサポートされています。 |
| **SQL Server Management Studio** | はい | はい。バージョン 18.5 以上。 |

> [!NOTE]
> SSMS を使用して SQL オンデマンド (プレビュー) に接続し、クエリを実行できます。 これはバージョン 18.5 以降、部分的にサポートされています。接続してクエリを実行する場合にのみ使用できます。

標準の Transact-SQL 言語が使用されるほとんどのアプリケーションでは、プロビジョニングとサーバーレス両方の Synapse SQL 消費モデルのクエリを実行できます。

## <a name="storage-options"></a>ストレージ オプション

分析されるデータは、さまざまな種類のストレージで保存できます。 次の表は、すべての使用可能なストレージ オプションの一覧です。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **内部ストレージ** | はい | いいえ |
| **Azure Data Lake v2** | はい | はい |
| **Azure Blob Storage** | はい | はい |
| **Azure CosmosDB 分析ストレージ** | いいえ | はい。[Synapse Link](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) を使用 ([限定的なプレビュー](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>データ形式

分析されるデータは、さまざまな形式のストレージで保存できます。 次の表は、分析できるすべての使用可能なデータ形式の一覧です。

|   | プロビジョニング済み | サーバーレス |
| --- | --- | --- |
| **区切り記号** | [はい](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [はい](query-single-csv-file.md) |
| **CSV** | はい (複数の文字による区切り記号はサポートされません) | [はい](query-single-csv-file.md) |
| **Parquet** | [はい](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [はい](query-parquet-files.md)。[入れ子にされた型](query-parquet-nested-types.md)があるファイルを含む |
| **Hive ORC** | [はい](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | いいえ |
| **Hive RC** | [はい](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | いいえ |
| **JSON** | はい | [はい](query-json-files.md) |
| **Avro** | いいえ | いいえ |
| **[Delta-lake](https://delta.io/)** | いいえ | いいえ |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | いいえ | いいえ |

## <a name="next-steps"></a>次のステップ
SQL プールと SQL オンデマンドのベスト プラクティスに関する追加情報については、次の記事を参照してください。

- [SQL プールのベスト プラクティス](best-practices-sql-pool.md)
- [SQL オンデマンドのベスト プラクティス](best-practices-sql-on-demand.md)
