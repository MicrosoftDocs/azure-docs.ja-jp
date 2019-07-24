---
title: チュートリアル:Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs
description: Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852567"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>チュートリアル:Azure Active Directory と Salesforce Sandbox の統合

このチュートリアルでは、Salesforce Sandbox と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sandbox を使用すると、Salesforce 運用組織内のデータとアプリケーションを侵害することなく、多様な用途 (開発、テスト、トレーニングなど) ごとに別環境に組織の複数コピーを作成できます。
詳細については、 [サンドボックスの概要](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)に関するページを参照してください。

Salesforce Sandbox と Azure AD の統合には、次の利点があります。

- Salesforce Sandbox にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Salesforce Sandbox にサインオン (シングル サインオン) されるようにすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce Sandbox の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Salesforce Sandbox でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Salesforce Sandbox の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>ギャラリーからの Salesforce Sandbox の追加

Azure AD への Salesforce Sandbox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce Sandbox を追加する必要があります。

**ギャラリーから Salesforce Sandbox を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Salesforce Sandbox**」と入力し、結果パネルで **[Salesforce Sandbox]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リスト内の Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Salesforce Sandbox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce Sandbox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce Sandbox の関連ユーザーの間で、リンク関係が確立されている必要があります。

Salesforce Sandbox で、Azure AD の **ユーザー名**の値を**ユーザー名**の値として割り当ててリンク関係を確立します。

Salesforce Sandbox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Salesforce Sandbox テスト ユーザーの作成](#create-a-salesforce-sandbox-test-user)** - Salesforce Sandbox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Salesforce Sandbox アプリケーションでシングル サインオンを構成します。

**Salesforce Sandbox で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Salesforce Sandbox** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![シングル サインオン構成のリンク](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![シングル サインオン構成のリンク](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。
   
    ![シングル サインオン構成のリンク](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > このチュートリアルの後述するように、Salesforce Sandbox 管理ポータルからサービス プロバイダーのメタデータ ファイルを取得します。

    c. メタデータ ファイルが正常にアップロードされると、**[応答 URL]** の値が **[応答 URL]** ボックスに自動的に入力されます。

    ![Salesforce Sandbox Domain のドメインと URL のシングル サインオン情報](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータの XML** をダウンロードしてから、コンピューターに XML ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにログインします。

8. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

9. 左側のナビゲーション ウィンドウの **[設定]** まで下にスクロールし、**[ID]** をクリックして、関連セクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. **[シングル サインオンの設定]** ページで、**[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

11. **[SAML 有効]** を選択し、**[保存]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. SAML のシングル サインオン設定を構成するには、**[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードし、**[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. **[SAML シングル サインオンの設定]** ページでは、フィールドは自動的に入力されます。[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. **[シングル サインオンの設定]** ページで **[メタデータのダウンロード]** ボタンをクリックし、サービス プロバイダーのメタデータ ファイルをダウンロードします。 前述の必要な URL を構成するために、Azure portal の **[基本的な SAML 構成]** セクションでこのファイルを使用します。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure4.png)

16. **SP** 開始モードでアプリケーションを構成する場合、その前提条件を以下に示します。

    a. 確認済みドメインを持っている必要があります。

    b. Salesforce Sandbox でドメインを構成して有効にする必要があります。手順については、このチュートリアルで後述します。

    c. Azure portal の **[基本的な SAML 構成]** セクションで **[追加の URL を設定します]** をクリックして、次の手順を実行します。
  
    ![Salesforce Sandbox Domain のドメインと URL のシングル サインオン情報](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    **[サインオン URL]** ボックスに、`https://<instancename>--Sandbox.<entityid>.my.salesforce.com` のパターンを使用して値を入力します。

    > [!NOTE]
    > この値は、ドメインを有効にした後で Salesforce Sandbox ポータルからコピーする必要があります。

17. **[SAML 署名証明書]** セクションで **[フェデレーション メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにログインします。

19. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

20. 左側のナビゲーション ウィンドウの **[設定]** まで下にスクロールし、**[ID]** をクリックして、関連セクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. **[シングル サインオンの設定]** ページで、**[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

22. **[SAML 有効]** を選択し、**[保存]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. SAML のシングル サインオン設定を構成するには、**[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. **[ファイルの選択]** をクリックし、メタデータ XML ファイルをアップロードして **[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. **[SAML Single Sign-On Settings]\(SAML のシングル サインオン設定\)** ページのフィールドには自動的に値が入力されています。**[名前]** ボックスに構成の名前 (例: *SPSSOWAAD_Test*) を入力し、[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Salesforce Sandbox でドメインを有効にするには、次の手順を実行します。

    > [!NOTE]
    > ドメインを有効にする前に、Salesforce Sandbox 上に同じドメインを作成する必要があります。 詳細については、「[ドメイン名の定義](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)」をご覧ください。 ドメインを作成したら、ドメインが正しく構成されていることを確認してください。

    * Salesforce Sandbox の左側のナビゲーション ウィンドウで、**[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、**[My Domain]\(マイ ドメイン\)** をクリックします。

         ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * **[Authentication Configuration]\(認証の構成\)** セクションで、**[Edit]\(編集\)** をクリックします。

        ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * **[Authentication Configuration]\(認証の構成\)** セクションで、**[Authentication Service]\(認証サービス\)** として、Salesforce Sandbox の SSO 構成時に設定した [SAML Single Sign-On Setting]\(SAML シングル サインオン設定\) の名前を選択し、**[Save]\(保存\)** をクリックします。

        ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="create-a-salesforce-sandbox-test-user"></a>Salesforce Sandbox のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Salesforce Sandbox に作成します。 Salesforce Sandbox では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce Sandbox に存在しない場合は、Salesforce Sandbox にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce Sandbox は、自動ユーザー プロビジョニングもサポートしています。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](salesforce-sandbox-provisioning-tutorial.md)を参照してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce Sandbox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Salesforce Sandbox に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Salesforce Sandbox]** を選択します。

    ![アプリケーション リストの [Salesforce Sandbox] リンク](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[ユーザーの追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Salesforce Sandbox] タイルをクリックすると、自動的に Salesforce Sandbox アプリケーションにサインオンするはずです。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
