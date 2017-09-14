---
title: "チュートリアル: Azure Active Directory と Workplace by Facebook の統合 | Microsoft Docs"
description: "Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>チュートリアル: Azure Active Directory と Workplace by Facebook の統合

このチュートリアルでは、Workplace by Facebook と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workplace by Facebook と Azure AD の統合には、次の利点があります。

- Workplace by Facebook にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Workplace by Facebook にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Workplace by Facebook の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workplace by Facebook でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workplace by Facebook の追加。
2. Azure AD シングル サインオンの構成とテスト。

## <a name="add-workplace-by-facebook-from-the-gallery"></a>ギャラリーから Workplace by Facebook を追加する
Azure AD への Workplace by Facebook の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workplace by Facebook を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Workplace by Facebook**」と入力し、結果から **[Workplace by Facebook]** を選択します。 次に、**[追加]** を選択してアプリケーションを追加します。

    ![結果リストの Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workplace by Facebook で Azure AD の SSO を構成してテストします。

SSO を機能させるには、Azure AD ユーザーに対応する Workplace by Facebook ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workplace by Facebook の関連ユーザーの間で、リンク関係が確立されている必要があります。

Azure AD の **[ユーザー名]** の値を、Workplace by Facebook の **[Username]\(ユーザー名\)** の値として割り当てることで、このリンク関係を確立します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD の SSO を有効にし、Workplace by Facebook アプリケーションで SSO を構成します。

1. Azure ポータルの **Workplace by Facebook** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. SSO を有効にするには、**[シングル サインオン]** ダイアログ ボックスの **[モード]** ボックスで **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. **[Workplace by Facebook のドメインと URL]** セクションで、次の操作を行います。

    a. **[サインオン URL]** ボックスに、`https://<company subdomain>.facebook.com` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://www.facebook.com/company/<scim company id>` という形式で URL を入力します。

    > [!NOTE]
    > これらの値は、例としてのみ使用しています。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Workplace by Facebook クライアント サポート チーム](https://workplace.fb.com/faq/)にお問い合わせください。 

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** を選択し、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. [ **保存**] を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. **[Workplace by Facebook 構成]** セクションで、**[Workplace by Facebook の構成する]** を選択して **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Workplace by Facebook の構成](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. 別の Web ブラウザーのウィンドウで、Workplace by Facebook 企業サイトに管理者としてサインインします。
  
   > [!NOTE] 
   > SAML 認証プロセスの一環として、Azure AD にパラメーターを渡すために Workplace が最大サイズ 2.5 KBのクエリ文字列を使用する可能性があります。

8. **[Company Dashboard]\(会社のダッシュボード\)** で、**[Authentication]\(認証\)** タブに移動します。

9. **[SAML Authentication]\(SAML 認証\)** で、ドロップダウン リストの一覧から **[SSO Only]\(SSO のみ\)** を選択します。

10. Azure ポータルの **[Workplace by Facebook の構成]** セクションからコピーした値を対応するフィールドに入力します。

    *   **[SAML URL]** ボックスに、Azure ポータルからコピーした**シングル サインオン サービス URL** の値を貼り付けます。
    *   **[SAML Issuer URL]\(SAML 発行者のURL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    *   **[SAML Logout redirect]\(SAML ログアウト リダイレクト\)** (オプション) に、Azure ポータルからコピーした**サインアウト URL** の値を貼り付けます。
    *   Azure ポータルからダウンロードした **base 64 エンコード証明書**をメモ帳で開きます。 その内容をクリップボードにコピーし、**[SAML Certificate]\(SAML 証明書\)** テキスト ボックスに貼り付けます。

11. **[SAML Configuration]\(SAML の構成\)** セクションに一覧表示されている [Audience URL]\(対象 URL\)、[Recipient URL]\(受信側 URL\)、[ACS (Assertion Consumer Service) URL] の入力が必要になる場合もあります。

12. セクションの下部にスクロールし、**[Test SSO]\(SSO のテスト\)** をクリックします。 ポップアップ ウィンドウが開き、Azure AD サインイン ページが表示されます。 認証するには、通常どおり資格情報を入力します。 Azure AD から返された電子メール アドレスが、ログインに使用した Workplace アカウントと同じであることを確認してください。

13. テストが正常に完了したら、ページの一番下にスクロールし、**[保存]** をクリックします。

14. Workplace を使用しているすべてのユーザーに、認証用の Azure AD サインイン ページが表示されるようになりました。

Azure AD のサインアウト ページを指定するために使用できる SAML サインアウト URL を構成することを選択できます。 この設定が有効に構成されている場合は、ユーザーに対して Workplace サインアウト ページが表示されなくなります。 代わりに、SAML サインアウト リダイレクトの設定に追加された URL にユーザーがリダイレクトされるようになります。


> [!TIP]
> アプリのセットアップ中、[Azure ポータル](https://portal.azure.com)内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能について詳しくは、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="configure-reauthentication-frequency"></a>再認証の頻度の構成

SAML チェックの要求を毎日、3 日ごと、1 週間ごと、2 週間ごと、1 か月ごとに行う、または行わないように、Workplace を構成できます。

> [!NOTE] 
>モバイル アプリケーションで設定できる SAML チェック頻度の最小値は 1 週間です。

すべてのユーザーに対して SAML のリセットを強制することもできます。 これを行うには、**[Require SAML authentication for all users now]\(今すぐすべてのユーザーの SAML 認証を要求する\)** ボタンを使用します。


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

1. **Azure Portal** の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選択します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログ ボックスを開くには、**[追加]** を選択します。
 
    ![[追加] ボタン](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードの表示]** を選択し、パスワードを書き留めます。

    d. **[作成]**を選択します。
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Workplace by Facebook テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Workplace by Facebook に作成します。 Workplace by Facebook では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Workplace by Facebook にユーザーが 1 人もいない場合は、Workplace by Facebook にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Workplace by Facebook クライアント サポート チーム](https://workplace.fb.com/faq/)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Workplace by Facebook へのアクセスを許可することで、Britta Simon が Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

1. Azure ポータルで、アプリケーション ビューを開きます。 ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動した後、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Workplace by Facebook]** を選択します。

    ![アプリケーションの一覧の Workplace by Facebook リンク](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]**を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

SSO の設定をテストする場合は、アクセス パネルを開きます。
詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)を参照します。
* 「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を読みます。
* [ユーザーのプロビジョニングを構成する](active-directory-saas-facebook-at-work-provisioning-tutorial.md)方法を確認します。



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png


