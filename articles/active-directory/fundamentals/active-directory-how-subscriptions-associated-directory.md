---
title: 既存の Azure サブスクリプションをテナントに追加する - Azure Active Directory | Microsoft Docs
description: 既存の Azure サブスクリプションを Azure Active Directory テナントに追加する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b141de4c22ba1ba1325982d8e027fa46e0909fbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009856"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する

Azure サブスクリプションには、Azure Active Directory (Azure AD) との間に信頼関係があります。つまり、サブスクリプションは Azure AD を信頼することでユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じ Azure AD ディレクトリを信頼することはできますが、1 つのサブスクリプションが信頼できるディレクトリは 1 つに限られます。

サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられた他のすべてのリソースへのアクセスもできなくなります。 ただし、Azure AD ディレクトリは Azure に残るため、別の Azure サブスクリプションを使用してディレクトリの関連付けおよび管理を行うことができます。

すべてのユーザーに、認証のための *ホーム* ディレクトリが 1 つあります。 ただし、ユーザーは、他のディレクトリのゲストになることもできます。 Azure AD では、各ユーザーのホーム ディレクトリとゲスト ディレクトリの両方を確認できます。

> [!Important]
> サブスクリプションを別のディレクトリに関連付けると、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/role-assignments-portal.md) を使用してロールが割り当てられているユーザーはアクセスできなくなります。 従来のサブスクリプション管理者 (サービス管理者と共同管理者) もアクセスできなくなります。
> 
> また、Azure Kubernetes Service (AKS) クラスターを別のサブスクリプションに移したり、クラスターを所有するサブスクリプションを新しいテナントに移したりすると、ロールの割り当てやサービス プリンシパルの権限が失われるため、クラスターの機能が失われることになります。 AKS の詳細については、「[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

サブスクリプションの関連付けまたは追加を行う前に、次のタスクを実行する必要があります。

1. 次の変更と影響の一覧を確認してください。

    - RBAC を使用してロールが割り当てられているユーザーはアクセスできなくなる
    - サービス管理者と共同管理者はアクセスできなくなる
    - キー コンテナーがある場合はアクセスできなくなり、関連付けを行った後にそれらの修正が必要になる
    - 登録されている Azure Stack がある場合は、関連付けを行った後に再登録が必要になる

1. 次のようなアカウントを使用してサインインします。
    - サブスクリプションの[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールが割り当てられているアカウント。 所有者ロールの割り当て方法について詳しくは、「[RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」をご覧ください。
    - サブスクリプションに関連付けられた現在のディレクトリと、今後サブスクリプションを関連付ける新しいディレクトリの両方に存在する。 別のディレクトリへのアクセスの取得の詳細については、[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](../b2b/add-users-administrator.md)に関する記事を参照してください。

1. Azure クラウド サービス プロバイダー (CSP) サブスクリプション (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 内部サブスクリプション (MS-AZR-0015P)、Microsoft Imagine サブスクリプション (MS-AZR-0144P) を使用していないことを確認します。
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>既存のサブスクリプションを Azure AD ディレクトリに関連付けるには

1. Azure portal にサインインし、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で使用するサブスクリプションを選択します。

2. **[ディレクトリの変更]** を選択します。

    ![[ディレクトリの変更] オプションが強調表示された [サブスクリプション] ページ](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 表示されたすべての警告を確認してから、**[変更]** を選択します。

    ![ディレクトリの変更先が表示された [ディレクトリの変更] ページ](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    サブスクリプションのディレクトリが変更され、成功メッセージが表示されます。

    ![ディレクトリ変更の成功メッセージ](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. **ディレクトリ スイッチャー**を使用して、新しいディレクトリに移動します。 すべてが正しく表示されるまで、最大で 10 分かかる場合があります。

    ![サンプル情報が含まれたディレクトリ スイッチャーのページ](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

サブスクリプションのディレクトリの変更はサービス レベルの操作であるため、サブスクリプションの課金所有権には影響しません。 アカウント管理者は、引き続き[アカウント センター](https://account.azure.com/subscriptions)からサービス管理者を変更することができます。 元のディレクトリを削除するには、新しいアカウント管理者にサブスクリプションの課金所有権を譲渡する必要があります。課金所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../billing/billing-subscription-transfer.md)」を参照してください。

## <a name="post-association-steps"></a>関連付けの後の手順
サブスクリプションを別のディレクトリに関連付けた後、操作を再開するために実行しなければならない追加手順がある場合があります。

1. キー コンテナーがある場合は、キー コンテナーのテナント ID を変更する必要があります。 詳細については、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../../key-vault/key-vault-subscription-move-fix.md)」を参照してください。

2. このサブスクリプションを使用して Azure Stack を登録した場合は、再登録する必要があります。 詳細については、「[Azure サブスクリプションを使用した Azure Stack の登録](/azure-stack/operator/azure-stack-registration)」を参照してください。



## <a name="next-steps"></a>次の手順

- 新しい Azure AD のテナントを作成するには、[Azure Active Directory へのアクセスによる新しいテナントの作成](active-directory-access-create-new-tenant.md)に関する記事を参照してください

- Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Azure AD でのロールの割り当て方法の詳細については、[Azure Active Directory を使用したユーザーへのディレクトリ ロールの割り当て方法](active-directory-users-assign-role-azure-portal.md)に関する記事を参照してください
