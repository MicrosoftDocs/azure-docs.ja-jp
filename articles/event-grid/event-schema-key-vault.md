---
title: Event Grid ソースとして Azure Key Vault
description: Azure Event Grid で Azure Key Vault のイベント用に提供されているプロパティとスキーマについて説明します
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1f9cbe85de9423484343e4054be8d2d58c6c5e7e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109435"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Event Grid ソースとして Azure Key Vault

この記事では、現在プレビュー段階にある、[Azure Key Vault](../key-vault/index.yml) のイベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

### <a name="available-event-types"></a>使用可能なイベントの種類

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

### <a name="event-examples"></a>イベントの例

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

### <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| ---------- | ----------- |---|
| id | string | このイベントをトリガーしたオブジェクトの ID |
| vaultName | string | このイベントをトリガーしたオブジェクトのキー コンテナー名 |
| objectType | string | このイベントをトリガーしたオブジェクトの種類 |
| objectName | string | このイベントをトリガーしたオブジェクトの名前 |
| version | string | このイベントをトリガーしたオブジェクトのバージョン |
| nbf | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の開始日 (秒単位) |
| exp | number | このイベントをトリガーしたオブジェクトの 1970-01-01T00:00:00Z 以後の有効期限 (秒単位) |

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル  |説明  |
|---------|---------|
| [Azure Event Grid での Key Vault イベントの監視](../key-vault/general/event-grid-overview.md) | Key Vault と Event Grid の統合の概要です。 |
| [チュートリアル:Event Grid を使用した Key Vault イベントの作成と監視](../key-vault/general/event-grid-tutorial.md) | Key Vault に関する Event Grid の通知を設定する方法について説明します。 |


## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成方法の詳細については、「[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)」を参照してください。
* Key Vault の Event Grid との統合の詳細については、「[Azure Event Grid での Key Vault の監視 (プレビュー)](../key-vault/general/event-grid-overview.md)」を参照してください。
* Key Vault の Event Grid との統合に関するチュートリアルについては、「[Azure Event Grid でキー コンテナー通知を受信して応答する (プレビュー)](../key-vault/general/event-grid-tutorial.md)」を参照してください。
* Key Vault と Azure Automation に関する追加のガイダンスについては、以下を参照してください。
    - [Azure Key Vault とは](../key-vault/general/overview.md)
    - [Azure Event Grid での Key Vault の監視 (プレビュー)](../key-vault/general/event-grid-overview.md)
    - [Azure Event Grid でキー コンテナー通知を受信して応答する (プレビュー)](../key-vault/general/event-grid-tutorial.md)
    - [Azure Automation の概要](../automation/index.yml)
