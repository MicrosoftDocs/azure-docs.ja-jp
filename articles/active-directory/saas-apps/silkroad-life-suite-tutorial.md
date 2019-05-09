---
title: チュートリアル:Azure Active Directory と SilkRoad Life Suite の統合 | Microsoft Docs
description: Azure Active Directory と SilkRoad Life Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7f047d784796b9e7c59d95863aa74b3f72da324
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995635"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>チュートリアル:Azure Active Directory と SilkRoad Life Suite の統合

このチュートリアルでは、SilkRoad Life Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。

SilkRoad Life Suite と Azure AD の統合には、次の利点があります。

- SilkRoad Life Suite にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SilkRoad Life Suite にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SilkRoad Life Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SilkRoad Life Suite でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SilkRoad Life Suite の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>ギャラリーからの SilkRoad Life Suite の追加
Azure AD への SilkRoad Life Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SilkRoad Life Suite を追加する必要があります。

**ギャラリーから SilkRoad Life Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SilkRoad Life Suite**」と入力し、結果パネルで **[SilkRoad Life Suite]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SilkRoad Life Suite](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SilkRoad Life Suite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SilkRoad Life Suite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SilkRoad Life Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

SilkRoad Life Suite で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

SilkRoad Life Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SilkRoad Life Suite テスト ユーザーの作成](#create-a-silkroad-life-suite-test-user)** - SilkRoad Life Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SilkRoad Life Suite アプリケーションでシングル サインオンを構成します。

**SilkRoad Life Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SilkRoad Life Suite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

1. **[SilkRoad Life Suite のドメインと URL]** セクションで、次の手順を実行します。

    ![[SilkRoad Life Suite のドメインと URL] のシングル サインオン情報](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.silkroad-eng.com/Authentication/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 
    
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SilkRoad Life Suite クライアント サポート チーム](https://www.silkroad.com/locations/)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
1. **[SilkRoad Life Suite の構成]** セクションで、**[SilkRoad Life Suite の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![SilkRoad Life Suite の構成](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

1. SilkRoad 企業サイトに管理者としてサインオンします。 
 
    >[!NOTE] 
    > Microsoft Azure AD とフェデレーションを構成するために SilkRoad Life Suite の認証アプリケーションへのアクセス権を取得するには、SilkRoad サポートまたは SilkRoad サービス担当者にお問い合わせください。

1. **[サービス プロバイダー]** に移動して、**[フェデレーションの詳細]** をクリックします。 
   
    ![Azure AD Single Sign-On][10]

1. **[Download Federation Metadata (フェデレーション メタデータのダウンロード)]** をクリックし、メタデータ ファイルをコンピューターに保存します。
   
    ![Azure AD Single Sign-On][11] 

1. **SilkRoad** アプリケーションで、**[Authentication Sources (認証ソース)]** をクリックします。
   
    ![Azure AD Single Sign-On][12] 

1. **[Add Authentication Source (認証ソースの追加)]** をクリックします。 
   
    ![Azure AD Single Sign-On][13] 

1. **[Add Authentication Source (認証ソースの追加)]** セクションで、次の手順に従います。 
   
    ![Azure AD Single Sign-On][14]
  
    a. **[Option 2 - Metadata File]\(オプション 2 - メタデータ ファイル\)** の下の **[参照]** をクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
  
    b. **[Create Identity Provider using File Data]** をクリックします。

1. **[Authentication Sources (認証ソース)]** セクションで、**[編集]** をクリックします。 
    
     ![Azure AD Single Sign-On][15] 

1. **[Edit Authentication Source (認証ソースの編集)]** ダイアログ ボックスで、次の手順を実行します。 
    
     ![Azure AD Single Sign-On][16] 

    a. **[Enabled]** で **[Yes]** を選択します。

    b. **[Entity Id]\(エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
   
    c. **[IdP Description]\(IdP の説明\)** ボックスに、構成の説明を入力します (例:*Azure AD の SSO*)。

    d. **[メタデータ ファイル]** ボックスに、Azure Portal からダウンロードした**メタデータ** ファイルをアップロードします。
  
    e. **[IdP Name]\(IdP 名\)** ボックスに、構成の固有の名前を入力します (例:*Azure SP*)。
  
    f. **[Logout Service URL]\(ログアウト サービス URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    g. **[sign-on service URL]\(サインオン サービス URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    h. **[Save]** をクリックします。

1. その他のすべての認証のソースを無効にします。 
    
     ![Azure AD Single Sign-On][17]

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-silkroad-life-suite-test-user"></a>SilkRoad Life Suite テスト ユーザーの作成

このセクションでは、SilkRoad Life Suite で Britta Simon というユーザーを作成します。 SilkRoad Life Suite プラットフォームにユーザーを追加するには、 [SilkRoad Life Suite Client サポート チーム](https://www.silkroad.com/locations/) に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SilkRoad Life Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SilkRoad Life Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SilkRoad Life Suite]** を選択します。

    ![アプリケーションの一覧の SilkRoad Life Suite のリンク](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで SilkRoad Life Suite のタイルをクリックすると、自動的に SilkRoad Life Suite アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
