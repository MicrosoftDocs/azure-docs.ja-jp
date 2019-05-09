---
title: チュートリアル:Azure Active Directory と ServiceChannel の統合 | Microsoft Docs
description: Azure Active Directory と ServiceChannel の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4be5087af70e10e5a73ea2a183a25b326aea664
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433469"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>チュートリアル:Azure Active Directory と ServiceChannel の統合

このチュートリアルでは、ServiceChannel と Azure Active Directory (Azure AD) を統合する方法について説明します。

ServiceChannel と Azure AD の統合には、次の利点があります。

- ServiceChannel にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に ServiceChannel にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD と ServiceChannel の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- ServiceChannel でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ServiceChannel の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-servicechannel-from-the-gallery"></a>ギャラリーからの ServiceChannel の追加
Azure AD への ServiceChannel の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceChannel を追加する必要があります。

**ギャラリーから ServiceChannel を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**ServiceChannel**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. 結果パネルで、**[ServiceChannel]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ServiceChannel で Azure AD シングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が、Azure AD のユーザーに対応する ServiceChannel のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと ServiceChannel の関連するユーザーの間のリンク関係が確立されている必要があります。

このリンク関係は、Azure AD での **[ユーザー名]** の値を ServiceChannel での **[ユーザー名]** の値として割り当てることによって確立されます。

ServiceChannel で Azure AD シングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[ServiceChannel テスト ユーザーの作成](#creating-a-servicechannel-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD シングル サインオンを有効にし、ServiceChannel アプリケーションでシングル サインオンを構成します。

**ServiceChannel で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure 管理ポータルの **ServiceChannel** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. **[ServiceChannel Domain and URLs] \(ServiceChannel のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. **[識別子]** ボックスに、値として「`http://adfs.<domain>.com/adfs/service/trust`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<customer domain>.servicechannel.com/saml/acs` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 これらの値は、実際の識別子と応答 URL で更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、[ServiceChannel サポート チーム](https://servicechannel.zendesk.com/hc/en-us)に問い合わせてください。

1. ServiceChannel アプリケーションは特定の形式の SAML アサーションを予測しているため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットはその例です。 **NameIdentifier(ユーザー識別子)** が唯一の必須要求であり、既定値は **user.userprincipalname** ですが、ServiceChannel はこれが **user.mail** にマッピングされることを予測しています。 ジャスト イン タイムのユーザー プロビジョニングを有効にする予定がある場合は、下に示すように、次の要求を追加する必要があります。 **[役割]** 要求を、ユーザーの役割を含む **user.assignedroles** にマッピングする必要があります。  

    要求に関する詳細なガイダンスについては、ServiceChannel ガイドの[ここ](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example)を参照してください。
    
    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Azure AD で**役割**を構成する方法については、「[RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

1. **[ユーザー属性]** セクションで、**[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を設定します。

    | 属性名 | 属性値 |
    | --- | --- |    
    | Role| user.assignedroles |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。
    
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. **[Save]** をクリックします。

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial_general_400.png)

1. **[ServiceChannel Configuration] \(ServiceChannel 構成)** セクションで、**[Configure ServiceChannel] \(ServiceChannel の構成)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションの **SAML エンティティ ID** をメモしてください。

1. **ServiceChannel** 側でシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)** と **SAML エンティティ ID** を [ServiceChannel サポート チーム](https://servicechannel.zendesk.com/hc/en-us)に送信する必要があります。 両方の側で SAML SSO 接続を正しく設定するためにサポート チームがこれを設定します。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。 

### <a name="creating-a-servicechannel-test-user"></a>ServiceChannel テスト ユーザーの作成

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 完全なユーザー プロビジョニングについては、[ServiceChannel サポート チーム](https://servicechannel.zendesk.com/hc/en-us)に問い合わせてください

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ServiceChannel へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を ServiceChannel に割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[ServiceChannel]** を選択します。

    ![Configure single sign-on](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [ServiceChannel] タイルをクリックすると、自動的に ServiceChannel アプリケーションにサイン オンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png
