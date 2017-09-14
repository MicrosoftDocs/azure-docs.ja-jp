---
title: "チュートリアル: Google Apps を構成し、Azure での自動ユーザー プロビジョニングに対応させる | Microsoft Docs"
description: "Azure AD から Google Apps に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b061f0ddad70be4a5ca48d48d1a737d6af8afa8d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-configuring-google-apps-for-automatic-user-provisioning"></a>チュートリアル: Google Apps を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Google Apps にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで、Google Apps と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Google Apps for Work または Google Apps for Education の有効なテナントが必要です。 どちらのサービスにも無料試用版のアカウントを使用できます。
*   Team Admin アクセス許可がある Google Apps のユーザー アカウント。

## <a name="assigning-users-to-google-apps"></a>Google Apps にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Google Apps アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関する手順に従って、これらのユーザーを Google Apps アプリに割り当てることができます。

> [!IMPORTANT]
>*   単一の Azure AD ユーザーを Google Apps に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。
>*   ユーザーを Google Apps に割り当てるときに、割り当てのダイアログでユーザーまたは "グループ" のロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Google Apps のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Google Apps で作成、更新、無効化する手順を説明します。

>[!Tip]
>Google Apps では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

> [!NOTE]
> Google Apps へのユーザー プロビジョニングを自動化する方法として、[Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) を使用して Google Apps にオンプレミスの Active Directory ID をプロビジョニングする方法もありますが、 このチュートリアルでは Azure Active Directory (クラウド) のユーザーとメールが有効なグループを Google Apps にプロビジョニングします。 

1. 管理者アカウントを使用して、 [Google Apps の管理コンソール](http://admin.google.com/) にサインインし、 **[セキュリティ]**をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。
   
    ![Click Security.][10]

2. **[セキュリティ]** ページで、**[API リファレンス]** をクリックします。
   
    ![Click API Reference.][15]

3. **[API アクセスを有効にする]**を選択します。
   
    ![Click API Reference.][16]

    > [!IMPORTANT]
    > Google Apps にプロビジョニングするすべてのユーザーは、Azure Active Directory でのユーザー名がカスタム ドメインに関連付けられている *必要があります* 。 たとえば Google Apps では、bob@contoso.onmicrosoft.com のようなユーザー名は使用できませんが、bob@contoso.com のようなユーザー名は使用できます。 Azure AD でプロパティを編集することによって、既存のユーザーのドメインを変更できます。 Azure Active Directory と Google Apps の両方でカスタム ドメインを設定する方法を次の手順に示します。
      
4. まだ Azure Active Directory にカスタム ドメイン名を追加していない場合は、次の手順に従います。
  
    a. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。 ディレクトリの一覧で、ディレクトリを選択します。 

    b. 左のナビゲーション ウィンドウで **[ドメイン名]** をクリックし、**[追加]** をクリックします。
     
     ![ドメイン](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![ドメインを追加](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. **[ドメイン名]** フィールドにドメイン名を入力します。 ドメイン名は、Google Apps で使用するのと同じドメイン名にする必要があります。 準備ができたら、**[ドメインの追加]** をクリックします。
     
     ![ドメイン名](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. **[次へ]** をクリックし、確認ページに移動します。 このドメインを所有していることを確認するために、このページに表示されている値に従ってドメインの DNS レコードを編集する必要があります。 **[レコードの種類]** オプションでの選択内容に応じて、**MX レコード**と **TXT レコード**のどちらを使用して確認するかを選択できます。 Azure AD でドメイン名を確認する、より包括的な手順の詳細については、[Azure AD への独自のドメイン名の追加](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)に関するページをご覧ください。
     
     ![ドメイン](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. ディレクトリに追加するすべてのドメインに対して、前述の手順を繰り返します。

5. すべてのドメインを Azure AD で確認したので、今度は Google Apps で確認する必要があります。 Google Apps にまだ登録されていない各ドメインに対して、次の手順を実行します。
   
    a. [Google Apps の管理コンソール](http://admin.google.com/)で、**[ドメイン]** をクリックします。
     
     ![Click on Domains][20]

    b. **[ドメインやドメイン エイリアスを追加]**をクリックします。
     
     ![Add a new domain][21]

    c. **[別のドメインを追加]**を選択し、追加するドメインの名前を入力します。
     
     ![Type in your domain name][22]

    d. **[Continue and verify domain ownership]\(続行してドメインの所有権を確認\)** をクリックします。 次に、手順に従って、ドメイン名を所有していることを確認します。 Google Apps でドメインを確認する包括的な手順については、 [Google Apps でサイトの所有者であることを確認する](https://support.google.com/webmasters/answer/35179)方法に関するページをご覧ください。

    e. Google Apps に追加するすべての追加ドメインに対して、前述の手順を繰り返します。
     
     > [!WARNING]
     > Google Apps テナントのプライマリ ドメインを変更する場合や、Azure AD でシングル サインオンが構成済みである場合は、[手順 2: シングル サインオンを有効にする](#step-two-enable-single-sign-on)の手順 3 を繰り返す必要があります。
       
6. [Google Apps の管理コンソール](http://admin.google.com/)で、**[管理者ロール]** をクリックします。
   
     ![Click on Google Apps][26]

7. ユーザー プロビジョニングの管理にどの管理者アカウントを使用するかを決定します。 使用するアカウントの**[管理者の役割]** で、役割の **[権限]** を編集します。 このアカウントをプロビジョニングに使用できるように、**[Admin API Privileges]\(管理 API の権限\)** がすべて有効になっていることを確認します。
   
     ![Click on Google Apps][27]
   
    > [!NOTE]
    > 運用環境を構成している場合は、特にこの手順で Google Apps の管理者アカウントを作成することをお勧めします。 管理者アカウントには、必要な API 特権を持つ管理者ロールが関連付けられている必要があります。
     
8. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

9. シングル サインオンのために Google Apps を既に構成している場合は、検索フィールドで Google Apps のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Google Apps** を検索します。 検索結果から Google Apps を選択してアプリケーションの一覧に追加します。

10. Google Apps のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

11. **[プロビジョニング モード]** を **[自動]** に設定します。 

     ![プロビジョニング](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 これで、ブラウザーの新しいウィンドウで Google Apps 承認ダイアログが開きます。

13. Google Apps のテナントに対して変更を行うためのアクセス許可を Azure Active Directory に付与することを確認します。 **[Accept]\(受け入れる\)** をクリックします。
    
     ![Confirm permissions.][28]

14. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Google Apps アプリに接続できることを確認します。 接続が失敗した場合、使用中の Google Apps アカウントに Team Admin アクセス許可があることを確認して、**"承認"** の手順をもう一度試してください。

15. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

16. **[保存]** をクリックします。

17. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Google Apps]\(Azure Active Directory ユーザーを Google Apps に同期する\)** を選択します。

18. **[属性マッピング]** セクションで、Azure AD から Google Apps に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Google Apps のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

19. Google Apps に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

20. **[保存]** をクリックします。

[ユーザーとグループ] セクションで Google Apps に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Google Apps アプリに対して実行されたすべてのアクションが記載されています。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
