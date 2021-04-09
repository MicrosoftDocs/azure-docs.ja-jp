---
title: Azure Active Directory のカスタム ロールに対するアプリへの同意のアクセス許可 | Microsoft Docs
description: Azure portal、PowerShell、Graph API におけるカスタム Azure AD ロールに対するアプリへの同意のアクセス許可について見ていきます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740110"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory のカスタム ロールに対するアプリへの同意のアクセス許可

この記事では、Azure Active Directory (Azure AD) のカスタム ロールの定義に対して現在利用可能なアプリへの同意のアクセス許可について説明します。 この記事には、アプリへの同意とアクセス許可に関連したいくつかの一般的なシナリオに必要なアクセス許可が記載されています。

## <a name="required-license-plan"></a>必要とされるライセンス プラン

この機能を使用するためには、Azure AD 組織に Azure AD Premium P1 ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

## <a name="app-consent-permissions"></a>アプリへの同意のアクセス許可

この記事に記載されている一連のアクセス許可は、アプリへの同意ポリシーの管理に加え、アプリに同意を付与するためのアクセス許可の管理に使用されます。

> [!NOTE]
> 現在、この記事に記載されているアクセス許可を、Azure AD 管理ポータルからカスタム ディレクトリ ロールの定義に追加することはできません。 この記事に記載されているアクセス許可を有する[カスタム ディレクトリ ロールは、Azure AD PowerShell を使用して作成](custom-create.md#create-a-role-using-powershell)する必要があります。

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>自己の代理となるアプリに、委任されたアクセス許可を付与する (ユーザーの同意)

アプリへの同意ポリシーの適用を条件に、自己の代理となることへの同意 (ユーザーの同意) をユーザーがアプリケーションに与えられるようにします。

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

`{id}` は、[アプリへの同意ポリシー](../manage-apps/manage-app-consent-policies.md)の ID に置き換えてください。このアクセス許可を有効にするために満たすべき条件が、このポリシーによって設定されます。

たとえば、`microsoft-user-default-low` という ID を持つ、組み込みのアプリへの同意ポリシーが適用されることを条件に、アプリが自己の代理となることへの同意をユーザーが与えられるようにするには、`...managePermissionGrantsForSelf.microsoft-user-default-low` というアクセス許可を使用することになります。

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>すべてのユーザーの代理となるアプリにアクセス許可を付与する (管理者の同意)

委任されたアクセス許可とアプリケーションのアクセス許可 (アプリ ロール) の両方について、テナント全体の管理者の同意をアプリに委任します。

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

`{id}` は、[アプリへの同意ポリシー](../manage-apps/manage-app-consent-policies.md)の ID に置き換えてください。このアクセス許可を有効にするために満たすべき条件が、このポリシーによって設定されます。

たとえば、`low-risk-any-app` という ID を持つ、カスタムの[アプリへの同意ポリシー](../manage-apps/manage-app-consent-policies.md)が適用されることを条件に、ロールを割り当てられたユーザーが、テナント全体の管理者の同意をアプリに付与できるようにするには、`microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` というアクセス許可を使用することになります。

### <a name="managing-app-consent-policies"></a>アプリへの同意ポリシーを管理する

[アプリへの同意ポリシー](../manage-apps/manage-app-consent-policies.md)の作成、更新、削除を委任します。

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>アクセス許可の全一覧

権限 | 説明
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | 自己の代理となることへの同意 (ユーザーの同意) をアプリに与える権限が、アプリへの同意ポリシー `{id}` の適用を条件に付与されます。
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | すべてのユーザーの代理となることへの同意 (テナント全体の管理者の同意) をアプリに与える権限が、アプリへの同意ポリシー `{id}` の適用を条件に付与されます。
microsoft.directory/permissionGrantPolicies/standard/read | アプリへの同意ポリシーの読み取り権限が付与されます。
microsoft.directory/permissionGrantPolicies/basic/update | 既存のアプリへの同意ポリシーにある基本的なプロパティを更新する権限が付与されます。
microsoft.directory/permissionGrantPolicies/create | アプリへの同意ポリシーの作成権限が付与されます。
microsoft.directory/permissionGrantPolicies/delete | アプリへの同意ポリシーの削除権限が付与されます。

## <a name="next-steps"></a>次の手順

- [Azure portal、Azure AD PowerShell、および Graph API を使用してカスタム ロールを作成する](custom-create.md)
- [カスタム ロールの割り当てを表示する](../roles/view-assignments.md)
