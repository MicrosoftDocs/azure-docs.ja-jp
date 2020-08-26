---
title: Azure CLI を使用してデータ アクセスのための Azure ロールを割り当てる
titleSuffix: Azure Storage
description: Azure CLI を使用して、ロールベースのアクセス制御 (RBAC) を使用して Azure Active Directory セキュリティ プリンシパルへのアクセス許可を割り当てる方法について説明します。 Azure Storage では、Azure AD を使用した認証用の組み込みロールと Azure カスタム ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5714ab17f82f9c4d9c1d00f297c0950e41f54cdc
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827981"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure CLI を使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB またはキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールは、Azure Storage によって定義されます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

この記事では、Azure CLI を使用して、Azure 組み込みロールの一覧を表示し、ロールをユーザーに割り当てる方法について説明します。 Azure CLI の使用の詳細については、「[Azure コマンド ライン インターフェイス (CLI)](/cli/azure)」を参照してください。

## <a name="azure-roles-for-blobs-and-queues"></a>BLOB とキューの Azure ロール

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>リソースのスコープを決定する

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>利用可能な Azure ロールの一覧を表示する

Azure CLI を使用して利用可能な Azure 組み込みロールの一覧を表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) コマンドを使用します。

```azurecli-interactive
az role definition list --out table
```

組み込みの Azure Storage データ ロールが、Azure のその他の組み込みロールとともに表示されます。

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>セキュリティ プリンシパルへの Azure ロールの割り当て

セキュリティ プリンシパルに Azure ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) コマンドを使用します。 コマンドの形式は、割り当てのスコープによって異なります。 次の例は、さまざまなスコープでユーザーにロールを割り当てる方法を示していますが、同じコマンドを使用して、任意のセキュリティ プリンシパルにロールを割り当てることができます。

### <a name="container-scope"></a>コンテナー スコープ

コンテナーに対してスコープが指定されたロールを割り当てるには、コンテナーのスコープを含む文字列を `--scope` パラメーターに指定します。 コンテナーのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

次の例では、コンテナーのレベルにスコープが指定された**ストレージ BLOB データ共同作成者**ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>キュー スコープ

キューに対してスコープが指定されたロールを割り当てるには、キューのスコープを含む文字列を `--scope` パラメーターに指定します。 キューのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

次の例では、キューのレベルにスコープが指定された**ストレージ キュー データ共同作成者**ロールをユーザーに割り当てます。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>ストレージ アカウント スコープ

ストレージ アカウントにスコープが指定されたロールを割り当てるには、ストレージ アカウント リソースのスコープを `--scope` パラメーターに指定します。 ストレージ アカウントのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

次の例は、ストレージ アカウントのレベルで**ストレージ BLOB データ閲覧者**ロールをユーザーに割り当てる方法を示しています。 サンプルの値は必ず実際の値に置き換えてください (\)。

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>リソース グループ スコープ

リソース グループにスコープが指定されたロールを割り当てるには、リソース グループの名前または ID を `--resource-group` パラメーターに指定します。 次の例は、リソース グループのレベルで**ストレージ キュー データ閲覧者**ロールをユーザーに割り当てています。 サンプルの値とブラケット内のプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>サブスクリプション スコープ

サブスクリプションにスコープが指定されたロールを割り当てるには、サブスクリプションのスコープを `--scope` パラメーターに指定します。 サブスクリプションのスコープのフォームは次のとおりです。

```
/subscriptions/<subscription>
```

次の例は、ストレージ アカウントのレベルで**ストレージ BLOB データ閲覧者**ロールをユーザーに割り当てる方法を示しています。 サンプルの値は必ず実際の値に置き換えてください。 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>次のステップ

- [RBAC と Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する](../../role-based-access-control/role-assignments-powershell.md)
- [RBAC と Azure PowerShell を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-powershell.md)
- [Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)
