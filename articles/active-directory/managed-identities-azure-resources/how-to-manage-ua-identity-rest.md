---
title: REST を使用してユーザー割り当てマネージド ID を管理する - Azure AD
description: REST API を呼び出すためのユーザー割り当てマネージド ID を作成、一覧表示、削除する詳細な手順。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546694"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>REST API 呼び出しを使用して、ユーザー割り当てマネージド ID を作成、一覧表示、削除する

Azure リソースのマネージド ID を使用すれば、Azure サービスで Azure AD Authentication をサポートするサービスを認証することができます。この場合、コードに資格情報を挿入する必要はありません。 

この記事では、CURL を使用して REST API を呼び出し、ユーザー割り当てマネージド ID を作成、一覧表示、削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#managed-identity-types)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事で取り上げるすべてのコマンドは、クラウドでもローカルでも実行できます。
    - クラウドで実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。
    - ローカルで実行するには、[curl](https://curl.haxx.se/download.html) と [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

## <a name="obtain-a-bearer-access-token"></a>ベアラー アクセス トークンを取得する

1. ローカルで実行している場合は、Azure CLI を使用して Azure にサインインします。

    ```
    az login
    ```

1. [az account get-access-token](/cli/azure/account#az_account_get_access_token) を使用してアクセス トークンを取得する

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する 

ユーザー割り当てマネージド ID を作成するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**要求ヘッダー**

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

**要求本文**

|名前  |説明  |
|---------|---------|
|location     | 必須。 リソースの場所。        |

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

ユーザー割り当てマネージド ID の一覧表示/読み取りには、お使いのアカウントに[マネージド ID オペレーター](../../role-based-access-control/built-in-roles.md#managed-identity-operator)ロールまたは[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

ユーザー割り当てマネージド ID を削除するには、お使いのアカウントに[マネージド ID 共同作成者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)ロールの割り当てが必要です。

> [!NOTE]
> ユーザー割り当てマネージド ID を削除しても、それが割り当てられていたリソースから参照が削除されることはありません。 CURL を使用して VM からユーザー割り当てマネージド ID を削除する場合は、「[Azure VM からユーザー割り当て ID を削除する](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)」を参照してください。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|要求ヘッダー  |説明  |
|---------|---------|
|*Content-Type*     | 必須。 `application/json` を設定します。        |
|*承認*     | 必須。 有効な `Bearer` アクセス トークンを設定します。        |

## <a name="next-steps"></a>次のステップ

CURL を使用して Azure VM/VMSS にユーザー割り当てマネージド ID を割り当てる方法については、[REST API 呼び出しを使用する Azure VM での Azure リソースのマネージド ID の構成](qs-configure-rest-vm.md#user-assigned-managed-identity)と [REST API 呼び出しを使用する仮想マシン スケール セットでの Azure リソースのマネージド ID の構成](qs-configure-rest-vmss.md#user-assigned-managed-identity)に関するページを参照してください。
