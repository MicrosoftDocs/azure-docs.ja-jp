---
title: Event Grid ソースとして Azure Key Vault
description: Azure Event Grid で Azure Key Vault のイベント用に提供されているプロパティとスキーマについて説明します
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363408"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Event Grid ソースとして Azure Key Vault

この記事では、[Azure Key Vault](../key-vault/index.yml) でのイベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。


## <a name="available-event-types"></a>使用可能なイベントの種類

Azure Key Vault アカウントでは、以下の種類のイベントが生成されます。

| イベントのフル ネーム | イベントの表示名 | 説明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificate New Version Created (証明書の新しいバージョンが作成されました) | 新しい証明書または証明書の新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.CertificateNearExpiry | Certificate Near Expiry (証明書の有効期限が近づいています) | 現在のバージョンの証明書が有効期限切れになろうとしているときにトリガーされます。 (イベントは、有効期限の 30 日前にトリガーされます)。 |
| Microsoft.KeyVault.CertificateExpired | 証明書の有効期限が切れた | 証明書の有効期限が切れたときにトリガーされます。 |
| Microsoft.KeyVault.KeyNewVersionCreated | New Version Created (新しいバージョンが作成されました) | 新しいキーまたはキーの新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.KeyNearExpiry | Key Near Expiry (キーの有効期限が近づいています) | 現在のバージョンのキーが有効期限切れになろうとしているときにトリガーされます。 (イベントは、有効期限の 30 日前にトリガーされます)。 |
| Microsoft.KeyVault.KeyExpired | Key Expired (キーの有効期限が切れました) | キーの有効期限が切れたときにトリガーされます。 |
| Microsoft.KeyVault.SecretNewVersionCreated | Secret New Version Created (シークレットの新しいバージョンが作成されました) | 新しいシークレットまたはシークレットの新しいバージョンが作成されたときにトリガーされます。 |
| Microsoft.KeyVault.SecretNearExpiry | Secret Near Expiry (シークレットの有効期限が近づいています) | 現在のバージョンのシークレットが有効期限切れになろうとしているときにトリガーされます。 (イベントは、有効期限の 30 日前にトリガーされます)。 |
| Microsoft.KeyVault.SecretExpired | Secret Expired (シークレットの有効期限が切れました) | シークレットの有効期限が切れたときにトリガーされます。 |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Vault Access Policy Changed (Vault のアクセス ポリシーが変更されました) | Key Vault のアクセス ポリシーが変更されたときにトリガーされます。 Key Vault のアクセス許可モデルが Azure ロールベースのアクセス制御に、またはそれから変更されるシナリオが含まれます。   |

## <a name="event-examples"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**Microsoft.KeyVault.SecretNewVersionCreated** のスキーマを示しています。

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**Microsoft.KeyVault.SecretNewVersionCreated** のスキーマを示しています。

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>イベントのプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)
イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | App Configuration イベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |


# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | App Configuration イベント データ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---
 

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| ---------- | ----------- |---|
| `id` | string | このイベントをトリガーしたオブジェクトの ID |
| `vaultName` | string | このイベントをトリガーしたオブジェクトのキー コンテナー名 |
| `objectType` | string | このイベントをトリガーしたオブジェクトの種類 |
| `objectName` | string | このイベントをトリガーしたオブジェクトの名前 |
| `version` | string | このイベントをトリガーしたオブジェクトのバージョン |
| `nbf` | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の開始日 (秒単位) |
| `exp` | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の有効期限 (秒単位) |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル  |説明  |
|---------|---------|
| [Azure Event Grid での Key Vault イベントの監視](../key-vault/general/event-grid-overview.md) | Key Vault と Event Grid の統合の概要です。 |
| [チュートリアル:Event Grid を使用した Key Vault イベントの作成と監視](../key-vault/general/event-grid-logicapps.md) | Key Vault に関する Event Grid の通知を設定する方法について説明します。 |


## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成方法の詳細については、「[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)」を参照してください。
* Key Vault の詳細については、[Azure Key Vault の概要](../key-vault/general/overview.md)に関する記事を参照してください。

