---
title: "チュートリアル: Azure Active Directory と SAP Cloud Platform Identity Authentication の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Cloud Platform Identity Authentication の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>チュートリアル: Azure Active Directory と SAP Cloud Platform Identity Authentication の統合

このチュートリアルでは、SAP Cloud Platform Identity Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud Platform Identity Authentication は、Azure AD をメイン IdP として使用して SAP アプリケーションにアクセスするためのプロキシ IdP として使用されます。

SAP Cloud Platform Identity Authentication を Azure AD と統合すると、次の利点があります。

- SAP アプリケーションにアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP アプリケーションにシングル サインオン (SSO) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SAP Cloud Platform Identity Authentication と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP Cloud Platform Identity Authentication でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAP Cloud Platform Identity Authentication の追加
2. Azure AD シングル サインオンの構成とテスト

技術的な詳細の説明に入る前に、調べようとしている事柄の概念を理解する必要があります。 SAP Cloud Platform Identity Authentication と Azure Active Directory のフェデレーションにより、SAP Cloud Platform Identity Authentication で保護された SAP アプリケーションおよびサービスに対し、(IdP としての) AAD によって保護されたアプリケーションまたはサービス全体で SSO を実装できます。

現時点では、SAP Cloud Platform Identity Authentication は、SAP アプリケーションへのプロキシ ID プロバイダーとして機能します。 さらに Azure Active Directory は、このセットアップにおける主要な ID プロバイダーとして機能します。 

これを次の図に示します。

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

このセットアップにより、SAP Cloud Platform Identity Authentication テナントは、Azure Active Directory で信頼されたアプリケーションとして構成されます。 

この方法で保護するすべての SAP アプリケーションおよびサービスは、その後で SAP Cloud Platform Identity Authentication 管理コンソールで構成されます。

つまり、SAP アプリケーションおよびサービスへのアクセスの許可のための承認は、このようなセットアップ向けに、SAP Cloud Platform Identity Authentication で行われる必要があります (Azure Active Directory で承認を構成するのではありません)。

Azure Active Directory Marketplace を通じて SAP Cloud Platform Identity Authentication をアプリケーションとして構成すれば、SAP アプリケーション向けに有効な認証トークンを生成するうえで必要な要求/SAML アサーションを個別に構成する必要がありません。

>[!NOTE] 
>現在、その両者で Web SSO がテストされています。 アプリ対API または API 対 API の通信に必要なフローは機能するものの、まだテストされていません。 そのテストは後のアクティビティの一環として行われる予定です。
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform Identity Authentication の追加
Azure AD への SAP Cloud Platform Identity Authentication の統合を構成するには、SAP Cloud Platform Identity Authentication をギャラリーから一連のマネージド SaaS アプリに追加する必要があります。

**ギャラリーから SAP Cloud Platform Identity Authentication を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SAP Cloud Platform Identity Authentication**」と入力して、結果パネルで **[SAP Cloud Platform Identity Authentication]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Cloud Platform Identity Authentication ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Cloud Platform Identity Authentication の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP Cloud Platform Identity Authentication で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

SAP Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP Cloud Platform Identity Authentication テスト ユーザーの作成](#create-an-sap-cloud-platform-identity-authentication-test-user)** - SAP Cloud Platform Identity Authentication で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Cloud Platform Identity Authentication アプリケーションでシングル サインオンを構成します。

**SAP Cloud Platform Identity Authentication で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. **[SAP Cloud Platform Identity Authentication のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    **[識別子]** ボックスに、`https://<entity-id>.accounts.ondemand.com` の形式で URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の識別子でこの値を更新します。 この値を取得するには、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)に問い合わせてください。 この値がわからない場合は、SAP Cloud Platform Identity Authentication ドキュメントの「[Tenant SAML 2.0 Configuration (テナントの SAML 2.0 の構成)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)」の手順に従ってください。

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    **[サインオン URL]** ボックスに、`https://<entity-id>.accounts.ondemand.com/admin` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)に問い合わせてください。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform Identity Authentication アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、SAP アプリケーションに "firstName" などの属性が必要な場合は、 [Saml トークン属性] ダイアログで、"firstName" 属性を追加します。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、属性名 "firstName" を入力します。
    
    c. **[値]** ボックスの一覧から、属性値 "user.givenname" を選択します。
    
    d. **[OK]**をクリックします。

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. **[SAP Cloud Platform Identity Authentication 構成]** セクションで、**[SAP Cloud Platform Identity Authentication の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![SAP Cloud Platform Identity Authentication 構成](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. アプリケーション用に構成された SSO を入手するために、SAP Cloud Platform Identity Authentication Administration コンソールにアクセスします。 URL は `https://<tenant-id>.accounts.ondemand.com/admin` というパターンです。 次に、SAP Cloud Platform Identity Authentication のドキュメントの手順に従って、[SAP Cloud Platform Identity Authentication で Microsoft Azure AD を会社の ID プロバイダーとして構成します](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。 

11. Azure Portal で、**[保存]** をクリックします。

12. 次の手順は、もう 1 つの SAP アプリケーションに対して SSO を追加して有効にする場合にのみ行います。 「ギャラリーからの SAP Cloud Platform Identity Authentication の追加」セクションの手順を繰り返し、SAP Cloud Platform Identity Authentication の別のインスタンスを追加します。

13. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、**[リンクされたサインオン]** をクリックします。

    ![リンクされたサインオンの構成](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. 構成を保存します。

>[!NOTE] 
>新しいアプリケーションでは、前の SAP アプリケーション用の SSO 構成が再利用されます。 SAP Cloud Platform Identity Authentication Administration コンソールで、同じ会社の ID プロバイダーを使用していることを確認してください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication テスト ユーザーの作成

SAP Cloud Platform Identity Authentication でユーザーを作成する必要はありません。 Azure AD ユーザー ストアに存在するユーザーは、SSO 機能を使用できます。

SAP Cloud Platform Identity Authentication は、ID フェデレーション オプションをサポートしています。 このオプションにより、アプリケーションは、企業の ID プロバイダーによって認証されたユーザーが SAP Cloud Platform Identity Authentication のユーザー ストアに存在するかどうかを確認できます。 

既定の設定では、ID フェデレーション オプションは無効になっています。 ID フェデレーションが有効になっていれば、SAP Cloud Platform Identity Authentication にインポートされているユーザーのみがアプリケーションにアクセスできます。 

SAP Cloud Platform Identity Authentication との ID フェデレーションを有効または無効にする方法の詳細については、[SAP Cloud Platform Identity Authentication のユーザー ストアとの ID フェデレーションの構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)に関するページで、SAP Cloud Platform Identity Authentication との ID フェデレーションの有効化に関するセクションを参照してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Cloud Platform Identity Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を SAP Cloud Platform Identity Authentication に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[SAP Cloud Platform Identity Authentication]** を選択します。

    ![アプリケーションの一覧の [SAP Cloud Platform Identity Authentication] リンク](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [SAP Cloud Platform Identity Authentication] タイルをクリックすると、SAP Cloud Platform Identity Authentication アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

