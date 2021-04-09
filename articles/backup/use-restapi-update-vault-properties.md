---
title: REST API を使用して Recovery Services コンテナー構成を更新する
description: この記事では、REST API を使用してコンテナーの構成を更新する方法について説明します。
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91567827"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>REST API を使用して Azure Recovery Services コンテナーの構成を更新する

この記事では、REST API を使用して Azure Recovery Services コンテナーのバックアップ関連の構成を更新する方法について説明します。

## <a name="soft-delete-state"></a>論理的な削除状態

保護された項目のバックアップの削除は、監視する必要がある重要な操作です。 誤って削除されるのを防ぐために、Azure Recovery Services コンテナーには論理的な削除機能があります。 この機能を使用すると、削除されたバックアップを必要に応じて削除後の一定期間内で復元できます。

ただし、この機能が不要なシナリオもあります。 Azure Recovery Services コンテナーは、論理的に削除されたものも含め、その中にバックアップ項目がある場合は削除できません。 コンテナーをすぐに削除する必要がある場合、このことにより問題が発生する可能性があります。 たとえばデプロイ操作では、作成されたリソースが同じワークフローでクリーンアップされることがよくあります。 デプロイはコンテナーを作成し、項目のバックアップを構成して、テストの復元を実行してから、バックアップ項目とコンテナーの削除に進むことができます。 コンテナーの削除に失敗した場合、デプロイ全体が失敗する可能性があります。 論理的な削除を無効にすることが、即時の削除を保証する唯一の方法です。

そのため、シナリオに応じて、特定のコンテナーの論理的な削除を無効にするかどうかを慎重に選択する必要があります。 詳細については、[論理的な削除に関する記事](backup-azure-security-feature-cloud.md)を参照してください。

### <a name="fetch-soft-delete-state-using-rest-api"></a>REST API を使用した論理的な削除状態の取得

既定では、新しく作成されたあらゆる Recovery Services コンテナーに対して、論理的な削除状態が有効になります。 コンテナーの論理的な削除状態を取得/更新するには、バックアップ コンテナーの構成に関連する [REST API ドキュメント](/rest/api/backup/backupresourcevaultconfigs)を使用します

コンテナーの現在の論理的な削除状態を取得するには、次の *GET* 操作を使用します

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

GET URI には、`{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}` のパラメーターがあります。 この例では、`{vaultName}` は "testVault" で、`{vaultresourceGroupName}` は "testVaultRG" です。 すべての必須パラメーターは URI で指定されるため、別の要求本文は不要です。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Responses

'GET' 操作に対する正常な応答は次のようになります。

|名前  |種類  |説明  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [OK]        |

##### <a name="example-response"></a>応答の例

'GET' 要求を送信すると、200 (successful) 応答が返されます。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>REST API を使用した論理的な削除状態の更新

REST API を使用して Recovery Services コンテナーの論理的な削除状態を更新するには、次の *PUT* 操作を使用します

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

PUT URI には、`{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}` の各パラメーターがあります。 この例では、`{vaultName}` は "testVault" で、`{vaultresourceGroupName}` は "testVaultRG" です。 URI を上記の値に置き換えると、URI は次のようになります。

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>要求本文を作成する

要求本文を作成するには、以下の一般的な定義が使用されます

詳細については、[REST API のドキュメント](/rest/api/backup/backupresourcevaultconfigs/update#request-body)を参照してください

|名前  |必須  |種類  |説明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  省略可能な eTag       |
|location     |  true       |String         |   リソースの場所      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  コンテナーのプロパティ       |
|tags     |         | Object        |     リソース タグ    |

#### <a name="example-request-body"></a>要求本文の例

次の例は、論理的な削除状態を 'disabled' に更新するために使用されます。

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>PATCH 操作の応答

'PATCH' 操作に対する正常な応答は次のようになります。

|名前  |種類  |説明  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | [OK]        |

##### <a name="example-response-for-the-patch-operation"></a>PATCH 操作の応答例

'PATCH' 要求を送信すると、200 (successful) 応答が返されます。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>次のステップ

[このコンテナーに Azure VM をバックアップするためのバックアップ ポリシーを作成します](backup-azure-arm-userestapi-createorupdatepolicy.md)。

Azure REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Recovery Services プロバイダー REST API](/rest/api/recoveryservices/)
- [Azure Rest API の開始](/rest/api/azure/)
