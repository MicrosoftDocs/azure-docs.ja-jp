---
title: インフォメーション ワーカーとして B2B コラボレーション ユーザーを追加する - Azure AD
description: B2B コラボレーションを利用して、インフォメーション ワーカーおよびアプリ所有者がアクセスのために Azure AD にゲスト ユーザーを追加する | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219914"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>組織内のユーザーがゲスト ユーザーをアプリに招待する方法

ゲスト ユーザーが Azure AD のディレクトリに追加されたら、アプリケーション所有者は、共有したいアプリへの直接リンクをゲスト ユーザーに送信できます。 また、Azure AD 管理者は、それぞれの Azure AD テナントにギャラリーまたは SAML ベースのアプリのセルフサービス管理を設定することもできます。 これにより、ゲスト ユーザーがまだディレクトリに追加されていない場合でもアプリケーション所有者が自身のゲスト ユーザーを管理できます。 アプリがセルフサービス用に構成されたら、アプリケーション所有者はアクセス パネルを使用して、アプリにゲスト ユーザーを招待するか、またはアプリにアクセスできるグループにゲスト ユーザーを追加します。 ギャラリーや SAML ベースのアプリのセルフサービス アプリ管理では、管理者によるいくつかの初期設定が必要です。以下に設定手順の概要を示します (詳細な手順については、このページで後述する「[前提条件](#prerequisites)」を参照してください)。

 - テナント用にセルフサービスのグループ管理を有効化する
 - アプリに割り当てるグループを作成して、ユーザーを所有者にする
 - セルフサービス用にアプリを構成して、グループをアプリに割り当てる

> [!NOTE]
> * この記事では、Azure AD テナントに追加したギャラリーおよび SAML ベースのアプリのセルフサービス管理を設定する方法について説明します。 ユーザーが自身の Microsoft 365 グループへのアクセスを管理できるように、[Microsoft 365 のセルフサービス グループを設定する](../enterprise-users/groups-self-service-management.md)こともできます。 ユーザーが Office ファイルとアプリをゲスト ユーザーと共有できるその他の方法については、[Microsoft 365 グループでのゲスト アクセス](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6)に関するページと、「[SharePoint ファイルまたはフォルダーの共有](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)」を参照してください。
> * ユーザーは、**ゲスト招待元** ロールが与えられている場合にのみ、ゲストを招待できます。
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>アクセス パネルからアプリにゲスト ユーザーを招待する

セルフサービス用にアプリが構成された後、アプリケーション所有者は自身のアクセス パネルを使用して、共有するアプリにゲスト ユーザーを招待できます。 ゲスト ユーザーは、必ずしも事前に Azure AD に追加されている必要はありません。 

1. `https://myapps.microsoft.com` に移動して、アクセス パネルを開きます。
2. アプリをポイントし、省略記号 ( **...** ) を選択してし、 **[アプリの管理]** を選択します。
 
   ![Salesforce アプリの [アプリの管理] サブメニューを示すスクリーンショット](media/add-users-iw/access-panel-manage-app.png)
 
3. ユーザー一覧の最上部で、 **+** を選択します。
   
   ![アプリにメンバーを追加するためのプラス記号を示すスクリーンショット](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. **[メンバーの追加]** 検索ボックスに、ゲスト ユーザーの電子メール アドレスを入力します。 必要に応じて、ようこそメッセージを含めます。
   
   ![ゲストを追加するための [メンバーの追加] ウィンドウを示すスクリーンショット](media/add-users-iw/access-panel-invitation.png)
   
5. **[追加]** を選択して、ゲスト ユーザーに招待を送信します。 招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>アプリにアクセスできるグループに参加する人を招待する
セルフサービス用にアプリが構成された後、アプリケーション所有者は、共有するアプリにアクセスできる自身の管理下のグループに、ゲスト ユーザーを招待できます。 ゲスト ユーザーが、ディレクトリに既に存在している必要はありません。 アプリケーション所有者は次の手順に従って、アプリにアクセスできるようにゲスト ユーザーをグループに招待します。

1. 自分が、共有したいアプリにアクセスできるセルフサービス グループの所有者であることを確認します。
2. `https://myapps.microsoft.com` に移動して、アクセス パネルを開きます。
3. **[グループ]** アプリを選択します。
   
   ![アクセス パネルの [グループ] アプリを示すスクリーンショット](media/add-users-iw/access-panel-groups.png)
   
4. **[Groups I own]\(所有するグループ\)** で、共有したいアプリにアクセスできるグループを選択します。
   
   ![[所有しているグループ] でグループを選択する場所を示すスクリーンショット](media/add-users-iw/access-panel-groups-i-own.png)
   
5. グループ メンバー一覧の最上部で、 **+** を選択します。
   
   ![グループにメンバーを追加するためのプラス記号を示すスクリーンショット](media/add-users-iw/access-panel-groups-add-member.png)
   
6. **[メンバーの追加]** 検索ボックスに、ゲスト ユーザーの電子メール アドレスを入力します。 必要に応じて、ようこそメッセージを含めます。
   
   ![ゲストを追加するための [メンバーの追加] ウィンドウを示すスクリーンショット](media/add-users-iw/access-panel-invitation.png)
   
7. **[追加]** を選択して、招待をゲスト ユーザーに自動的に送信します。 招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。


## <a name="prerequisites"></a>前提条件

セルフサービス アプリ管理には、グローバル管理者と Azure AD 管理者によるいくつかの初期設定が必要です。 この設定の一環として、セルフサービス用にアプリを構成して、アプリケーション所有者が管理できるアプリにグループを割り当てます。 また、誰でもメンバーシップを要求できるが、グループ所有者の承認が必要なグループを構成できます ([セルフサービス グループ管理](../enterprise-users/groups-self-service-management.md)に関するページを参照してください)。 

> [!NOTE]
> ゲスト ユーザーを、動的グループ、またはオンプレミスの Active Directory と同期しているグループに追加することはできません。

### <a name="enable-self-service-group-management-for-your-tenant"></a>テナント用にセルフサービスのグループ管理を有効化する
1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。
2. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
3. **[グループ]** を選びます。
4. **[設定]** で、 **[全般]** を選択します。
5. **[セルフサービス グループ管理]** で、 **[Owners can manage group membership requests in the Access Panel]\(所有者がアクセス パネルでグループ メンバーシップの要求を管理できる)** の横にある **[はい]** を選択します。
6. **[保存]** を選択します。

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>アプリに割り当てるグループを作成して、ユーザーを所有者にする
1. Azure AD 管理者またはグローバル管理者として [Azure Portal](https://portal.azure.com) にサインインします。
2. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
3. **[グループ]** を選びます。
4. **[新しいグループ]** を選びます。
5. **[グループの種類]** で **[セキュリティ]** を選択します。
6. **[グループ名]** および **[グループの説明]** を入力します。
7. **[メンバーシップの種類]** で **[割り当て済み]** を選択します。
8. **[作成]** を選択して、 **[グループ]** ページを閉じます。
9. **[Groups - All groups]\(グループ - すべてのグループ\)** ページで、グループを開きます。 
10. **[管理]** で、 **[所有者]**  >  **[所有者の追加]** の順に選択します。 アプリケーションへのアクセスの管理を担うユーザーを検索します。 該当のユーザーを選択し、 **[選択]** をクリックします。

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>セルフサービス用にアプリを構成して、グループをアプリに割り当てる
1. Azure AD 管理者またはグローバル管理者として [Azure Portal](https://portal.azure.com) にサインインします。
2. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
3. **[管理]** で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
4. アプリケーションの一覧で、アプリを検索して開きます。
5. **[管理]** で、 **[シングル サインオン]** を選択して、シングル サインオン用にアプリケーションを構成します (詳細については、[エンタープライズ アプリのシングル サインオンの管理方法](../manage-apps/add-application-portal-setup-sso.md)に関するページを参照してください)。
6. **[管理]** で、 **[セルフサービス]** を選択して、セルフサービス アプリ アクセスを設定します (詳細については、[セルフサービス アプリ アクセスの使用方法](../manage-apps/access-panel-manage-self-service-access.md)に関するページを参照してください)。 

    > [!NOTE]
    > **[To which group should assigned users be added?]\(割り当てたユーザーの追加先となるグループ\)** の設定に、前のセクションで作成したグループを選択します。
7. **[管理]** で、 **[Users and groups]\(ユーザーとグループ\)** を選択して、作成したセルフサービス グループが一覧に表示されていることを確認します。
8. グループ所有者のアクセス パネルにアプリを追加するには、 **[ユーザーの追加]**  >  **[Users and groups]\(ユーザーとグループ\)** の順に選択します。 グループ所有者を検索し、ユーザーを選択して **[選択]** をクリックし、 **[割り当て]** をクリックしてユーザーをアプリに追加します。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](add-users-administrator.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [External Identities の価格](external-identities-pricing.md)