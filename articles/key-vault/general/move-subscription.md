---
title: Azure Key Vault のコンテナーを別のサブスクリプションに移動する | Microsoft Docs
description: キー コンテナーを別のサブスクリプションに移動するためのガイダンス。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: b37b327a535b716bbce845cd5883e58ec5379c48
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782721"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Azure Key Vault を別のサブスクリプションに移動する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

> [!IMPORTANT]
> **キー コンテナーを別のサブスクリプションに移動すると、環境に破壊的変更が生じます。**
> キー コンテナーを新しいサブスクリプションに移動することを決める前に、必ずこの変更の影響について理解し、この記事のガイダンスに慎重に従うようにしてください。
> 管理対象サービス ID (MSI) を使用している場合は、このドキュメントの最後にある移動後の手順を参照してください。 

キー コンテナーを作成すると、そのキー コンテナーは、それが作成されたサブスクリプションの既定の Azure Active Directory テナント ID に自動的に関連付けられます。 また、すべてのアクセス ポリシー エントリがこのテナント ID に関連付けられます。 テナント A からテナント B に Azure サブスクリプションを移行した場合、テナント B のサービス プリンシパル (ユーザーとアプリケーション) は既存のキー コンテナーにアクセスできなくなります。この問題を解決するには、次の操作を行う必要があります。

* そのサブスクリプションにあるすべての既存のキー コンテナーに関連付けられたテナント ID を、テナント B に変更する。
* すべての既存のアクセス ポリシー エントリを削除する。
* テナント B に関連付けられた新しいアクセス ポリシー エントリを追加する。

## <a name="limitations"></a>制限事項

一部のサービス プリンシパル (ユーザーとアプリケーション) は特定のテナントにバインドされます。 別のテナントのサブスクリプションにキー コンテナーを移動した場合、特定のサービス プリンシパルへのアクセスを復元できなくなる可能性があります。 キー コンテナーを移動するテナントに、すべての重要なサービス プリンシパルが存在することを確認してください。

## <a name="design-considerations"></a>設計上の考慮事項

組織は、サブスクリプション レベルでの適用または除外を使用して Azure Policy を実装している可能性があります。 キー コンテナーが現在存在するサブスクリプションと、キー コンテナーの移動先のサブスクリプションには、異なるポリシー割り当てのセットが存在する場合があります。 ポリシー要件の競合により、アプリケーションが中断される可能性があります。

### <a name="example"></a>例

2 年間有効な証明書を作成するアプリケーションがキー コンテナーに接続されているとします。 キー コンテナーの移動先のサブスクリプションには、1 年より長い期間有効な証明書の作成をブロックするポリシー割り当てがあります。 キー コンテナーを新しいサブスクリプションに移動すると、2 年間有効な証明書を作成する操作は、Azure Policy の割り当てによってブロックされます。

### <a name="solution"></a>解決策

Azure portal の Azure Policy ページにアクセスし、現在のサブスクリプションと移動先のサブスクリプションのポリシー割り当てを調べて、不一致がないことを確認します。

## <a name="prerequisites"></a>前提条件

* キー コンテナーが存在する現在のサブスクリプションに対する共同作成者レベル以上のアクセス権。
* キー コンテナーを移動するサブスクリプションに対する共同作成者レベル以上のアクセス権。
* 新しいサブスクリプションのリソース グループ。

## <a name="procedure"></a>手順

もし 

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>同じテナントの新しいサブスクリプションへの Key Vault の移動

1. Azure Portal にログインする
2. お使いのキー コンテナーに移動する
3. [概要] タブをクリックする
4. [移動] ボタン を選択する
5. ドロップダウン リストのオプションから [別のサブスクリプションに移動する] を選択する
6. キー コンテナーを移動するリソース グループを選択する
7. リソースの移動に関する警告を確認する
8. [OK] を選択する

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>キー コンテナーを新しいテナントのサブスクリプションに移動した場合の追加の手順

お使いのキー コンテナーを新しいテナントのサブスクリプションに移動した場合、そのテナントの ID を手動で更新して、古いアクセス ポリシーを削除する必要があります。 ここでは、PowerShell と Azure CLI でこれらの手順を実行するためのチュートリアルを紹介します。 PowerShell を使用している場合は、お使いの現在選択されているスコープ以外のリソースを表示できるように、次に示す Clear-AzContext コマンドを実行する必要がある場合があります。 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

これで、コンテナーが正しいテナント ID に関連付けられ、古いアクセス ポリシー エントリが削除されたので、Azure PowerShell の [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) コマンドレットまたは Azure CLI の [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを使用して新しいアクセス ポリシー エントリを設定します。

Azure リソースのマネージド ID を使用している場合は、新しい Azure AD テナントにも更新する必要があります。 マネージド ID の詳細については、「[マネージド ID で Key Vault の認証を提供する](managed-identity.md)」を参照してください。

MSI を使用している場合は、古い ID が正しい AAD テナントに存在しなくなるため、MSI ID も更新する必要があります。 この問題を解決するには、次のドキュメントを参照してください。 

* [MSI の更新](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [サブスクリプションを新しいディレクトリに移転する](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)


