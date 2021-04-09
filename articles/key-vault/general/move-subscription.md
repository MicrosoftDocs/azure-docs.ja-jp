---
title: Azure Key Vault のコンテナーを別のサブスクリプションに移動する | Microsoft Docs
description: キー コンテナーを別のサブスクリプションに移動するためのガイダンス。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: a84627b2b426385d21ad72b85780db86e7dd5ee3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096082"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Azure Key Vault を別のサブスクリプションに移動する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

> [!IMPORTANT]
> **キー コンテナーを別のサブスクリプションに移動すると、環境に破壊的変更が生じます。**
> キー コンテナーを新しいサブスクリプションに移動することを決める前に、必ずこの変更の影響について理解し、この記事のガイダンスに慎重に従うようにしてください。
> 管理対象サービス ID (MSI) を使用している場合は、このドキュメントの最後にある移動後の手順を参照してください。 

[Azure Key Vault](overview.md) は、それが作成されたサブスクリプションの既定の [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) テナント ID に自動的に関連付けられます。 こちらの[ガイド](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)に従って、サブスクリプションに関連付けられているテナント ID を見つけることができます。 また、すべてのアクセス ポリシー エントリとロールの割り当てもこのテナント ID に関連付けられます。  テナント A からテナント B に Azure サブスクリプションを移行した場合、テナント B のサービス プリンシパル (ユーザーとアプリケーション) は既存のキー コンテナーにアクセスできなくなります。この問題を解決するには、次の操作を行う必要があります。

* そのサブスクリプションにあるすべての既存のキー コンテナーに関連付けられたテナント ID を、テナント B に変更する。
* すべての既存のアクセス ポリシー エントリを削除する。
* テナント B に関連付けられた新しいアクセス ポリシー エントリを追加する。

Azure Key Vault と Azure Active Directory の詳細については、次を参照してください
- [Azure Key Vault について](overview.md)
- [Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)
- [テナント ID を検索する方法](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>制限事項

> [!IMPORTANT]
> **ディスク暗号化に使用されるキー コンテナーは移動できません** VM のディスク暗号化にキー コンテナーを使用している場合、ディスク暗号化が有効な間は、そのキー コンテナーを別のリソース グループまたはサブスクリプションに移動できません。 キー コンテナーを新しいリソース グループまたはサブスクリプションに移動する前に、ディスクの暗号化を無効にする必要があります。 

一部のサービス プリンシパル (ユーザーとアプリケーション) は特定のテナントにバインドされます。 別のテナントのサブスクリプションにキー コンテナーを移動した場合、特定のサービス プリンシパルへのアクセスを復元できなくなる可能性があります。 キー コンテナーを移動するテナントに、すべての重要なサービス プリンシパルが存在することを確認してください。

## <a name="prerequisites"></a>[前提条件]

* キー コンテナーが存在する現在のサブスクリプションに対する[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)レベル以上のアクセス権。 ロールは [Azure portal](../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../role-based-access-control/role-assignments-cli.md)、または [PowerShell](../../role-based-access-control/role-assignments-powershell.md) を使用して割り当てることができます。
* キー コンテナーを移動するサブスクリプションに対する[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)レベル以上のアクセス権。ロールは [Azure portal](../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../role-based-access-control/role-assignments-cli.md)、または [PowerShell](../../role-based-access-control/role-assignments-powershell.md) を使用して割り当てることができます。
* 新しいサブスクリプションのリソース グループ。 これは [Azure portal](../../azure-resource-manager/management/manage-resource-groups-portal.md)、[PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)、または [Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md) を使用して作成できます。

既存のロールは、[Azure portal](../../role-based-access-control/role-assignments-list-portal.md)、[PowerShell](../../role-based-access-control/role-assignments-list-powershell.md)、[Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)、または [REST API](../../role-based-access-control/role-assignments-list-rest.md) を使用して確認できます。


## <a name="moving-a-key-vault-to-a-new-subscription"></a>キー コンテナーを新しいサブスクリプションに移動する

1. Azure Portal ( https://portal.azure.com ) にサインインします。
2. お使いの[キー コンテナー](overview.md)に移動します
3. [概要] タブをクリックする
4. [移動] ボタン を選択する
5. ドロップダウン リストのオプションから [別のサブスクリプションに移動する] を選択する
6. キー コンテナーを移動するリソース グループを選択する
7. リソースの移動に関する警告を確認する
8. [OK] を選択する

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>サブスクリプションが新しいテナントにある場合の追加の手順

お使いのキー コンテナーを新しいテナントのサブスクリプションに移動した場合、そのテナントの ID を手動で更新して、古いアクセス ポリシーとロールの割り当てを削除する必要があります。 ここでは、PowerShell と Azure CLI でこれらの手順を実行するためのチュートリアルを紹介します。 PowerShell を使用している場合は、お使いの現在選択されているスコープ以外のリソースを表示できるように、次に示す Clear-AzContext コマンドを実行する必要がある場合があります。 

### <a name="update-tenant-id-in-a-key-vault"></a>キー コンテナーのテナント ID を更新する

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
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
### <a name="update-access-policies-and-role-assignments"></a>アクセス ポリシーとロールの割り当てを更新する

> [!NOTE]
> Key Vault で [Azure RBAC](../../role-based-access-control/overview.md) アクセス許可モデルを使用している場合。 キー コンテナーのロールの割り当ても削除する必要があります。 ロールの割り当ては、[Azure portal](../../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../../role-based-access-control/role-assignments-cli.md)、または [PowerShell](../../role-based-access-control/role-assignments-powershell.md) を使用して削除できます。 

これで、コンテナーが正しいテナント ID に関連付けられ、古いアクセス ポリシー エントリまたはロールの割り当てが削除されたので、新しいアクセス ポリシー エントリまたはロールの割り当てを設定します。

ポリシーを割り当てる方法については、以下を参照してください。
- [ポータルを使用してアクセス ポリシーを割り当てる](assign-access-policy-portal.md)
- [Azure CLI を使用してアクセス ポリシーを割り当てる](assign-access-policy-cli.md)
- [PowerShell を使用してアクセス ポリシーを割り当てる](assign-access-policy-powershell.md)

ロールの割り当てを追加する方法については、以下を参照してください。
- [Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)
- [Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)
- [PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>マネージド ID を更新する

サブスクリプション全体を移転し、Azure リソースのマネージド ID を使用する場合は、新しい Azure Active Directory テナントにも更新する必要があります。 マネージド ID の詳細については、[マネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

マネージド ID を使用している場合、以前の ID は正しい Azure Active Directory テナントに存在しないため、ID を更新する必要もあります。 この問題を解決するには、次のドキュメントを参照してください。 

* [MSI の更新](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [サブスクリプションを新しいディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>次のステップ

- [キー、シークレット、証明書](about-keys-secrets-certificates.md)について学習します
- 概念的な情報については、Key Vault のログを解釈する方法を含め、[Key Vault のログ記録](logging.md)に関するページを参照してください
- [Key Vault 開発者ガイド](../general/developers-guide.md)
- [キー コンテナーのセキュリティ保護](secure-your-key-vault.md)
- [Azure Key Vault のファイアウォールと仮想ネットワークを構成する](network-security.md)