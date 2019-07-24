---
title: 一般的なパラメーターとヘッダー
description: Key Vault リソースに関連するすべての操作に共通のヘッダーおよびパラメーター。
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: cc6c937e37ebb7919ca271e765ad6460c104e43b
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405047"
---
# <a name="common-parameters-and-headers"></a>一般的なパラメーターとヘッダー

次の情報は、Key Vault リソースに関連するすべての操作に共通しています。

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

|要素名 | type | 説明 |
|---|---|---|
| code | 文字列 | 発生したエラーの種類。|
| message | 文字列 | エラーの原因の説明。 |



## <a name="see-also"></a>関連項目
 [Azure Key Vault REST API リファレンス](/rest/api/keyvault/)
