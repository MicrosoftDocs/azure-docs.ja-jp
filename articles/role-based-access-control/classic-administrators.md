---
title: Azure の従来のサブスクリプション管理者
description: Azure の共同管理者ロールとサービス管理者ロールを追加または変更する方法、およびアカウント管理者を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557541"
---
# <a name="azure-classic-subscription-administrators"></a>Azure の従来のサブスクリプション管理者

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを管理することをお勧めします。 ただし、クラシック デプロイ モデルをまだ使用している場合は、次の従来のサブスクリプション管理者ロールを使用する必要があります:サービス管理者および共同管理者。 詳しくは、[Azure Resource Manager とクラシック デプロイ](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

この記事では、共同管理者ロールとサービス管理者ロールを追加または変更する方法、およびアカウント管理者を表示する方法について説明します。

## <a name="add-a-co-administrator"></a>共同管理者を追加する

> [!TIP]
> 共同管理者を追加する必要があるのは、[Azure サービス管理 PowerShell モジュール](/powershell/module/servicemanagement/azure.service)を使用して Azure クラシック デプロイを管理する必要がある場合だけです。 クラシック リソースの管理に Azure portal だけを使用する場合は、ユーザーに従来の管理者を追加する必要はありません。

1. [Azure portal](https://portal.azure.com) にサービス管理者または共同管理者としてサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

    共同管理者は、サブスクリプション スコープでのみ割り当てることができます。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[従来の管理者]** タブをクリックします。

    ![[従来の管理者] が開かれているスクリーンショット](./media/classic-administrators/classic-administrators.png)

1. **[追加]**  >  **[共同管理者の追加]** をクリックして、[共同管理者の追加] ウィンドウを開きます。

    [共同管理者の追加] オプションが無効になっている場合は、アクセス許可がありません。

1. 追加するユーザーを選択して、 **[追加]** をクリックします。

    ![共同管理者追加のスクリーンショット](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>ゲスト ユーザーを共同管理者として追加する

ゲスト ユーザーを共同管理者として追加するには、前の [[共同管理者を追加する]](#add-a-co-administrator) セクションと同じ手順に従います。 ゲスト ユーザーは次の条件を満たす必要があります。

- ゲスト ユーザーは、ディレクトリ内に存在している必要があります。 これは、ユーザーがディレクトリに招待され、招待を受け入れたことを意味します。

ゲスト ユーザーを自分のディレクトリに追加する方法の詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」を参照してください。

### <a name="differences-for-guest-users"></a>ゲスト ユーザーの違い

共同管理者ロールが割り当てられたゲスト ユーザーは、同じく共同管理者ロールが割り当てられたメンバー ユーザーとは異なります。 以下のシナリオについて考えてみます。

- Azure AD アカウント (職場または学校アカウント) を持つユーザー A は、Azure サブスクリプションのサービス管理者です。
- ユーザー B は Microsoft アカウントを持っています。
- ユーザー A がユーザー B に共同管理者ロールを割り当てます。
- ユーザー B はほぼすべての操作を実行できますが、Azure AD にアプリケーションを登録したり、Azure AD ディレクトリでユーザーを検索したりすることはできません。

想定とは異なり、ユーザー B はすべてを管理できるわけではありません。 この違いの理由は、Microsoft アカウントはメンバー ユーザーとしてではなく、ゲスト ユーザーとしてサブスクリプションに追加されるためです。 Azure AD でゲスト ユーザーに割り当てられる既定のアクセス許可は、メンバー ユーザーとは異なります。 たとえば、メンバー ユーザーは Azure AD の他のユーザーを読み取ることができますが、ゲスト ユーザーには他のユーザーの読み取りは許可されていません。 メンバー ユーザーは新しいサービス プリンシパルを Azure AD に登録できますが、ゲスト ユーザーにはサービス プリンシパルの登録は許可されていません。

ゲスト ユーザーがこれらのタスクを実行できるようにする必要がある場合、考えられるソリューションは、ゲスト ユーザーに必要な特定の Azure AD ロールを割り当てることです。 たとえば前のシナリオで他のユーザーの読み取りを許可するには[ディレクトリ リーダー](../active-directory/roles/permissions-reference.md#directory-readers)のロールを割り当て、サービス プリンシパルを作成できるようにするには[アプリケーション開発者](../active-directory/roles/permissions-reference.md#application-developer)のロールを割り当てます。 メンバー ユーザーとゲスト ユーザーおよびそれぞれのアクセス許可の詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」をご覧ください。 ゲスト ユーザーにアクセス権を付与する方法の詳細については、「[Azure portal を使用して Azure ロールを外部のゲスト ユーザーに割り当てる](role-assignments-external-users.md)」を参照してください。

[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)は [Azure AD ロール](../active-directory/roles/permissions-reference.md)とは異なることに注意してください。 組み込みロールは Azure AD に対するアクセス許可を一切付与しません。 詳細については、「[各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)」をご覧ください。

メンバー ユーザーとゲスト ユーザーの比較情報については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」を参照してください。

## <a name="remove-a-co-administrator"></a>共同管理者を削除する

1. [Azure portal](https://portal.azure.com) にサービス管理者または共同管理者としてサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[従来の管理者]** タブをクリックします。

1. 削除する共同管理者の横にチェック マークを追加します。

1. **[削除]** をクリックします。

1. 表示されるメッセージ ボックスで、 **[はい]** をクリックします。

    ![共同管理者削除のスクリーンショット](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>サービス管理者を変更する

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。 既定の設定では、Azure サブスクリプションにサインアップした時点では、サービス管理者とアカウント管理者は同じです。 アカウント管理者ロールのユーザーには、Azure portal へのアクセス権が与えられません。 サービス管理者ロールのユーザーには、Azure portal へのフル アクセス権が与えられます。 アカウント管理者とサービス管理者が同じユーザーの場合、サービス管理者を別のユーザーに変更すると、アカウント管理者は Azure portal にアクセスできなくなります。 ただし、アカウント管理者はいつでもアカウント センターを使用して、サービス管理者を自身に戻すことができます。

Azure portal でサービス管理者を変更するには、次の手順に従います。

1. [サービス管理者を変更するための制限事項](#limitations-for-changing-the-service-administrator)を確認して、使用するシナリオがサポートされていることを確認してください。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。

1. **Cost Management + Billing** を開き、サブスクリプションを選択します。

1. 次に、左側のナビゲーションで、 **[プロパティ]** をクリックします。

1. **[サービス管理者]** をクリックします。

    ![Azure portal でサブスクリプションの [プロパティ] を示すスクリーンショット](./media/classic-administrators/service-admin.png)

1. **[サービス管理者の編集]** ページで、新しいサービス管理者のメール アドレスを入力します。

    ![[サービス管理者の編集] ページを示すスクリーンショット](./media/classic-administrators/service-admin-edit.png)

1. **[OK]** をクリックして変更を保存します。

### <a name="limitations-for-changing-the-service-administrator"></a>サービス管理者の変更に関する制限事項

Azure サブスクリプションごとに 1 人のサービス管理者のみを設定できます。 サービス管理者の変更の動作は、アカウント管理者が Microsoft アカウントか、Azure AD アカウント (職場または学校アカウント) かによって異なります。

| アカウント管理者のアカウント | サービス管理者を別の Microsoft アカウントに変更できるか? | サービス管理者を同じディレクトリの Azure AD アカウントに変更できるか? | サービス管理者を異なるディレクトリの Azure AD アカウントに変更できるか? |
| --- | --- | --- | --- |
| Microsoft アカウント | はい | いいえ | いいえ |
| Azure AD アカウント | はい | はい | いいえ |

アカウント管理者が Azure AD アカウントの場合は、サービス管理者を同じディレクトリ内の Azure AD アカウントに変更することはできますが、別のディレクトリには変更できません。 たとえば、abby@contoso.com は、サービス管理者を bob@contoso.com に変更できますが、john@notcontoso.com に変更することは、john@notcontoso.com が contoso.com ディレクトリに存在しない場合はできません。

Microsoft アカウントと Azure AD アカウントの詳細については、「[Azure Active Directory とは](../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

## <a name="view-the-account-administrator"></a>アカウント管理者を表示する

アカウント管理者とは、Azure サブスクリプションに最初にサインアップしたユーザーで、サブスクリプションの請求先の所有者としての責任を負います。 サブスクリプションのアカウント管理者を変更する方法の詳細については、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../cost-management-billing/manage/billing-subscription-transfer.md)」を参照してください。

アカウント管理者を表示するには、以下の手順のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **Cost Management + Billing** を開き、サブスクリプションを選択します。

1. 次に、左側のナビゲーションで、 **[プロパティ]** をクリックします。

    サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

    ![アカウント管理者を示すスクリーンショット](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>次のステップ

* [各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
* [Azure サブスクリプション管理者を追加または変更する](../cost-management-billing/manage/add-change-subscription-administrator.md)