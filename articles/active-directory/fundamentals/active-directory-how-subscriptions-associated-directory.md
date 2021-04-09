---
title: ご利用のテナントに既存の Azure サブスクリプションを追加する - Azure AD
description: 既存の Azure サブスクリプションを Azure Active Directory (Azure AD) テナントに追加する方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594633"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する

Azure サブスクリプションには、Azure Active Directory (Azure AD) との信頼関係があります。 サブスクリプションでは、Azure AD を信頼して、ユーザー、サービス、デバイスの認証が行われます。

複数のサブスクリプションが同じ Azure AD ディレクトリを信頼できます。 各サブスクリプションが信頼できるディレクトリは 1 つに限られます。

1 つまたは複数の Azure サブスクリプションは、Azure サービスに対してセキュリティ プリンシパルおよびデバイスを認証し、許可を与える目的で、Azure Active Directory (Azure AD) インスタンスと信頼関係を構築できます。  サブスクリプションの有効期限が切れると、Azure AD サービスの信頼されたインスタンスは残りますが、セキュリティ プリンシパルでは、Azure リソースへのアクセスが失われます。

ユーザーが Microsoft のクラウド サービスに新規登録すると、新しい Azure AD テナントが作成され、そのユーザーは全体管理者ロールに属します。 ただし、サブスクリプションの所有者が自分のサブスクリプションを既存のテナントに参加させるとき、その所有者は全体管理者ロールに割り当てられません。

すべてのユーザーに、認証のための *ホーム* ディレクトリが 1 つあります。 ユーザーは、他のディレクトリのゲストになることもできます。 Azure AD では、各ユーザーのホーム ディレクトリとゲスト ディレクトリの両方を確認できます。

> [!Important]
> サブスクリプションを別のディレクトリに関連付けると、[Azure ロールベースのアクセス制御](../../role-based-access-control/role-assignments-portal.md)を使用してロールが割り当てられているユーザーはアクセスできなくなります。 サービス管理者や共同管理者などの従来のサブスクリプション管理者もアクセスできなくなります。
>
> Azure Kubernetes Service (AKS) クラスターを別のサブスクリプションに移したり、クラスターを所有するサブスクリプションを新しいテナントに移したりすると、ロールの割り当てやサービス プリンシパルの権限が失われるため、クラスターの機能が失われることになります。 AKS の詳細については、「[Azure Kubernetes Service (AKS)](../../aks/index.yml)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

サブスクリプションの関連付けまたは追加を行う前に、次のタスクを実行してください。

- サブスクリプションを関連付けたり追加したりした後に行われる変更の一覧と、その影響を確認してください。

  - Azure RBAC を使用してロールが割り当てられているユーザーはアクセスできなくなります。
  - サービス管理者と共同管理者はアクセスできなくなります。
  - キー コンテナーがある場合はアクセスできなくなり、関連付けを行った後にそれらを修正する必要があります。
  - 仮想マシンやロジック アプリなどのリソースのマネージド ID を持っている場合は、関連付け後にそれらを再度有効にするか再作成する必要があります。
  - 登録されている Azure Stack がある場合は、関連付けを行った後に再登録が必要になります。
  - 詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

- 次のようなアカウントを使用してサインインします。

  - サブスクリプションの[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールが割り当てられているアカウント。 所有者ロールの割り当て方法について詳しくは、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
  - 現在のディレクトリと新しいディレクトリの両方に存在する。 現在のディレクトリはサブスクリプションに関連付けられています。 新しいディレクトリをそのサブスクリプションに関連付けます。 別のディレクトリへのアクセスを取得する方法について詳しくは、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../external-identities/add-users-administrator.md)」を参照してください。

- Azure クラウド サービス プロバイダー (CSP) サブスクリプション (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 内部サブスクリプション (MS-AZR-0015P)、または Microsoft Azure for Students Starter サブスクリプション (MS-AZR-0144P) を使用していないことを確認します。

## <a name="associate-a-subscription-to-a-directory"></a>サブスクリプションをディレクトリに関連付ける<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

既存のサブスクリプションを Azure AD ディレクトリに関連付けるには、次の手順に従います。

1. Azure portal にサインインし、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で使用するサブスクリプションを選択します。

1. **[ディレクトリの変更]** を選択します。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="[サブスクリプション] ページを示すスクリーンショット。[ディレクトリの変更] オプションが強調表示されています。":::

1. 表示されたすべての警告を確認してから、 **[変更]** を選択します。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="サンプル ディレクトリと [変更] ボタンが強調表示されている [ディレクトリの変更] ページを示すスクリーンショット。":::

   サブスクリプションのディレクトリが変更された後、成功メッセージが表示されます。

1. [サブスクリプション] ページで **[ディレクトリの切り替え]** を選択し、新しいディレクトリに移動します。

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="サンプル情報を含む [ディレクトリの切り替え] ページを示すスクリーンショット。":::

   すべてが適切に表示されるまでに数時間かかる場合があります。 時間がかかりすぎていると思われる場合は、 **[グローバルなサブスクリプション フィルター]** を確認します。 移動したサブスクリプションが非表示になっていないことを確認します。 一度 Azure portal からサインアウトしてから再びサインインしないと、新しいディレクトリが表示されない場合があります。

サブスクリプションのディレクトリの変更はサービス レベルの操作であるため、サブスクリプションの課金所有権には影響しません。 元のディレクトリを削除するには、新しいアカウント管理者にサブスクリプションの課金所有権を譲渡する必要があります。課金所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../cost-management-billing/manage/billing-subscription-transfer.md)」を参照してください。

## <a name="post-association-steps"></a>関連付け後の手順

サブスクリプションを別のディレクトリに関連付けた後、次のタスクを実行しないと操作が再開されない場合があります。

- キー コンテナーがある場合は、キー コンテナーのテナント ID を変更する必要があります。 詳細については、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../../key-vault/general/move-subscription.md)」を参照してください。

- リソースにシステム割り当てマネージド ID を使用していた場合は、それらの ID を再度有効にする必要があります。 ユーザー割り当てマネージド ID を使用していた場合は、それらの ID を再作成する必要があります。 マネージド ID を再度有効にするか再作成した後、それらの ID に割り当てられているアクセス許可を再確立する必要があります。 詳細については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。

- このサブスクリプションを使用して Azure Stack を登録した場合は、再登録する必要があります。 詳細については、[Azure を使用した Azure Stack Hub の登録](/azure-stack/operator/azure-stack-registration)に関するページを参照してください。

- 詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 新しい Azure AD テナントを作成するには、「[クイックスタート:Azure Active Directory で新しいテナントを作成する](active-directory-access-create-new-tenant.md)」を参照してください。

- Microsoft Azure によってリソース アクセスを制御する方法の詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。

- Azure AD でロールを割り当てる方法について詳しくは、「[Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる](active-directory-users-assign-role-azure-portal.md)」を参照してください。
