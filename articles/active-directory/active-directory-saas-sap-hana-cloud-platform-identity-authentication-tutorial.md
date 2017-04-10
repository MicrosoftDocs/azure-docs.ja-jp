---
title: "チュートリアル: Azure Active Directory と SAP HANA Cloud Platform Identity Authentication の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP HANA Cloud Platform Identity Authentication の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 87c3b66a8789254e962ccfd7fc1eb7842c0db638
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>チュートリアル: Azure Active Directory と SAP HANA Cloud Platform Identity Authentication の統合

このチュートリアルでは、SAP HANA Cloud Platform Identity Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP HANA Cloud Platform Identity Authentication は、Azure AD をメイン IdP として使用して SAP アプリケーションにアクセスするためのプロキシ IdP として使用されます。

SAP HANA Cloud Platform Identity Authentication を Azure AD と統合すると、次の利点があります。

- SAP アプリケーションにアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで SAP アプリケーションに自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

SAP HANA Cloud Platform Identity Authentication と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- **SAP HANA Cloud Platform Identity Authentication** でのシングル サインオンが有効なサブスクリプション


>[!NOTE] 
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。



## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの SAP HANA Cloud Platform Identity Authentication の追加
2. Azure AD シングル サインオンの構成とテスト

技術的な詳細について掘り下げる前に、確認する概念について理解する必要があります。 SAP HANA Cloud Platform Identity Authentication と Azure Active Directory のフェデレーションにより、SAP HANA Cloud Platform Identity Authentication で保護された SAP アプリケーションおよびサービスに対し、(IdP としての) AAD によって保護されたアプリケーションまたはサービス全体でシングル サインオンを実装できます。

現時点では、SAP HANA Cloud Platform Identity Authentication は、SAP アプリケーションへのプロキシ ID プロバイダーとして機能します。 さらに Azure Active Directory は、このセットアップにおける主要な ID プロバイダーとして機能します。 それを次の図に示します。    

![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/architecture-01.png)

このセットアップにより、SAP HANA Cloud Platform Identity Authentication テナントは、Azure Active Directory で信頼されたアプリケーションとして構成されます。 

この方法で保護するすべての SAP アプリケーションおよびサービスは、その後で SAP HANA Cloud Platform Identity Authentication 管理コンソールで構成されます。

これを理解することが非常に重要です。 つまり、SAP アプリケーションおよびサービスへのアクセスの許可のための承認は、このようなセットアップ向けに、SAP HANA Cloud Platform Identity Authentication で行われる必要があります (Azure Active Directory で承認を構成するのではありません)。

Azure Active Directory Marketplace を通じて SAP HANA Cloud Platform Identity Authentication をアプリケーションとして構成すれば、SAP アプリケーション向けに有効な認証トークンを生成するうえで必要な要求/SAML アサーションを個別に構成する必要がありません。

>[!NOTE] 
>現在、その両者で Web SSO がテストされています。 アプリ対API または API 対 API の通信に必要なフローは機能するものの、まだテストされていません。 そのテストは後のアクティビティの一環として行われる予定です。 検証が正常に済んだら、それに応じてドキュメントが更新されます。

## <a name="adding-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP HANA Cloud Platform Identity Authentication の追加
Azure AD への SAP HANA Cloud Platform Identity Authentication の統合を構成するには、SAP HANA Cloud Platform Identity Authentication をギャラリーから一連のマネージド SaaS アプリに追加する必要があります。

**ギャラリーから SAP HANA Cloud Platform Identity Authentication を追加するには、次の手順に従います。**

1. [**Azure 管理ポータル**](https://portal.azure.com)の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**SAP HANA Cloud Platform Identity Authentication**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_01.png)

5. 結果パネルで **[SAP HANA Cloud Platform Identity Authentication]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP HANA Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP HANA Cloud Platform Identity Authentication ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP HANA Cloud Platform Identity Authentication の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、SAP HANA Cloud Platform Identity Authentication の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

SAP HANA Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP HANA Cloud Platform Identity Authentication のテスト ユーザーの作成](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)** - SAP HANA Cloud Platform Identity Authentication で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、SAP HANA Cloud Platform Identity Authentication アプリケーションでシングル サインオンを構成します。

SAP HANA Cloud Platform Identity Authentication アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 この属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。

![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_03.png)

