---
title: サブスクリプション移行後のキー コンテナー テナント ID の変更 - Azure Key Vault | Microsoft Docs
description: サブスクリプションを別のテナントに移行した後にキー コンテナーのテナント ID を切り替える方法について説明します。
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428914"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>サブスクリプション移行後のキー コンテナー テナント ID の変更

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


サブスクリプション内で新しいキー コンテナーを作成すると、作成したキー コンテナーは、そのサブスクリプションの既定の Azure Active Directory テナント ID に自動的に関連付けられます。 また、すべてのアクセス ポリシー エントリがこのテナント ID に関連付けられます。 

テナント A からテナント B に Azure サブスクリプションを移行する場合、テナント B のプリンシパル (ユーザーとアプリケーション) は既存のキー コンテナーにアクセスできなくなります。この問題を解決するには、次のことを行う必要があります。

* そのサブスクリプションにあるすべての既存のキー コンテナーに関連付けられたテナント ID を、テナント B に変更する。
* すべての既存のアクセス ポリシー エントリを削除する。
* テナント B に関連付けられた新しいアクセス ポリシー エントリを追加する。

たとえば、テナント A からテナント B に移行したサブスクリプションに "myvault" というキー コンテナーがある場合は、Azure PowerShell を使用することで、テナント ID を変更して古いアクセス ポリシーを削除できます。

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Azure CLI を使用することもできます。

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

これで、コンテナーが正しいテナント ID に関連付けられ、古いアクセス ポリシー エントリが削除されたので、Azure PowerShell の [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) コマンドレットまたは Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを使用して新しいアクセス ポリシー エントリを設定します。

Azure リソースのマネージド ID を使用している場合は、新しい Azure AD テナントにも更新する必要があります。 マネージド ID の詳細については、「[マネージド ID で Key Vault の認証を提供する](managed-identity.md)」を参照してください。


MSI を使用している場合は、古い ID が正しい AAD テナントに存在しなくなるため、MSI ID も更新する必要があります。

## <a name="next-steps"></a>次の手順

Azure Key Vault に関する質問がある場合は、[Azure Key Vault フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。
