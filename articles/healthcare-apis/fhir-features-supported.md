---
title: Azure でサポートされる FHIR 機能 - Azure API for FHIR
description: この記事では、Azure API for FHIR で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 1752ec8b2f846b51ef8222c54a00d5a5a0cdd05a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875197"
---
# <a name="features"></a>機能

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、FHIR Server の主な機能を一覧表示します。

## <a name="fhir-version"></a>FHIR のバージョン

現在のバージョン: `3.0.1`

## <a name="rest-api"></a>REST API

| API                            | サポートされています | Comment (コメント) |
|--------------------------------|-----------|---------|
| read                           | はい       |         |
| vread                          | はい       |         |
| update                         | はい       |         |
| オプティミスティック ロック付きの update | はい       |         |
| update (条件付き)           | いいえ         |         |
| patch                          | いいえ         |         |
| delete                         | はい       |         |
| delete (条件付き)           | いいえ         |         |
| create                         | はい       | POST/PUT の両方をサポートします |
| create (条件付き)           | いいえ         |         |
| 検索                         | 部分的   | 下記参照 |
| capabilities                   | はい       |         |
| batch (バッチ)                          | いいえ         |         |
| transaction                    | いいえ         |         |
| history                        | はい       |         |
| paging                         | 部分的   | `self` と `next` がサポートされています |
| intermediaries                 | いいえ         |         |

## <a name="search"></a>Search

すべての検索パラメーターの種類がサポートされています。 チェーンされたパラメーターと逆方向チェーンはサポートされて*いません*。

| 検索パラメーターの種類 | サポートされています | Comment (コメント) |
|-----------------------|-----------|---------|
| Number                | はい       |         |
| Date/DateTime         | はい       |         |
| String                | はい       |         |
| トークン                 | はい       |         |
| リファレンス             | はい       |         |
| 複合             | はい       |         |
| 数量              | はい       | イシュー [#103](https://github.com/Microsoft/fhir-server/issues/103) |
| URI                   | はい       |         |


| 修飾子             | サポートされています | Comment (コメント) |
|-----------------------|-----------|---------|
|`:missing`             | はい       |         |
|`:exact`               | はい       |         |
|`:contains`            | はい       |         |
|`:text`                | はい       |         |
|`:in` (トークン)          | いいえ         |         |
|`:below` (トークン)       | いいえ         |         |
|`:above` (トークン)       | いいえ         |         |
|`:not-in` (トークン)      | いいえ         |         |
|`:[type]` (参照)  | いいえ         |         |
|`:below` (URI)         | はい       |         |
|`:above` (URI)         | いいえ         | イシュー [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 一般的な検索パラメーター | サポートされています | Comment (コメント) |
|-------------------------| ----------|---------|
| `_id`                   | はい       |         |
| `_lastUpdated`          | はい       |         |
| `_tag`                  | はい       |         |
| `_profile`              | はい       |         |
| `_security`             | はい       |         |
| `_text`                 | いいえ         |         |
| `_content`              | いいえ         |         |
| `_list`                 | いいえ         |         |
| `_has`                  | いいえ         |         |
| `_type`                 | はい       |         |
| `_query`                | いいえ         |         |

| 検索操作       | サポートされています | Comment (コメント) |
|-------------------------|-----------|---------|
| `_filter`               | いいえ         |         |
| `_sort`                 | いいえ         |         |
| `_score`                | いいえ         |         |
| `_count`                | はい       |         |
| `_summary`              | 部分的   | `_summary=count` がサポートされています |
| `_include`              | いいえ         |         |
| `_revinclude`           | いいえ         |         |
| `_contained`            | いいえ         |         |
| `_elements`             | いいえ         |         |

## <a name="persistence"></a>永続化

Microsoft FHIR Server には、プラグ可能な永続化モジュールがあります ([`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) を参照)。

現在、FHIR Server のオープン ソース コードには [Azure Cosmos DB](../cosmos-db/index-overview.md) 用の実装が含まれています。

Cosmos DB は、グローバル分散型のマルチモデル (SQL API、MongoDB API など) データベースです。 これは、さまざまな[整合性レベル](../cosmos-db/consistency-levels.md)をサポートしています。 既定のデプロイ テンプレートでは `Strong` の整合性で FHIR Server を構成しますが、整合性ポリシーは、`x-ms-consistency-level` 要求ヘッダーを使用して要求ごとに変更 (一般には緩和) できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR Server は、アクセス制御のために [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。 特に、`FhirServer:Security:Enabled` 構成パラメーターが `true` に設定され、FHIR Server へのすべての要求 (`/metadata` を除く) で `Authorization` 要求ヘッダーが `Bearer <TOKEN>` に設定されている必要がある場合は、ロールベースのアクセス制御 (RBAC) が適用されます。 トークンには、`roles` 要求で定義されている 1 つ以上のロールが含まれている必要があります。 トークンに、指定されたリソースに対する指定されたアクションを許可するロールが含まれている場合は、要求が許可されます。

現在、特定のロールに対して許可されるアクションは API で*グローバルに*適用されます。

## <a name="next-steps"></a>次の手順

この記事では、Azure API for FHIR でサポートされる FHIR 機能について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Server をデプロイする](fhir-oss-powershell-quickstart.md)