**SAP HANA Cloud Platform Identity Authentication で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure 管理ポータルの **SAP HANA Cloud Platform Identity Authentication** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On][5]

3. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、SAP アプリケーションに "firstName" などの属性が必要な場合は、 [Saml トークン属性] ダイアログで、"firstName" 属性を追加します。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]][6]

    ![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_05.png)
    
    b. **[属性名]** ボックスに、属性名 "firstName" を入力します。

    c. **[属性値]** リストから、属性値 "user.givenname" を選択します。
    
    d. **[OK]** をクリックします。


4. **[SAP HANA Cloud Platform Identity Authentication のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_06.png)

    a. **[サインオン URL]** ボックスに、SAP アプリケーションのサインオン URL を入力します。

    b. **[識別子]** ボックスに、`<entity-id>.accounts.ondemand.com` というパターンの値を入力します。 この値がわからない場合は、SAP HANA Cloud Platform Identity Authentication ドキュメントの「[Tenant SAML 2.0 Configuration (テナントの SAML 2.0 の構成)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)」の手順に従ってください。


5. **[SAP HANA Cloud Platform Identity Authentication 構成]** セクションで、**[SAP HANA Cloud Platform Identity Authentication の構成]** をクリックして **[サインオンの構成]** ダイアログを開きます。 次に、**[SAML XML Metadata (SAML XML メタデータ)]** をクリックし、ファイルをコンピューターに保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_08.png)


6. アプリケーション用に構成された SSO を入手するために、SAP HANA Cloud Platform Identity Authentication Administration コンソールにアクセスします。 URL は `https://<tenant-id>.accounts.ondemand.com/admin` というパターンです。 次に、SAP HANA Cloud Platform Identity Authentication のドキュメントの手順に従って、[SAP HANA Cloud Platform Identity Authentication で Microsoft Azure AD を会社の ID プロバイダーとして構成します](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。 

7. Azure 管理ポータルで、**[保存]** ボタンをクリックします。
8. 次の手順は、もう&1; つの SAP アプリケーションに対して SSO を追加して有効にする場合にのみ行います。 「ギャラリーからの SAP HANA Cloud Platform Identity Authentication の追加」セクションの手順を繰り返し、SAP HANA Cloud Platform Identity Authentication の別のインスタンスを追加します。
9. Microsoft Azure 管理ポータルの **SAP HANA Cloud Platform Identity Authentication** アプリケーション統合ページで、**[リンクされたサインオン]** をクリックします。

     ![リンクされたサインオンの構成](./media/active-directory-saas-sap-hana-cloud-tutorial/linked_sign_on.png)
10. 次に、構成を保存します。

>[!NOTE] 
>新しいアプリケーションでは、前の SAP アプリケーション用の SSO 構成が再利用されます。 SAP HANA Cloud Platform Identity Authentication Administration コンソールで、同じ会社の ID プロバイダーを使用していることを確認してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、新しいポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>SAP HANA Cloud Platform Identity Authentication テスト ユーザーの作成

SAP HANA Cloud Platform Identity Authentication でユーザーを作成する必要はありません。 Azure AD ユーザー ストアに存在するユーザーは、シングル サインオン (SSO) 機能を使用できます。

SAP HANA Cloud Platform Identity Authentication は、ID フェデレーション オプションをサポートしています。 このオプションにより、アプリケーションは、企業の ID プロバイダーによって認証されたユーザーが SAP HANA Cloud Platform Identity Authentication のユーザー ストアに存在するかどうかを確認できます。 既定の設定では、ID フェデレーション オプションは無効になっています。 ID フェデレーションが有効になっていれば、SAP HANA Cloud Platform Identity Authentication にインポートされているユーザーのみがアプリケーションにアクセスできます。 

SAP HANA Cloud Platform Identity Authentication との ID フェデレーションを有効または無効にする方法の詳細については、[SAP HANA Cloud Platform Identity Authentication のユーザー ストアとの ID フェデレーションの構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)に関するページで、SAP HANA Cloud Platform Identity Authentication との ID フェデレーションの有効化に関するセクションを参照してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP HANA Cloud Platform Identity Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を SAP HANA Cloud Platform Identity Authentication に割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[SAP HANA Cloud Platform Identity Authentication]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_09.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [SAP HANA Cloud Platform Identity Authentication] タイルをクリックすると、SAP HANA Cloud Platform Identity Authentication アプリケーションに自動的にサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_203.png
