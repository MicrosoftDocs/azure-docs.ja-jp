---
title: Azure の従来のサブスクリプション管理者 | Microsoft Docs
description: Azure の共同管理者ロールとサービス管理者ロールを追加または変更する方法、およびアカウント管理者を表示する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a19153677e691d2681c131df67100a43ef9532a0
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889326"
---
# <a name="azure-classic-subscription-administrators"></a>Azure の従来のサブスクリプション管理者

ロールベースのアクセス制御 (RBAC) を使用して、Azure リソースへのアクセスを管理することをお勧めします。 ただし、クラシック デプロイ モデルをまだ使用している場合は、次の従来のサブスクリプション管理者ロールを使用する必要があります:サービス管理者および共同管理者。 詳しくは、[Azure Resource Manager とクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事をご覧ください。

この記事では、共同管理者ロールとサービス管理者ロールを追加または変更する方法、およびアカウント管理者を表示する方法について説明します。

## <a name="add-a-co-administrator"></a>共同管理者を追加する

> [!TIP]
> 共同管理者を追加する必要があるのは、[Azure サービス管理 PowerShell モジュール](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure)を使用して Azure クラシック デプロイを管理する必要がある場合だけです。 クラシック リソースの管理に Azure portal だけを使用する場合は、ユーザーに従来の管理者を追加する必要はありません。

1. [Azure portal](https://portal.azure.com) にサービス管理者としてサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

    共同管理者は、サブスクリプション スコープでのみ割り当てることができます。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[従来の管理者]** タブをクリックします。

    ![[従来の管理者] が開かれているスクリーンショット](./media/classic-administrators/classic-administrators.png)

1. **[追加]** > **[共同管理者の追加]** をクリックして、[共同管理者の追加] ウィンドウを開きます。

    [共同管理者の追加] オプションが無効になっている場合は、アクセス許可がありません。

1. 追加するユーザーを選択して、**[追加]** をクリックします。

    ![共同管理者追加のスクリーンショット](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>ゲスト ユーザーを共同管理者として追加する

共同管理者ロールが割り当てられた[ゲスト ユーザー](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md)は、同じく共同管理者ロールが割り当てられたメンバー ユーザーとは異なる点にいくつか気付くことがあります。 次のシナリオで考えてみましょう。

- Azure AD の職場または学校アカウントを持つユーザー A は、Azure サブスクリプションのサービス管理者です。
- ユーザー B は Microsoft アカウントを持っています。
- ユーザー A がユーザー B に共同管理者ロールを割り当てます。
- ユーザー B はほぼすべての操作を実行できますが、Azure AD にアプリケーションを登録したり、Azure AD ディレクトリでユーザーを検索したりすることはできません。

想定とは異なり、ユーザー B はすべてを管理できるわけではありません。 この違いの理由は、Microsoft アカウントはメンバー ユーザーとしてではなく、ゲスト ユーザーとしてサブスクリプションに追加されるためです。 Azure AD でゲスト ユーザーに割り当てられる既定のアクセス許可は、メンバー ユーザーとは異なります。 たとえば、メンバー ユーザーは Azure AD の他のユーザーを読み取ることができますが、ゲスト ユーザーには他のユーザーの読み取りは許可されていません。 メンバー ユーザーは新しいサービス プリンシパルを Azure AD に登録できますが、ゲスト ユーザーにはサービス プリンシパルの登録は許可されていません。 ゲスト ユーザーがこれらのタスクを実行できるようにする必要がある場合、考えられるソリューションは、ゲスト ユーザーに必要な特定の Azure AD 管理者ロールを割り当てることです。 たとえば前のシナリオで他のユーザーの読み取りを許可するには[ディレクトリ リーダー](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)のロールを割り当て、サービス プリンシパルを作成できるようにするには[アプリケーション開発者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer)のロールを割り当てます。 メンバー ユーザーとゲスト ユーザーおよびそれぞれのアクセス許可の詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../active-directory/fundamentals/users-default-permissions.md)」をご覧ください。

[Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md)は、[Azure AD 管理者ロール](../active-directory/users-groups-roles/directory-assign-admin-roles.md)とは異なることに注意してください。 組み込みロールは Azure AD に対するアクセス許可を一切付与しません。 詳細については、「[各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)」をご覧ください。

## <a name="remove-a-co-administrator"></a>共同管理者を削除する

1. [Azure portal](https://portal.azure.com) にサービス管理者としてサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[従来の管理者]** タブをクリックします。

1. 削除する共同管理者の横にチェック マークを追加します。

1. **[削除]** をクリックします。

1. 表示されるメッセージ ボックスで、**[はい]** をクリックします。

    ![共同管理者削除のスクリーンショット](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>サービス管理者を変更する

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。 既定の設定では、Azure サブスクリプションにサインアップした時点では、サービス管理者とアカウント管理者は同じです。 アカウント管理者ロールのユーザーには、Azure portal へのアクセス権が与えられません。 サービス管理者ロールのユーザーには、Azure portal へのフル アクセス権が与えられます。 アカウント管理者とサービス管理者が同じユーザーの場合、サービス管理者を別のユーザーに変更すると、アカウント管理者は Azure portal にアクセスできなくなります。 ただし、アカウント管理者はいつでもアカウント センターを使用して、サービス管理者を自身に戻すことができます。

サービス管理者を変更するには 2 つの方法があります。 **Azure portal** または**アカウント センター**で変更することができます。

### <a name="azure-portal"></a>Azure ポータル

1. サービス管理者を変更するための制限事項を確認して、使用するシナリオがサポートされていることを確認してください。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

1. **[プロパティ]** をクリックします。

    ![アカウント管理者を示すスクリーンショット](./media/classic-administrators/account-admin.png)

1. 上部の **[サービス管理者]** をクリックして [サービス管理者] ウィンドウを開きます。

    [サービス管理者] ボタンが無効になっている場合は、アクセス許可がありません。 サービス管理者を変更できるのは、アカウント管理者であるユーザーだけです。

1. 新しいサービス管理者を選択して、**[保存]** をクリックします。

### <a name="account-center"></a>アカウント センター

1. サービス管理者を変更するための制限事項を確認して、使用するシナリオがサポートされていることを確認してください。

1. アカウント管理者として [Azure センター](https://account.windowsazure.com/subscriptions)にサインインします。

1. サブスクリプションをクリックします。

1. 右側にある **[サブスクリプション詳細の編集]** をクリックします。

    ![アカウント センターの [サブスクリプションの編集] ボタンを示すスクリーン ショット](./media/classic-administrators/editsub.png)

1. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。

    ![サービス管理者の電子メールを変更する、ボックスを示すスクリーン ショット](./media/classic-administrators/change-service-admin.png)

1. チェックマークをクリックして、変更を保存します。

### <a name="limitations-for-changing-the-service-administrator"></a>サービス管理者の変更に関する制限事項

すべてのサブスクリプションは Azure AD ディレクトリと関連付けられています。 サブスクリプションが関連付けられているディレクトリを検索するには、Azure portal で **[サブスクリプション]** を開き、ディレクトリを表示するサブスクリプションを選択します。

職場または学校アカウントを使用してサインインした場合、組織内の他のアカウントをサービス管理者として追加することができます。 たとえば、abby@contoso.com はサービス管理者として bob@contoso.com を追加できますが、john@notcontoso.com が contoso.com ディレクトリ内にない限り john@notcontoso.com は追加できません。 職場または学校アカウントでサインインしているユーザーは、引き続き Microsoft アカウント ユーザーをサービス管理者として追加できます。

  | サインイン方法 | サービス管理者として Microsoft アカウント ユーザーを追加できる | 同じ組織内の職場または学校アカウントをサービス管理者として追加できる | 別の組織内の職場または学校アカウントをサービス管理者として追加できる |
  | --- | --- | --- | --- |
  |  Microsoft アカウント |はい |いいえ  |いいえ  |
  |  会社または学校のアカウント |はい |はい |いいえ  |

## <a name="view-the-account-administrator"></a>アカウント管理者を表示する

アカウント管理者とは、Azure サブスクリプションに最初にサインアップしたユーザーで、サブスクリプションの請求先の所有者としての責任を負います。 サブスクリプションのアカウント管理者を変更する方法の詳細については、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」を参照してください。

アカウント管理者を表示するには、以下の手順のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) を開き、サブスクリプションを選択します。

1. **[プロパティ]** をクリックします。

    サブスクリプションのアカウント管理者が、**[アカウント管理者]** ボックスに表示されます。

    ![アカウント管理者を示すスクリーンショット](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>次の手順

* [Azure での各種ロールについて](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する](../role-based-access-control/role-assignments-portal.md)
* [Azure サブスクリプション管理者を追加または変更する](../billing/billing-add-change-azure-subscription-administrator.md)
