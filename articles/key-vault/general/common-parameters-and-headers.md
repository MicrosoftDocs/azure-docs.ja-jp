---
title: 一般的なパラメーターとヘッダー
description: Key Vault リソースに関連するすべての操作に共通のヘッダーおよびパラメーター。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005811"
---
# <a name="common-parameters-and-headers"></a>一般的なパラメーターとヘッダー

次の情報は、Key Vault リソースに関連するすべての操作に共通しています。

- HTTP `Host` ヘッダーは常に存在する必要があり、また、コンテナーのホスト名がこのヘッダーによって指定される必要があります。 例: `Host: contoso.vault.azure.net`. ほとんどのクライアント技術では、URI から `Host` ヘッダーが入力されることにご留意ください。 たとえば、`GET https://contoso.vault.azure.net/secrets/mysecret{...}` では `Host` が `contoso.vault.azure.net` として設定されます。 つまり、`GET https://10.0.0.23/secrets/mysecret{...}` のような生 IP アドレスを利用してキー コンテナーにアクセスする場合、`Host` ヘッダーの自動値を間違えるため、`Host` ヘッダーにコンテナーのホスト名が含まれるよう、手動で必ず確認する必要があります。
- `{api-version}` は、URI の api-version に置き換えます。
- `{subscription-id}` は、URI のサブスクリプション識別子に置き換えます
- `{resource-group-name}` は、リソース グループに置き換えます。 詳細については、リソース グループを使用した Azure リソースの管理に関するページを参照してください。
- `{vault-name}` は、URI の Key Vault 名に置き換えます。
- Content-Type ヘッダーは application/json に設定します。
- Azure Active Directory (AAD) から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](authentication-requests-and-responses.md)」を参照してください。

## <a name="common-error-response"></a>一般的なエラー応答
このサービスは、HTTP 状態コードを使用して成功または失敗を示します。 さらに、エラーには次の形式の応答が含まれています。

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|要素名 | Type | 説明 |
|---|---|---|
| code | string | 発生したエラーの種類。|
| message | string | エラーの原因の説明。 |



## <a name="see-also"></a>参照
 [Azure Key Vault REST API リファレンス](/rest/api/keyvault/)
