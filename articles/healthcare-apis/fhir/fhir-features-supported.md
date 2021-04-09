---
title: Azure でサポートされる FHIR 機能 - Azure API for FHIR
description: この記事では、Azure API for FHIR で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 9bd61d65d6d64dac6081d3491deb8a15efc4a45b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048421"
---
# <a name="features"></a>特徴

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、FHIR Server の主な機能を一覧表示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 読み取り                           | はい       | Yes       | はい       |                                                     |
| vread                          | はい       | Yes       | はい       |                                                     |
| update                         | はい       | Yes       | はい       |                                                     |
| オプティミスティック ロック付きの update | はい       | Yes       | はい       |                                                     |
| update (条件付き)           | はい       | Yes       | はい       |                                                     |
| patch                          | いいえ        | いいえ        | いいえ        |                                                     |
| delete                         | はい       | Yes       | Yes       |  下記のメモを参照してください。                                   |
| delete (条件付き)           | いいえ        | いいえ        | いいえ        |                                                     |
| history                        | はい       | Yes       | はい       |                                                     |
| create                         | はい       | Yes       | はい       | POST/PUT の両方をサポートします                               |
| create (条件付き)           | はい       | Yes       | はい       | イシュー [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| 検索                         | Partial   | Partial   | Partial   | 下の「検索」セクションを参照してください。                           |
| chained search                 | Yes       | はい       | 部分的   | 下記のメモ2を参照してください。                                   |
| reverse chained search         | Yes       | はい       | 部分的   | 下記のメモ2を参照してください。                                   |
| capabilities                   | はい       | Yes       | はい       |                                                     |
| batch (バッチ)                          | はい       | Yes       | はい       |                                                     |
| transaction                    | いいえ        | はい       | いいえ        |                                                     |
| paging                         | 部分的   | Partial   | 部分的   | `self` と `next` がサポートされています                     |
| intermediaries                 | いいえ        | いいえ        | いいえ        |                                                     |

> [!Note]
> FHIR 仕様で定義された削除では、仕様の要件により、削除後にリソースに対して後続の非バージョン固有読み取りを行うと、410 HTTP 状態コードが返され、検索によってリソースを見つけられなくなります。 Azure API for FHIR でも、リソース (すべての履歴を含む) を完全に削除することができます。 リソースを完全に削除するには、パラメーター設定 `hardDelete` を true (`DELETE {server}/{resource}/{id}?hardDelete=true`) にします。 このパラメーターを指定しなかった場合や、`hardDelete` を false に設定した場合は、その後もリソースの履歴バージョンを利用できます。


 **注 2**
* CosmosDB のチェーンと逆のチェーン FHIR 検索に関する MVP サポートを追加します。 

  Cosmos でサポートされている Azure API for FHIR とオープンソースの FHIR サーバーでは、チェーン検索と逆連鎖検索は MVP 実装です。 Cosmos DB でチェーン検索を実行するために、この実装は検索式を処理し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリが100を超える結果を返す場合は、エラーがスローされます。 既定では、チェーン検索は機能フラグの背後にあります。 Cosmos DB でチェーン検索を使用するには、ヘッダーを使用し `x-ms-enable-chained-search: true` ます。 詳細については、「 [PR 1695](https://github.com/microsoft/fhir-server/pull/1695)」を参照してください。

## <a name="search"></a>検索

すべての検索パラメーターの種類がサポートされています。 

| 検索パラメーターの種類 | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | はい       | Yes       | はい       |         |
| Date/DateTime         | はい       | Yes       | はい       |         |
| String                | はい       | Yes       | はい       |         |
| トークン                 | はい       | Yes       | はい       |         |
| リファレンス             | はい       | Yes       | はい       |         |
| Composite             | はい       | Yes       | はい       |         |
| Quantity              | はい       | Yes       | はい       |         |
| URI                   | はい       | Yes       | はい       |         |
| Special               | いいえ        | いいえ        | いいえ        |         |


| 修飾子             | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | はい       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | はい       |         |
|`:[type]` (参照)  | Yes       | Yes       | Yes       |         |
|`:not`                 | Yes       | Yes       | Yes       |         |
|`:below` (URI)         | はい       | Yes       | Yes       |         |
|`:above` (URI)         | いいえ        | いいえ        | いいえ        | イシュー [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (トークン)          | いいえ        | いいえ        | いいえ        |         |
|`:below` (トークン)       | いいえ        | いいえ        | いいえ        |         |
|`:above` (トークン)       | いいえ        | いいえ        | いいえ        |         |
|`:not-in` (トークン)      | いいえ        | いいえ        | いいえ        |         |

| 一般的な検索パラメーター | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | はい       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_type`                 | Yes       | Yes       | はい       | イシュー [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Yes       | Yes       | はい       |         |
| `_profile`              | 部分的   | Partial   | 部分的   | STU3 でサポートされています。 2021 年 2 月 20 日 **以降** にデータベースを作成した場合は、R4 でもサポートされます。 Microsoft では、2021 年 2 月 20 日より前に作成されたデータベースでも _profile を利用できるようにするべく取り組んでいます。 |
| `_text`                 | いいえ        | いいえ        | いいえ        |         |
| `_content`              | いいえ        | いいえ        | いいえ        |         |
| `_has`                  | いいえ        | いいえ        | いいえ        |         |
| `_query`                | いいえ        | いいえ        | いいえ        |         |
| `_filter`               | いいえ        | いいえ        | いいえ        |         |

| 検索結果のパラメーター | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | はい       | Yes       | Yes       | イシュー [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | はい       | Yes       | はい       | `_count` の上限は 1000 文字です。 1000 より大きい値に設定すると、1000 個だけが返され、バンドルで警告が返されます。 |
| `_include`              | はい       | Yes       | Yes       |含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS に含めても :iterate のサポートは含まれません。|
| `_revinclude`           | はい       | Yes       | Yes       | 含まれる項目は 100 に制限されています。 Cosmos DB 上の PaaS や OSS に含めても [:iterate のサポートは含まれません](https://github.com/microsoft/fhir-server/issues/1313)。 イシュー [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | 部分的   | Partial   | Partial   | `_summary=count` がサポートされています |
| `_total`                | Partial   | Partial   | Partial   | `_total=none` および `_total=accurate`      |
| `_sort`                 | Partial   | Partial   | Partial   |   `_sort=_lastUpdated` がサポートされています       |
| `_contained`            | いいえ        | いいえ        | いいえ        |         |
| `containedType`         | いいえ        | いいえ        | いいえ        |         |
| `_score`                | いいえ        | いいえ        | いいえ        |         |

## <a name="extended-operations"></a>拡張操作

サポートされており、RESTful API を拡張するあらゆる操作。

| 検索パラメーターの種類 | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|------------------------|-----------|-----------|-----------|---------|
| $export (システム全体) | はい       | Yes       | はい       |         |
| Patient/$export        | はい       | Yes       | はい       |         |
| Group/$export          | はい       | Yes       | Yes       |         |
| $convert-data          | Yes       | Yes       | はい       |         |


## <a name="persistence"></a>永続化

Microsoft FHIR Server には、プラグ可能な永続化モジュールがあります ([`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) を参照)。

現在、FHIR Server のオープン ソース コードには [Azure Cosmos DB](../../cosmos-db/index-overview.md) および [SQL Database](https://azure.microsoft.com/services/sql-database/) 用の実装が含まれています。

Cosmos DB は、グローバル分散型のマルチモデル (SQL API、MongoDB API など) データベースです。 これは、さまざまな[整合性レベル](../../cosmos-db/consistency-levels.md)をサポートしています。 既定のデプロイ テンプレートでは `Strong` の整合性で FHIR Server を構成しますが、整合性ポリシーは、`x-ms-consistency-level` 要求ヘッダーを使用して要求ごとに変更 (一般には緩和) できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR Server は、アクセス制御のために [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。 特に、`FhirServer:Security:Enabled` 構成パラメーターが `true` に設定され、FHIR Server へのすべての要求 (`/metadata` を除く) で `Authorization` 要求ヘッダーが `Bearer <TOKEN>` に設定されている必要がある場合は、ロールベースのアクセス制御 (RBAC) が適用されます。 トークンには、`roles` 要求で定義されている 1 つ以上のロールが含まれている必要があります。 トークンに、指定されたリソースに対する指定されたアクションを許可するロールが含まれている場合は、要求が許可されます。

現在、特定のロールに対して許可されるアクションは API で *グローバルに* 適用されます。

## <a name="service-limits"></a>サービスの制限

* [**要求ユニット (RU)**](../../cosmos-db/concepts-limits.md) - Azure API for FHIR のポータルで最大 10,000 RU を構成できます。 少なくとも 400 RU か 10 RU/GB が必要になります (大きい方)。 必要な単位が 10,000 RU を超える場合、サポート チケットを発行して増やすことができます。 利用できる最大値は 1,000,000 です。

* **コンカレント接続** と **インスタンス** - 既定では、クラスター内の 2 つのインスタンス上で 5 つのコンカレント接続が用意されています (同時要求は合計で 10)。 同時要求がさらに必要であると思われる場合、サポート チケットを開き、ニーズの詳細を含めてください。

* **バンドル サイズ** - 各バンドルは 500 項目に制限されています。

* **データ サイズ** - データとドキュメントはそれぞれ 2 MB より少しばかり少なくする必要があります。

## <a name="performance-expectations"></a>パフォーマンスの期待値

システムのパフォーマンスは、RU の数、コンカレント接続、実行している操作の種類 (Put や Post など) に依存します。 構成された RU に基づく期待値の一般的範囲は以下のようになります。 一般的に、RU を増やせば、パフォーマンスが直線的に上がります。

| RU の数 | リソース/sec |    最大ストレージ (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1,000    | 100-150       |      100  |
| 10,000   | 225-400       |      1,000  |
| 100,000  | 2,500-4,000   |      10,000  |

注:Cosmos DB の要件ごとに、ストレージの GB あたり 10 RU/秒の最小スループットの要件があります。 詳細については、「[Azure Cosmos DB サービスのクォータ](../../cosmos-db/concepts-limits.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR でサポートされる FHIR 機能について学習しました。 次は Azure API for FHIR をデプロイします。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)
