---
title: Azure でサポートされる FHIR 機能 - Azure API for FHIR
description: この記事では、Azure API for FHIR で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: bdf328222fef1f763776bd26c47f5cd4d65e487e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000007"
---
# <a name="features"></a>特徴

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、FHIR Server の主な機能を一覧表示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 読み取り                           | はい       | はい       | はい       |                                                     |
| vread                          | はい       | はい       | はい       |                                                     |
| update                         | はい       | はい       | はい       |                                                     |
| オプティミスティック ロック付きの update | はい       | はい       | はい       |                                                     |
| update (条件付き)           | はい       | はい       | はい       |                                                     |
| patch                          | いいえ        | いいえ        | いいえ        |                                                     |
| delete                         | はい       | はい       | はい       |                                                     |
| delete (条件付き)           | いいえ        | いいえ        | いいえ        |                                                     |
| create                         | はい       | はい       | はい       | POST/PUT の両方をサポートします                               |
| create (条件付き)           | はい       | はい       | はい       |                                                     |
| 検索                         | 部分的   | 部分的   | 部分的   | 下記参照                                           |
| chained search                 | いいえ        | はい       | いいえ        |                                           |
| reverse chained search         | いいえ        | いいえ        | いいえ        |                                            |
| capabilities                   | はい       | はい       | はい       |                                                     |
| batch (バッチ)                          | はい       | はい       | はい       |                                                     |
| transaction                    | いいえ        | はい       | いいえ        |                                                     |
| history                        | はい       | はい       | はい       |                                                     |
| paging                         | 部分的   | 部分的   | 部分的   | `self` と `next` がサポートされています                     |
| intermediaries                 | いいえ        | いいえ        | いいえ        |                                                     |

## <a name="search"></a>検索

すべての検索パラメーターの種類がサポートされています。 

| 検索パラメーターの種類 | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | はい       | はい       | はい       |         |
| Date/DateTime         | はい       | はい       | はい       |         |
| String                | はい       | はい       | はい       |         |
| トークン                 | はい       | はい       | はい       |         |
| リファレンス             | はい       | はい       | はい       |         |
| Composite             | はい       | はい       | はい       |         |
| Quantity              | はい       | はい       | はい       |         |
| URI                   | はい       | はい       | はい       |         |
| Special               | いいえ        | いいえ        | いいえ        |         |


| 修飾子             | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | はい       | はい       | はい       |         |
|`:exact`               | はい       | はい       | はい       |         |
|`:contains`            | はい       | はい       | はい       |         |
|`:text`                | はい       | はい       | はい       |         |
|`:in` (トークン)          | いいえ        | いいえ        | いいえ        |         |
|`:below` (トークン)       | いいえ        | いいえ        | いいえ        |         |
|`:above` (トークン)       | いいえ        | いいえ        | いいえ        |         |
|`:not-in` (トークン)      | いいえ        | いいえ        | いいえ        |         |
|`:[type]` (参照)  | いいえ        | いいえ        | いいえ        |         |
|`:below` (URI)         | はい       | はい       | はい       |         |
|`:not`                 | いいえ        | いいえ        | いいえ        |         |
|`:above` (URI)         | いいえ        | いいえ        | いいえ        | イシュー [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 一般的な検索パラメーター | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | はい       | はい       | はい       |         |
| `_lastUpdated`          | はい       | はい       | はい       |         |
| `_tag`                  | はい       | はい       | はい       |         |
| `_profile`              | はい       | はい       | はい       |         |
| `_security`             | はい       | はい       | はい       |         |
| `_text`                 | いいえ        | いいえ        | いいえ        |         |
| `_content`              | いいえ        | いいえ        | いいえ        |         |
| `_list`                 | はい       | はい       | はい       |         |
| `_has`                  | いいえ        | いいえ        | いいえ        |         |
| `_type`                 | はい       | はい       | はい       |         |
| `_query`                | いいえ        | いいえ        | いいえ        |         |

| 検索操作       | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | いいえ        | いいえ        | いいえ        |         |
| `_sort`                 | いいえ        | Partial   | 部分的        |   `_sort=_lastUpdated` がサポートされています       |
| `_score`                | いいえ        | いいえ        | いいえ        |         |
| `_count`                | はい       | はい       | はい       |         |
| `_summary`              | 部分的   | 部分的   | 部分的   | `_summary=count` がサポートされています |
| `_include`              | いいえ        | はい       | いいえ        |         |
| `_revinclude`           | いいえ        | はい       | いいえ        | 含まれる項目は 100 に制限されています。 |
| `_contained`            | いいえ        | いいえ        | いいえ        |         |
| `_elements`             | いいえ        | いいえ        | いいえ        |         |

## <a name="persistence"></a>永続化

Microsoft FHIR Server には、プラグ可能な永続化モジュールがあります ([`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) を参照)。

現在、FHIR Server のオープン ソース コードには [Azure Cosmos DB](../cosmos-db/index-overview.md) および [SQL Database](https://azure.microsoft.com/services/sql-database/) 用の実装が含まれています。

Cosmos DB は、グローバル分散型のマルチモデル (SQL API、MongoDB API など) データベースです。 これは、さまざまな[整合性レベル](../cosmos-db/consistency-levels.md)をサポートしています。 既定のデプロイ テンプレートでは `Strong` の整合性で FHIR Server を構成しますが、整合性ポリシーは、`x-ms-consistency-level` 要求ヘッダーを使用して要求ごとに変更 (一般には緩和) できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR Server は、アクセス制御のために [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。 特に、`FhirServer:Security:Enabled` 構成パラメーターが `true` に設定され、FHIR Server へのすべての要求 (`/metadata` を除く) で `Authorization` 要求ヘッダーが `Bearer <TOKEN>` に設定されている必要がある場合は、ロールベースのアクセス制御 (RBAC) が適用されます。 トークンには、`roles` 要求で定義されている 1 つ以上のロールが含まれている必要があります。 トークンに、指定されたリソースに対する指定されたアクションを許可するロールが含まれている場合は、要求が許可されます。

現在、特定のロールに対して許可されるアクションは API で*グローバルに*適用されます。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR でサポートされる FHIR 機能について学習しました。 次は Azure API for FHIR をデプロイします。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)
