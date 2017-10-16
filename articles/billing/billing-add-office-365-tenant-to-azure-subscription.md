---
title: "Office 365 テナントを Azure サブスクリプションで使用する | Microsoft Docs"
description: "Office 365 ディレクトリ (テナント) を Azure サブスクリプションに追加する方法について説明します。"
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.openlocfilehash: e6300932d044ec9a4f88eb5bd5977220ed11d513
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>Office 365 テナントを Azure サブスクリプションにリンクする
個別の Azure サブスクリプションと Office 365 サブスクリプションをリンクして、Azure サブスクリプションから Office 365 テナントにアクセスできるようにします。 サブスクリプションをリンクするには、Azure サービス管理者アカウントで Azure にサインインし、ディレクトリを追加して、Office 365 の職場または学校アカウントを Azure Active Directory テナントに追加します。

**既存の Azure サブスクリプションを Office 365 の職場または学校アカウントに移動する場合** 個人の Microsoft アカウントで Azure にサインアップしていて、それを使いたい場合、または Office 365 のアカウントでサインインしたい場合は、サブスクリプションの所有権を譲渡することを強くお勧めします。 「[別のアカウントに Azure サブスクリプションの所有権を譲渡する](billing-subscription-transfer.md)」をご覧ください。 

**Office 365 を使って Azure にサインアップする場合** 「[Office 365 アカウントを使用して Azure にサインアップする](billing-use-existing-office-365-account-azure-subscription.md)」をご覧ください。 

## <a name="before-you-begin"></a>開始する前に
* Azure サブスクリプション サービス管理者の資格情報が必要です。 共同管理者アカウントでは、この記事の手順の一部を実行できません。 サービス管理者を変更する場合は、「[Azure 管理者ロールを追加または変更する方法](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)」をご覧ください。
* Office 365 テナントの全体管理者の資格情報が必要です。
* サービス管理者のメール アドレスが Office 365 テナントに含まれていないことが必要です。
* サービス管理者の電子メール アドレスが、Office 365 テナントのどの全体管理者の電子メール アドレスとも異なっていることが必要です。
* Microsoft アカウントと組織アカウントの両方に該当するメール アドレスを使っている場合は、別の Microsoft アカウントを使うように、Azure サブスクリプションのサービス管理者を一時的に変更してください。 Microsoft アカウントは、[Microsoft アカウントのサインアップ ページ](https://signup.live.com/)で作成できます。

## <a name="link-office-365-tenant-to-azure-subscription"></a>Office 365 テナントを Azure サブスクリプションにリンクする
Office 365 テナントを Azure サブスクリプションに関連付けるには、次の手順を実行します。

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>手順 1: Office 365 テナントを Azure サブスクリプションに追加する

1. サービス管理者の資格情報で [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。

    ![Azure サインイン ページのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
2. 左ウィンドウで、**[Active Directory]** を選択します。 Office 365 テナントが表示されていないことを確認してください。 表示されている場合は、「[手順 2: Azure サブスクリプションに関連付けられているディレクトリを変更する](#Step2)」に進みます。
   
   ![Azure Active Directory のエントリのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
3. **[新規]**  >  **[ディレクトリ]**  >  **[カスタム作成]** を選択します。
   
    ![Azure Active Directory のカスタム作成のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
4. **[ディレクトリの追加]** ページの **[ディレクトリ]** で、**[既存のディレクトリの使用]** を選択します。 次に、**[サインアウトする準備ができました。]**を選択し、**[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) を選択します。
   
    ![[既存のディレクトリの使用] のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
5. サインアウト後、Office 365 テナントの全体管理者の資格情報でサインインします。
   
    ![Office 365 グローバル管理者のサインインのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
6. **[続行]** をクリックします。
   
    ![検証のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
7. **[今すぐサインアウト]** を選択します。
   
    ![[保存] アイコンのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
8. サービス管理者の資格情報で [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。
   
    ![Azure サインイン ページのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
9. ダッシュボードに Office 365 テナントが表示されます。
   
    ![ダッシュボードのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>手順 2: Azure サブスクリプションに関連付けられているディレクトリを変更する
   
1. **[設定]**を選択します。
   
    ![Azure クラシック ポータルの設定アイコンのスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
2. Azure サブスクリプションを選択し、**[ディレクトリの編集]** をクリックします。

    ![Azure サブスクリプションのディレクトリの編集のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
3. **[次へ]** ![[次へ] アイコン](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png) を選びます。
   
    ![[関連付けられたディレクトリの変更] のスクリーンショット](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
4. 影響を受けたアカウントを確認します。 すべての共同管理者と、既存のリソース グループでアクセス権が割り当てられている[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) のユーザーが、削除されます。 表示される警告で言及されるのは共同管理者の削除だけです。
      
    ![削除される共同管理者アカウントを示すスクリーンショット。](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![削除されるユーザー アカウントの例を示すスクリーンショット。](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
5. **[完了]** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png) を選択します。

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>手順 3: 管理者として Azure のサブスクリプションに、Office 365 組織アカウントを追加します。
   
Azure サブスクリプションへの管理者の追加の詳細については、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](billing-add-change-azure-subscription-administrator.md)」を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

