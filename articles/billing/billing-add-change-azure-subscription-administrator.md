---
title: "Azure 管理者サブスクリプション ロールを追加または変更する | Microsoft Docs"
description: "Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 6d438cde704323a07cc125eb75d08cb64b2432ec
ms.openlocfilehash: 00b71701c4c114007e2d9a6d23f20b3df5fb2534
ms.lasthandoff: 02/21/2017


---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する
Azure サブスクリプションまたはサブスクリプションで使われる Azure サービスを管理する Azure 管理者を変更できます。 Azure 課金情報の確認およびサブスクリプションの管理を行うには、[アカウント センター](https://account.windowsazure.com/Home/Index)にアカウント管理者としてサインインする必要があります。 

## <a name="add-an-admin-for-a-subscription"></a>サブスクリプションの管理者を追加する
Azure Portal または Azure クラシック ポータルで、Azure 管理者を追加できます。

**Azure ポータル**

Azure Portal でユーザーをサブスクリプションの管理者として追加するには、そのユーザーに所有者ロールを付与します。 所有者ロールでは、割り当てられたサブスクリプション内のリソースのみを管理できます。 他のサブスクリプションへのアクセス権はありません。 [Azure Portal](https://portal.azure.com) で追加した所有者は、[Azure クラシック ポータル](https://manage.windowsazure.com)でリソースを管理できません。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューで **[サブスクリプション]** > *管理者にアクセスさせたいサブスクリプション*を選択します。

    ![選択されたサブスクリプションのスクリーンショット](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. サブスクリプション ブレードで、**[アクセス制御 (IAM)]** > **[追加]** の順に選択します。

    ![選択された [追加] ボタンを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. **[役割を選択]** > **[所有者]** の順に選択します。

    ![選択された所有者ロールを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. 所有者として追加するユーザーの電子メール アドレスを入力し、ユーザーをクリックしてから、 **[選択]**をクリックします。

    ![追加されて選択されたユーザーのメール アドレスを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Azure クラシック ポータル**

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。
2. ナビゲーション ウィンドウで、**[設定]** > **[管理者]** > **[追加]** の順に選択します。 </br>

    ![[追加] ボタンへの移動方法を示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 共同管理者として追加する個人の電子メール アドレスを入力し、共同管理者にアクセス権を付与するサブスクリプションを選択します。</br>

    ![選択されたサブスクリプションのスクリーンショット ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

次の電子メール アドレスを共同管理者として追加できます。

* **Microsoft アカウント** (以前の Windows Live ID) </br>
  Microsoft アカウントを使用して、Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone、Xbox LIVE などのすべてのコンシューマー向け Microsoft 製品とクラウド サービスにサインインすることができます。
* **Organizational account**</br>
  組織アカウントとは、Azure Active Directory で作成したアカウントです。 組織アカウントのアドレスの形式は次のとおりです。

    user@&lt;ドメイン&gt;.onmicrosoft.com



## <a name="change-service-administrator-for-a-subscription"></a>サブスクリプションのサービス管理者を変更する
サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。
2. 変更するサブスクリプションを選択します。
3. 右側にある **[サブスクリプション詳細の編集]** をクリックします。 </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。 </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>アカウント管理者を変更する
Azure アカウントの所有権を別のアカウントに譲渡するには、「 [Azure サブスクリプションの所有権の譲渡](billing-subscription-transfer.md)」を参照してください。

アカウント管理者のメール アドレスは削除または名前変更しないことを強くお勧めします。 Azure アカウントで予期しない、望ましくない動作が発生する可能性があります。 そのアカウントでの Azure へのサインイン、そのアカウントに対する変更、またはそのアカウントでのリソースの管理が、できなくなる可能性があります。 

## <a name="check-the-account-administrator-of-the-subscription"></a>サブスクリプションのアカウント管理者を確認する
サブスクリプションのアカウント管理者が不明な場合は、次の手順で確認します。

  1. [Azure ポータル](https://portal.azure.com)にサインインします。
  2. ハブ メニューで、 **[サブスクリプション]**を選択します。
  3. 確認するサブスクリプションを選択し、**[設定]** を調べます。
  4. **[プロパティ]**を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。  

## <a name="types-of-azure-admin-accounts"></a>Azure 管理者アカウントの種類
 Microsoft Azure の管理者ロールには、アカウント管理者、サービス管理者、共同管理者の&3; 種類があります。 次の表では、これら&3; つの管理ロールの違いについて説明します。

| 管理ロール | 制限 | Description |
| --- | --- | --- |
| アカウント管理者 (AA) |1 Azure アカウントに&1; 人 |Azure サブスクリプションをサインアップまたは購入し、 [アカウント センター](https://account.windowsazure.com/Home/Index) にアクセスして多様な管理タスクを実行する権限を持っている個人。 管理タスクには、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。 |
| サービス管理者 (SA) |1 Azure サブスクリプションに 1 人 |このロールには、 [Azure Portal](https://portal.azure.com)でのサービスの管理が許可されます。 既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。 |
| [Azure クラシック ポータル](https://manage.windowsazure.com) |サブスクリプションあたり&200; 人 |サービス管理者と同じアクセス権を持っているものの、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできないロール。 |

Azure Active Directory のロールベースのアクセス制御 (RBAC) では、ユーザーを複数のロールに追加できます。 詳細については、「 [Azure Active Directory のロールベースの Access Control](../active-directory/role-based-access-control-configure.md)」を参照してください。

## <a name="limitations-and-restrictions-for-admin-accounts"></a>管理者アカウントの制限事項および制約事項
* 各サブスクリプションは Azure AD ディレクトリ (既定のディレクトリとも呼ばれます) と関連付けられます。 サブスクリプションが関連付けられている既定のディレクトリを探すには、[Azure クラシック ポータル](https://manage.windowsazure.com/)にアクセスし、**[設定]** > **[サブスクリプション]** の順に選択します。 サブスクリプション ID をチェックして既定のディレクトリを見つけてください。
* Microsoft アカウントを使用してサインインした場合、他の Microsoft アカウントまたは既定のディレクトリ内のユーザーを共同管理者として追加することしかできません。
* 組織アカウントを使用してサインインした場合、組織内の他の組織アカウントを共同管理者として追加することができます。 たとえば、abby@contoso.com はサービス管理者または共同管理者として bob@contoso.com を追加できますが、john@noncontoso.com が既定のディレクトリ内にない限り john@notcontoso.com は追加できません。 組織アカウントでサインインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者または共同管理者として引き続き追加できます。
* 組織アカウントで Azure にサインインできるようになると、サービス管理者と共同管理者アカウントの要件は次のように変わります。

  | サインイン方法 | Microsoft アカウントまたは既定のディレクトリ内のユーザーを CA または SA として追加できる | 同じ組織内の組織アカウントを CA または SA として追加できる | 異なる組織内の組織アカウントを CA または SA として追加できる |
  | --- | --- | --- | --- |
  |  Microsoft アカウント |はい |なし |なし |
  |  組織アカウント |はい |あり |いいえ |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>リソースのアクセス制御と Active Directory の詳細
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「[Azure でのリソース アクセスについて](../active-directory/active-directory-understanding-resource-access.md)」を参照してください。
* Azure Active Directory について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」および「[Azure Active Directory での管理者ロールの割り当て](../active-directory/active-directory-assign-admin-roles.md)」をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

