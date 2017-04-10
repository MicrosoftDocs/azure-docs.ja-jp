---
title: "チュートリアル: Azure Active Directory と Boomi の統合 | Microsoft Docs"
description: "Azure Active Directory と Boomi の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e5d37e9e723d97d1e6844f0b89ad1c2aae258565
ms.openlocfilehash: 9ab3ac2cd6643b0e53f1f7853239c90e78f4bf77
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>チュートリアル: Azure Active Directory と Boomi の統合

このチュートリアルでは、Boomi と Azure Active Directory (Azure AD) を統合する方法について説明します。

Boomi と Azure AD の統合には、次の利点があります。

- Boomi にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Boomi にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Boomi と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Boomi でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Boomi の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-boomi-from-the-gallery"></a>ギャラリーからの Boomi の追加
Azure AD への Boomi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Boomi を追加する必要があります。

**ギャラリーから Boomi を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Boomi**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_000.png)

5. 結果ウィンドウで **[Boomi]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Boomi で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Boomi ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Boomi の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Boomi の **[Username]** の値として割り当てます。

Boomi で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Boomi のテスト ユーザーの作成](#creating-a-boomi-test-user)** - Boomi で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Boomi アプリケーションにシングル サインオンを構成します。

**Boomi で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Boomi** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

3. **[Boomi のドメインと URL]** セクションの **[応答 URL]** ボックスに、`https://platform.boomi.com/sso/<account name>/saml` のパターンを使用して URL を入力します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際の応答 URL で更新する必要があります。 この値を取得するには、Boomi サポート チームに問い合わせてください。 

4. Boomi アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | FEDERATION_ID | User.mail |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**

6. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)     

7. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_general_300.png)

8. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_07.png)

9. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

10. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_08.png) 

11. **[Boomi 構成]** セクションで、**[Boomi の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_09.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_10.png)

12. 別の Web ブラウザー ウィンドウで、Boomi 企業サイトに管理者としてログインします。 

13. **[会社名]**、**[セットアップ]** の順に進みます。

14. **[SSO オプション]** タブをクリックして以下の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Enable SAML Single Sign-On](SAML シングル サインオンの有効化)** のチェックボックスをオンにします。

    b. **[インポート]** をクリックして、Azure AD からダウンロードした証明書を **ID プロバーダー証明書** にアップロードします。
    
    c. **[ID プロバイダーのログイン URL]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-On Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    d. **[Federation Id Location]** で、**[Federation Id is in FEDERATION_ID Attribute element]** オプションを選択します。 

    e. **[保存]** ボタンをクリックします。
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-boomi-test-user"></a>Boomi のテスト ユーザーの作成

Azure AD ユーザーが Boomi にログインできるようにするには、ユーザーを Boomi にプロビジョニングする必要があります。 Boomi の場合、プロビジョニングは手動で行います。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Boomi 企業サイトに管理者としてログインします。

2. **[ユーザー管理]**、**[ユーザー]** の順に進みます。

    ![ユーザー](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. **+** アイコンをクリックすると **[Add/Maintain User Roles](ユーザーの役割の追加と管理)** ダイアログが開きます。

    ![ユーザー](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![ユーザー](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. ユーザーの**電子メール アドレス**を入力します。

5. ユーザーの**名**、**姓**を入力します。

6. ユーザーの**フェデレーション ID** を入力します。 各ユーザーには、アカウント内のユーザーを一意に識別するフェデレーション ID が必要です。 

7. **標準ユーザー**の役割をユーザーに割り当てます。 シングル サインオンのアクセスのほかに、Boomi AtomSphere の通常のアクセスも付与することになるため、管理者の役割は割り当てないでください。

8. **[OK]**をクリックします。

    > [!NOTE]
    > ユーザー パスワードは ID プロバイダーを通じて管理されるため、AtomSphere アカウントのログインに使用するパスワードを含む、「ようこそ」の通知メールはユーザーに送信されません。 Boomi から提供されている他の Boomi ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Boomi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Boomi に割り当てるには、次の手順に従います。**

1. Microsoft Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Boomi]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Boomi] タイルをクリックすると、自動的に Boomi アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
