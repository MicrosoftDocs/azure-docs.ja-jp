---
title: サブスクリプション移行後のキー コンテナー テナント ID の変更 - Azure Key Vault | Microsoft Docs
description: サブスクリプションを別のテナントに移行した後にキー コンテナーのテナント ID を切り替える方法について説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: a83bff5a494ce338f43b6e967df5fe67cacfab01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112191"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>サブスクリプション移行後のキー コンテナー テナント ID の変更

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q:テナント A からテナント B にサブスクリプションを移行しました。既存のキー コンテナーのテナント ID を変更し、テナント B 内のプリンシパルに正しい ACL を設定する方法を教えてください。

サブスクリプション内で新しいキー コンテナーを作成すると、作成したキー コンテナーは、そのサブスクリプションの既定の Azure Active Directory テナント ID に自動的に関連付けられます。 また、すべてのアクセス ポリシー エントリがこのテナント ID に関連付けられます。 テナント A からテナント B に Azure サブスクリプションを移行すると、テナント B のプリンシパル (ユーザーとアプリケーション) は既存のキー コンテナーにアクセスできなくなります。この問題を解決するには、次のことを行う必要があります。

* このサブスクリプションにあるすべての既存のキー コンテナーに関連付けられたテナント ID を、テナント B に変更する。
* すべての既存のアクセス ポリシー エントリを削除する。
* テナント B に関連付けられた新しいアクセス ポリシー エントリを追加する。

たとえば、テナント A からテナント B に移行したサブスクリプションに "myvault" というキー コンテナーがあるとすると、このキー コンテナーのテナント ID を変更して古いアクセス ポリシーを削除する方法は、次のようになります。

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

このコンテナーは移行前にテナント A にあったため、**$vault.Properties.TenantId** の元の値はテナント A です。一方、**(Get-AzContext).Tenant.TenantId** の値はテナント B になります。

これで、コンテナーが正しいテナント ID に関連付けられ、古いアクセス ポリシー エントリが削除されたので、[Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) で新しいアクセス ポリシー エントリを設定します。

## <a name="next-steps"></a>次の手順

Azure Key Vault に関する質問がある場合は、[Azure Key Vault フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。
