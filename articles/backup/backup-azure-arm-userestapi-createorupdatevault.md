---
title: REST API を使用して Recovery Services コンテナーを作成する
description: この記事では、REST API を使用して Azure VM Backup のバックアップ操作と復元操作を管理する方法について説明します。
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002938"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>REST API を使用して Azure Recovery Services コンテナーを作成する

REST API を使用して Azure Recovery Services コンテナーを作成する手順の概要は、[コンテナー作成 REST API](/rest/api/recoveryservices/vaults/createorupdate) に関するドキュメントで説明されています。 このドキュメントを参考にして、"West US" に "testVault" という名前のコンテナーを作成しましょう。

Azure Recovery Services コンテナーを作成または更新するには、次の *PUT* 操作を使用します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>要求を作成する

*PUT* 要求を作成するには、`{subscription-id}` パラメーターが必要です。 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli)を参照してください｡ リソースの `{resourceGroupName}` と `{vaultName}` を `api-version` パラメーターと共に定義します。 この記事では、`api-version=2016-06-01` を使用します。

次のヘッダーは必須です｡

| 要求ヘッダー   | 説明 |
|------------------|-----------------|
| *Content-Type:*  | 必須。 `application/json` を設定します。 |
| *Authorization:* | 必須。 有効な `Bearer` [アクセス トークン](/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します。 |

要求の作成方法の詳細については、「[Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse)」(REST API 要求/応答のコンポーネント) を参照してください。

## <a name="create-the-request-body"></a>要求本文を作成する

要求本文を作成するには、以下の一般的な定義が使用されます。

|名前  |必須  |Type  |説明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  省略可能な eTag       |
|location     |  true       |String         |   リソースの場所      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  コンテナーのプロパティ       |
|sku     |         |  [SKU](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    各 Azure リソースの一意のシステム ID を示します     |
|tags     |         | Object        |     リソース タグ    |

コンテナー名とリソース グループ名は PUT URI で提供することに注意してください。 要求本文では場所を定義します。

## <a name="example-request-body"></a>要求本文の例

次の例の本文は、"West US" にコンテナーを作成するために使用されています。 場所を指定します。 SKU は常に "Standard" です。

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Responses

Recovery Services コンテナーの作成または更新操作には、2 種類の成功応答があります。

|名前  |Type  |説明  |
|---------|---------|---------|
|200 OK     |   [コンテナー](/rest/api/recoveryservices/vaults/createorupdate#vault)      | [OK]        |
|201 Created     | [コンテナー](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   作成済み      |

REST API の応答の詳細については、「[Process the response message](/rest/api/azure/#process-the-response-message)」(応答メッセージを処理する) を参照してください。

### <a name="example-response"></a>応答の例

前の要求本文の例からの圧縮された *201 Created* 応答では、*id* が割り当てられ、*provisioningState* が *Succeeded* であることが示されています。

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>次のステップ

[このコンテナーに Azure VM をバックアップするためのバックアップ ポリシーを作成します](backup-azure-arm-userestapi-createorupdatepolicy.md)。

Azure REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Recovery Services プロバイダー REST API](/rest/api/recoveryservices/)
- [Azure Rest API の開始](/rest/api/azure/)
