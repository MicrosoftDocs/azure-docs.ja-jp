---
title: "Azure 管理者サブスクリプション ロールを追加または変更する | Microsoft Docs"
description: "Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/12/2017
ms.author: genli
ms.openlocfilehash: 0252a084061c89f3d0198fc9a08e8ee65581524b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する

Azure サブスクリプションまたはサブスクリプションで使われる Azure サービスを管理する Azure 管理者を変更できます。 Azure 課金情報の確認およびサブスクリプションの管理を行うには、アカウント センターにアカウント管理者としてサインインする必要があります。 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Azure ポータルでサブスクリプションの所有者の RBAC 管理者を追加します。 

Azure ポータルでユーザーをサブスクリプションの管理者として追加するには、そのユーザーに [RBAC](../active-directory/role-based-access-control-configure.md) 所有者ロールを付与することをお勧めします。 所有者ロールでは、管理者が割り当て、他のサブスクリプションにアクセス権がないサブスクリプションのリソースを管理できます。 [Azure ポータル](https://portal.azure.com)で追加した所有者は、[Azure クラシック ポータル](https://manage.windowsazure.com)でリソースを管理できません。

1. Azure ポータルの[サブスクリプション ビュー](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にサインインします。
1. この管理者がアクセスするサブスクリプションを選択します。
1. メニューから **[アクセス制御 (IAM)]** を選択します。
1. **[追加]** > **[ロール]** > **[所有者]** の順に選択します。 所有者として追加するユーザーの電子メール アドレスを入力し、ユーザーを選択し、**[保存]** を選択します。

    ![選択された所有者ロールを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-role.png)

### <a name="add-or-change-co-administrator"></a>共同管理者を追加または変更する

共同管理者として追加できるのは所有者のみです。 共同作成者や閲覧者などのロールの他のユーザーは、共同管理者として追加できません。

1. 既に実行していない場合は、上記の手順に従って、所有者として他のユーザーを追加します。
2. 追加した所有者ユーザーを**右クリック**し、**[共同管理者として追加]** を選択します。 **[共同管理者として追加]** オプションが表示されない場合は、ページを更新するか、別のインターネット ブラウザーを試してください。 

     ![共同管理者追加のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >ユーザーが [Azure クラシック ポータル](https://manage.windowsazure.com/)で Azure サービスを管理する必要がある場合は、"所有者" アカウントを共同管理者として追加する必要があります。

    共同管理者のアクセス許可を削除するには、"共同管理者" を**右クリック**し、**[共同管理者を削除する]** を選択します。

    ![共同管理者削除のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのサービス管理者を変更する

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。 サインアップ時の既定設定では、サービス管理者はアカウント管理者でもあります。

1. 「[サービス管理者を変更するための制限事項](#limits)」を参照して、使用するシナリオがサポートされていることを確認してください。
1. アカウント管理者として [Azure センター](https://account.windowsazure.com/subscriptions)にサインインします。
1. サブスクリプションを選択します。
1. 右側にある **[サブスクリプション詳細の編集]** を選択します。

    ![アカウント センターの [サブスクリプションの編集] ボタンを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。

    ![サービス管理者の電子メールを変更する、ボックスを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>サービス管理者を変更するための制限事項

* すべてのサブスクリプションは Azure AD ディレクトリと関連付けられています。 サブスクリプションが関連付けられているディレクトリを検索するには、[Azure クラシック ポータル](https://manage.windowsazure.com/)にアクセスし、**[設定]** > **[サブスクリプション]** の順に選択します。 サブスクリプション ID を確認してディレクトリを検索します。
* 職場または学校アカウントを使用してサインインした場合、組織内の他のアカウントをサービス管理者として追加することができます。 たとえば、abby@contoso.com はサービス管理者として bob@contoso.com を追加できますが、john@notcontoso.com が contoso.com ディレクトリ内にない限り john@notcontoso.com は追加できません。 職場または学校アカウントでサインインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者として引き続き追加できます。

  | サインイン方法 | SA として Microsoft アカウント ユーザーを追加しますか。 | SA と同じ組織内に、職場または学校のアカウントを追加しますか。 | SA と異なる組織内に、職場または学校のアカウントを追加しますか。 |
  | --- | --- | --- | --- |
  |  Microsoft アカウント |はい |なし |いいえ |
  |  職場または学校のアカウント |あり |あり |いいえ |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのアカウント管理者を変更する

サブスクリプションのアカウント管理者を変更する方法の詳細については、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。

<a name="check-the-account-administrator-of-the-subscription"></a>

「**サブスクリプションのアカウント管理者を確認する**」 次の手順に従います。

1. Azure Portal の[サブスクリプション ビュー](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にサインインします。
1. 確認するサブスクリプションを選択し、**[設定]** を調べます。
1. **[プロパティ]**を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。  

## <a name="types-of-azure-admin-accounts"></a>Azure 管理者アカウントの種類

 Microsoft Azure の管理者ロールには、アカウント管理者、サービス管理者、共同管理者の 3 種類があります。 次の表では、これら 3 つの管理ロールの違いについて説明します。

| 管理ロール | 制限 | Description |
| --- | --- | --- |
| アカウント管理者 (AA) |1 Azure アカウントに 1 人 |Azure サブスクリプションをサインアップまたは購入し、 [アカウント センター](https://account.azure.com/Subscriptions) にアクセスして多様な管理タスクを実行する権限を持っている個人。 管理タスクには、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。 |
| サービス管理者 (SA) |1 Azure サブスクリプションに 1 人 |このロールには、 [Azure ポータル](https://portal.azure.com)でのサービスの管理が許可されます。 既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。 |
| [Azure クラシック ポータル](https://manage.windowsazure.com) |サブスクリプションあたり 200 人 |サービス管理者と同じアクセス権を持っているものの、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできないロール。 |

Azure Active Directory のロールベースのアクセス制御 (RBAC) では、ユーザーを複数のロールに追加できます。 詳細については、「 [Azure Active Directory のロールベースの Access Control](../active-directory/role-based-access-control-configure.md)」を参照してください。


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>リソースのアクセス制御と Active Directory の詳細

* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「[Azure でのリソース アクセスについて](../active-directory/active-directory-understanding-resource-access.md)」を参照してください。
* Azure Active Directory について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」および「[Azure Active Directory での管理者ロールの割り当て](../active-directory/active-directory-assign-admin-roles.md)」をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
