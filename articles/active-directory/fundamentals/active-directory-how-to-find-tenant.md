---
title: テナント ID を見つける方法 - Azure Active Directory
description: 既存の Azure サブスクリプションで Azure Active Directory テナントを見つける方法の手順。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96326592"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Azure Active Directory のテナント ID を見つける方法

Azure サブスクリプションには、Azure Active Directory (Azure AD) との信頼関係があります。 サブスクリプションでは、ユーザー、サービス、デバイスを認証するために Azure AD が信頼されます。 各サブスクリプションには、それに関連付けられているテナント ID があり、いくつかの方法で、お使いのサブスクリプションのテナント ID を見つけることができます。

## <a name="find-tenant-id-through-the-azure-portal"></a>Azure portal を通してテナント ID を見つける

1. [Azure portal](https://portal.azure.com) にサインインします。
 
1. **[Azure Active Directory]** を選択します。

1. **[プロパティ]** を選択します。

1. 次に、 **[テナント ID]** フィールドまで下にスクロールします。 テナント ID はこのボックスに表示されます。

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory - [プロパティ] - [テナント ID] - [テナント ID] フィールド":::

## <a name="find-tenant-id-with-powershell"></a>PowerShell を使用してテナント ID を見つける

テナントは、プログラムによって見つけることもできます。 Azure PowerShell を使用してテナント ID を見つけるには、コマンドレット `Get-AzTenant` を使用します。

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
詳細については、[Get-AzTenant](/powershell/module/az.accounts/get-aztenant) の、Azure PowerShell のこのコマンドレット リファレンスを参照してください。


## <a name="find-tenant-id-with-cli"></a>CLI を使用してテナント ID を見つける
コマンド ライン インターフェイスを使用してテナント ID を見つける場合は、[Azure CLI](/cli/azure/install-azure-cli) または [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/) でもそれを行うことができます。 

Azure CLI の場合は、次の例に示すように、**az login**、**az account list**、**az account tenant list** のいずれかのコマンドを使用します。 各コマンドからの出力では、お使いのサブスクリプションそれぞれの **tenantId** プロパティに注目してください。

```azurecli-interactive
az login
az account list
az account tenant list
```

詳細については、[az login](/cli/azure/reference-index#az_login) のコマンド リファレンス、[az account](/cli/azure/ext/account/account) のコマンド リファレンス、または [az account tenant](/cli/azure/ext/account/account/tenant) のコマンド リファレンスを参照してください。


Microsoft 365 CLI の場合は、次の例に示すように、コマンドレット **tenant id** を使用します。
 
```cli
m365 tenant id get
```

詳細については、Microsoft 365 の [tenant id get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) のコマンド リファレンスを参照してください。


## <a name="next-steps"></a>次のステップ

- 新しい Azure AD テナントを作成するには、「[クイックスタート:Azure Active Directory で新しいテナントを作成する](active-directory-access-create-new-tenant.md)」を参照してください。

- サブスクリプションをテナントに関連付けるまたは追加する方法については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](active-directory-how-subscriptions-associated-directory.md)」を参照してください。

- オブジェクト ID を見つける方法については、「[ユーザー オブジェクト ID を見つける](/partner-center/find-ids-and-domain-names#find-the-user-object-id)」を参照してください。