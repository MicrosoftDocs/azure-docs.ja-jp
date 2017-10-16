---
title: "チュートリアル: Azure Active Directory と SAP Business Object Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Business Object Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>チュートリアル: Azure Active Directory と SAP Business Object Cloud の統合

このチュートリアルでは、SAP Business Object Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Business Object Cloud と Azure AD の統合には、次の利点があります。

- Azure AD で、SAP Business Object Cloud にアクセスするユーザーを制御できます。
- シングル サインオンとユーザーの Azure AD アカウントを使用して、ユーザーを SAP Business Object Cloud に自動的にサインインさせることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Business Object Cloud の統合を設定するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効になっている SAP Business Object Cloud

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境ではテストしないことをお勧めします。

このチュートリアルの手順をテストする際の推奨事項:

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SAP Business Object Cloud を追加する。
2. Azure AD シングル サインオンを設定してテストする。

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>ギャラリーから SAP Business Object Cloud を追加する。
Azure AD への SAP Business Object Cloud の統合を設定するには、ギャラリーで、管理対象 SaaS アプリの一覧に SAP Business Object Cloud を追加します。

ギャラリーから SAP Business Object Cloud を追加するには:

1. [Azure Portal](https://portal.azure.com) の左側のメニューで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ページ][2]
    
3. 新しいアプリケーションを追加するには、**[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**SAP Business Object Cloud**」と入力します。

    ![検索ボックス](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. 結果ウィンドウで **[SAP Business Object Cloud]** を選択し、**[追加]** を選択します。

    ![結果リストの SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンを設定してテストする

このセクションでは、*Britta Simon* という名前のテスト ユーザーに基づいて、SAP Business Object Cloud で Azure AD シングル サインオンを設定してテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD ユーザーに対応する SAP Business Object Cloud ユーザーを認識する必要があります。 Azure AD ユーザーと SAP Business Object Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

リンク関係を確立するには、SAP Business Object Cloud で、**[Username]\(ユーザー名\)** に Azure AD の **[ユーザー名]** の値を割り当てます。

SAP Business Object Cloud で Azure AD シングル サインオンを構成してテストするには、次のタスクを完了します。

1. [Azure AD シングル サインオンを設定する](#set-up-azure-ad-single-sign-on)。 この機能を使用するユーザーを設定します。
2. [Azure AD のテスト ユーザーを作成する](#create-an-azure-ad-test-user)。 ユーザー Britta Simon を使用して Azure AD シングル サインオンをテストします。
3. [SAP Business Object Cloud のテスト ユーザーを作成する](#create-an-sap-business-object-cloud-test-user)。 SAP Business Object Cloud で、Azure AD の Britta Simon に対応し、そのユーザーにリンクされているユーザーを作成します。
4. [Azure AD のテスト ユーザーを割り当てる](#assign-the-azure-ad-test-user)。 Azure AD シングル サインオンを使用するよう Britta Simon を設定します。
5. [シングル サインオンをテストする](#test-single-sign-on)。 構成が機能することを確認します。

### <a name="set-up-azure-ad-single-sign-on"></a>Azure AD シングル サインオンを設定する

このセクションでは、Azure ポータルで Azure AD シングル サインオンを有効にンします。 その後、SAP Business Object Cloud アプリケーションでシングル サインオンを設定します。

SAP Business Object Cloud アプリケーションで Azure AD シングル サインオンを設定するには:

1. Azure ポータルの **SAP Business Object Cloud** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択][4]

2. **[シングル サインオン]** ページで、**[モード]** として **[SAML ベースのサインオン]** を選択します。
 
    ![[SAML ベースのサインオン] の選択](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. **[SAP Business Object Cloud のドメインと URL]** で、次の手順を完了します。

    1. **[サインオン URL]** ボックスに、次のパターンで URL を入力します。 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![[SAP Business Object Cloud のドメインと URL] ページの URL](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > これらの URL の値は、単なる例です。 実際のサインオン URL と識別子 URLでこれらの値を更新してください。 サインオン URL を取得するには、[SAP Business Object Cloud クライアント サポート チーム](https://www.sap.com/product/analytics/cloud-analytics.support.html)に問い合わせてください。 識別子 URL は、管理コンソールから SAP Business Object Cloud のメタデータをダウンロードすることで取得できます。 これについては、このチュートリアルの後半で説明します。 

4. **[SAML 署名証明書]** で、**[メタデータ XML]** を選択します。 次に、コンピューターにメタデータ ファイルを保存します。

    ![メタデータ XML の選択](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. [ **保存**] を選択します。

    ![[保存] の選択](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Web ブラウザーの別のウィンドウで、SAP Business Object Cloud 企業サイトに管理者としてサインインします。

7. **[Menu]\(メニュー\)** > **[System]\(システム\)** > **[Administration]\(管理\)** を選択します。
    
    ![[Menu]\(メニュー\)、[System]\(システム\)、および [Administration]\(管理\) の選択](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. **[Security]\(セキュリティ\)** タブで **[Edit]\(編集\)** (ペン) アイコンを選択します。
    
    ![[Security]\(セキュリティ\) タブで [Edit]\(編集\)アイコンを選択](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. **[Authentication Method]\(認証方法\)** として **[SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\)** を選択します。

    ![認証方法として [SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\) を選択](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. サービス プロバイダーのメタデータをダウンロードする (手順 1) には、**[Download]\(ダウンロード\)** を選択します。 メタデータ ファイルで、**entityID** を見つけてその値をコピーします。 Azure ポータルで、**[SAP Business Object Cloud のドメインと URL]** の **[識別子]** ボックスに値を貼り付けます。

    ![EntityID 値をコピーして貼り付ける](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Azure ポータルからダウンロードしたファイル内のサービス プロバイダーのメタデータをアップロードする (手順 2) には、**[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\)** で、**[アップロード]** を選択します。  

    ![[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\) で [アップロード] を選択](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. **[User Attribute]\(ユーザー属性\)** の一覧で、実装で使用するユーザー属性を選択します (手順 3)。 このユーザー属性が ID プロバイダーにマップされます。 ユーザーのページでカスタム属性を入力するには、**[Custom SAML Mapping]\(カスタム SAML マッピング\)** オプションを使用します。 または、ユーザー属性として **[Email]\(電子メール\)** または **[USER ID]\(ユーザー ID\)** のいずれかを選択できます。 この例では、Azure ポータルで **[ユーザー属性]** セクションの **userprincipalname** 属性にユーザー識別子要求をマップしたため、**[Email]\(電子メール\)** を選択しています。 これにより、一意のユーザーの電子メールが用意され、SAML 応答が成功するたびに SAP Business Object Cloud アプリケーションに送信されます。

    ![ユーザー属性の選択](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. アカウントを ID プロバイダーで確認する (ステップ 4) には、**[Login Credential (Email)]\(ログイン資格情報 (電子メール)\)** ボックスに、ユーザーの電子メール アドレスを入力します。 次に、**[Verify Account]\(アカウントの確認\)** を選択します。 システムがサインイン資格情報をユーザー アカウントに追加します。

    ![電子メール アドレスを入力し、[Verify Account]\(アカウントの確認\) を選択](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. **[Save]\(保存\)**  アイコンを選択します。

    ![[Save]\(保存\) アイコン](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> アプリのセットアップ中に、[Azure ポータル](https://portal.azure.com)で上記の手順の簡易版を確認できます。 **[Active Directory]** > **[エンタープライズ アプリケーション]** を選択してこのアプリを追加した後、**[シングル サインオン]** タブを選択します。ページ下部の **[構成]** セクションで、組み込みのドキュメントにアクセスできます。 詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

Azure AD でテスト ユーザーを作成するには:

1. Azure Portal の左側のメニューで、**[Azure Active Directory]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** を選択し、**[すべてのユーザー]** を選択します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログ ボックスを開くには、**[追加]** を選択します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順を完了します。
 
    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    2. **[ユーザー名]** ボックスに、ユーザー Britta Simon の電子メール アドレスを入力します。

    3. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    4. **[作成]**を選択します。

        ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Azure AD ユーザーの作成][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>SAP Business Object Cloud のテスト ユーザーの作成

Azure AD ユーザーが SAP Business Object Cloud にサインインできるようにするには、そのユーザーを SAP Business Object Cloud にプロビジョニングしておく必要があります。 SAP Business Object Cloud では、プロビジョニングは手動のタスクです。

ユーザー アカウントをプロビジョニングするには:

1. SAP Business Object Cloud 企業サイトに管理者としてサインインします。

2. **[Menu]\(メニュー\)** > **[Security]\(セキュリティ\)** > **[Users]\(ユーザー\)** を選択します。

    ![従業員の追加](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. **[Users]\(ユーザー\)** ページで、新しいユーザーの詳細を追加するには、**+** を選択します。 

    ![[Add Users]\(ユーザーの追加\) ページ](./media/active-directory-saas-sapboc-tutorial/user4.png)

    その後、次の手順を完了します。

    1. **[USER ID]\(ユーザー ID\)** ボックスに、ユーザー ID を入力します (この例では **Britta**)。

    2. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    3. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    4. **[DISPLAY NAME]\(表示名\)** ボックスに、ユーザーのフル ネームを入力します (この例では **Britta Simon**)。

    5. **[E-MAIL]\(電子メール\)** ボックスに、ユーザーの電子メール アドレスを入力します (この例では **brittasimon@contoso.com**)。

    6. **[Select Roles]\(ロールの選択\)** ページで、ユーザーの適切なロールを選択し、**[OK]** を選択します。

      ![Select role](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. **[Save]\(保存\)**  アイコンを選択します。    


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Business Object Cloud へのユーザー アカウントによるアクセス権を付与することで、このユーザーが Azure AD シングル サインオンを使用できるようにします。

Britta Simon を SAP Business Object Cloud に割り当てるには:

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。 **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP Business Object Cloud]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択][202] 

4. **[追加]**を選択します。 **[割り当ての追加]** ページで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ページ][203]

5. **[ユーザーとグループ]** ページで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ページで **[選択]** を選択します。

7. **[割り当ての追加]** ページで **[割り当て]** を選択します。

![ユーザー ロールを割り当てる][200] 
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Business Object Cloud] タイルを選択すると、自動的に SAP Business Object Cloud アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

