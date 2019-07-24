---
title: チュートリアル:Azure Active Directory と Shmoop For Schools の統合 | Microsoft Docs
description: Azure Active Directory と Shmoop For Schools の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4091b20e97ca76629260a7420beecb77412b0d39
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453016"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>チュートリアル:Azure Active Directory と Shmoop For Schools の統合

このチュートリアルでは、Shmoop For Schools と Azure Active Directory (Azure AD) を統合する方法について説明します。

Shmoop For Schools と Azure AD の統合には、次の利点があります。

- Shmoop For Schools にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Shmoop For Schools にサインインできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Shmoop For Schools の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Shmoop For Schools でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルで手順をテストするときの推奨事項は次のとおりです。

- 必要な場合にのみ、運用環境を使用する。
- Azure AD の試用環境がない場合は、[1 か月の無料試用版](https://azure.microsoft.com/pricing/free-trial/)を入手します。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Shmoop For Schools の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-shmoop-for-schools-from-the-gallery"></a>ギャラリーからの Shmoop For Schools の追加
Azure AD への Shmoop For Schools の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Shmoop For Schools を追加する必要があります。

**ギャラリーから Shmoop For Schools を追加するには、次の手順に従います。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**Shmoop For Schools**」と入力します。 結果から **[Shmoop For Schools]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Shmoop For Schools](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Shmoop For Schools で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Shmoop For Schools ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Shmoop For Schools の関連ユーザーの間で、リンクが確立されている必要があります。

Shmoop For Schools で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を設定します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [Shmoop For Schools テスト ユーザーの作成](#create-a-shmoop-for-schools-test-user) - Shmoop For Schools で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Shmoop For Schools アプリケーションでシングル サインオンを構成します。

**Shmoop For Schools で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Shmoop For Schools** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログ ボックスの **[シングル サインオン モード]** のドロップダウン メニューで、**[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. **[Shmoop For Schools のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. **[サインオン URL]** ボックスに、`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>` というパターンで URL を入力します。

    b. **[識別子]** ボックスに、`https://schools.shmoop.com/<uniqueid>` というパターンで URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Shmoop For Schools クライアント サポート チーム](mailto:support@shmoop.com)に問い合わせてください。 
 
4. Shmoop For Schools アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットは、これらのアサーションの構成方法を示しています。

    ![Configure single sign-on](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop For Schools では、**[教師]** と **[学生]** の 2 つのユーザー ロールがサポートされます。 ユーザーに適切なロールを割り当てられるように、Azure AD でそのロールを設定します。 Azure AD でロールを構成する方法については、「[RBAC と Azure Portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
5. **[シングル サインオン]** ダイアログ ボックスの **[ユーザー属性]** セクションで、前の画像で示されているように、SAML トークン属性を構成します。  その後、次の手順を実行します。

    | 属性名 | 属性名 |
    | -------------- | --------------- |
    | role           | user.assignedroles |

    a. **[属性の追加]** ダイアログ ボックスを開くには、**[属性の追加]** を選択します。
    
    ![Configure single sign-on](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される属性値を選択します。

    d. **[名前空間]** ボックスは空白のままにします。
    
    e. **[OK]** を選びます。

6. **[保存]** を選択します。

    ![Configure single sign-on](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7.  **[SAML 署名証明書]**  セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. **Shmoop For Schools** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Shmoop For Schools サポート チーム](mailto:support@shmoop.com)に送信する必要があります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動します。 次に、**[すべてのユーザー]** を選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]** を選択します。
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Shmoop For Schools テスト ユーザーの作成

このセクションの目的は、Shmoop For Schools で Britta Simon というユーザーを作成することです。 Shmoop For Schools では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 新しいユーザーがまだ存在しない場合、そのユーザーは、Shmoop For Schools にアクセスしようしたときに自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Shmoop For Schools サポート チーム](mailto:support@shmoop.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Shmoop For Schools へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Shmoop For Schools に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルで、アプリケーション ビューを開きます。 次に、ディレクトリ ビューで **[エンタープライズ アプリケーション]** に移動します。  次に、**[すべてのアプリケーション]** を選びます。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Shmoop For Schools]** を選択します。

    ![アプリケーションの一覧の Shmoop For Schools のリンク](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。 

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[Shmoop For Schools]** タイルを選択すると、自動的に Shmoop For Schools アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法のチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

