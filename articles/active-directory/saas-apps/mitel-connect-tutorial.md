---
title: チュートリアル:Azure Active Directory と Mitel Connect の統合 | Microsoft Docs
description: Azure Active Directory と Mitel Connect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015181"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>チュートリアル:Azure Active Directory と Mitel MiCloud Connect または CloudLink Platform の統合

このチュートリアルでは、Mitel Connect アプリを使用して、Azure Active Directory (Azure AD) を Mitel MiCloud Connect または CloudLink Platform と統合する方法について説明します。 Mitel Connect アプリは Azure ギャラリーで入手できます。 MiCloud Connect または CloudLink Platform と Azure AD の統合には、次の利点があります。

* Azure AD で、エンタープライズ資格情報を使用して、MiCloud Connect アプリまたは CloudLink アプリにアクセスできるユーザーを制御できます。
* お使いのアカウントで、ユーザーが自身の Azure AD アカウントを使用して MiCloud Connect または CloudLink に自動的にサインイン (シングル サインオン) するように設定できます。

SaaS アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオン](../manage-apps/what-is-single-sign-on.md)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してから、Azure AD と Mitel MiCloud Connect または CloudLink Platform の統合を開始してください。

## <a name="prerequisites"></a>前提条件

MiCloud Connect と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* 構成するアプリケーションに応じて、Mitel MiCloud Connect アカウントまたは Mitel CloudLink アカウント。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Azure AD のシングル サインオン (SSO) を構成してテストします。

* Mitel Connect では、**SP** によって開始される SSO がサポートされます
* Mitel Connect を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-mitel-connect-from-the-gallery"></a>ギャラリーからの Mitel Connect の追加

Azure AD への Mitel Connect の統合を構成するには、Azure portal 内のマネージド SaaS アプリの一覧に、ギャラリーから Mitel Connect を追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索フィールドに「**Mitel Connect**」と入力し、結果のパネルから **[Mitel Connect]** を選択して、 **[追加]** を選択します。

     ![結果リストの Mitel Connect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**_Britta Simon_** というテスト ユーザーに基づいて、MiCloud Connect または CloudLink Platform に対する Azure AD SSO を構成およびテストします。 シングル サインオンが機能するためには、Azure AD ポータルのユーザーと Mitel プラットフォームの対応するユーザーの間にリンクが確立されている必要があります。 MiCloud Connect または CloudLink Platform に対する Azure AD SSO を構成してテストする方法については、以下のセクションを参照してください。
* MiCloud Connect に対する Azure AD SSO の構成とテスト
* CloudLink Platform に対する Azure AD SSO の構成とテスト

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>MiCloud Connect に対する Azure AD SSO の構成とテスト

MiCloud Connect に対する Azure AD シングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD を使った SSO のための MiCloud Connect の構成](#configure-micloud-connect-for-sso-with-azure-ad)** - ユーザーがこの機能を使用できるようにして、アプリケーション側で SSO 設定を構成します。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
4. **[Mitel MiCloud Connect のテスト ユーザーの作成](#create-a-mitel-micloud-connect-test-user)** - MiCloud Connect アカウントで Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Azure AD を使った SSO のための MiCloud Connect の構成

このセクションでは、Azure portal 内で MiCloud Connect について Azure AD によるシングル サインオンを有効にすると共に、MiCloud Connect アカウントに Azure AD を使用した SSO を許可するよう構成します。

MiCloud Connect に Azure AD による SSO を構成する際は、Azure portal と Mitel アカウント ポータルを並べて開いておくのが最も簡単です。 Azure portal と Mitel アカウント ポータルの間で、いくつかの情報を互いにコピーする必要があります。


1. [Azure portal](https://portal.azure.com/) で構成ページを開くには、次のようにします。

    1. **Mitel Connect** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

       ![シングル サインオン構成のリンク](common/select-sso.png)

    1. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML]** を選択します。
    
       ![シングル サインオン選択モード](common/select-saml-option.png)
    
       SAML ベースのサインオン ページが表示されます。

2. Mitel アカウント ポータルで構成ダイアログ ボックスを開くには、次の手順を実行します。

    1. **[Phone System]\(電話システム\)** メニューで、 **[Add-On Features]\(アドオン機能\)** を選択します。

    1. **[Single Sign-On]\(シングル サインオン\)** の右側で、 **[Activate]\(アクティブ化\)** または **[Settings]\(設定\)** を選択します。
    
    [Connect Single Sign-On Settings]\(Connect シングル サインオン設定\) ダイアログ ボックスが開きます。
    
3. **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** チェック ボックスをオンにします。
    
    ![Mitel の [Connect Single Sign-On Settings]\(Connect シングル サインオン設定\) ページのスクリーンショット。[Enable Single Sign-On]\(シングル サインオンを有効にする\) チェック ボックスがオンになっています。](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Azure portal で、 **[基本的な SAML 構成]** セクションの **[編集]** アイコンを選択します。
   
    ![[SAML でのシングル サインオンの設定] ページを示すスクリーンショット。[編集] アイコンが選択されています。](common/edit-urls.png)

    [基本的な SAML 構成] ダイアログ ボックスが開きます。

5.  Mitel アカウント ポータル内の **[Mitel Identifier (Entity ID)]\(Mitel 識別子 (エンティティ ID)\)** フィールドから URL をコピーし、Azure portal の **[識別子 (エンティティ ID)]** フィールドに貼り付けます。

6. Mitel アカウント ポータル内の **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (Assertion Consumer Service URL)\)** フィールドから URL をコピーし、Azure portal の **[応答 URL (Assertion Consumer Service URL)]** フィールドに貼り付けます。

   ![Azure portal の [基本的な SAML 構成] および Mitel アカウント ポータルの [ID プロバイダーの設定] セクションを示すスクリーンショット。それらの関係が線で示されています。](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。

    1. **https://portal.shoretelsky.com** - 既定の Mitel アプリケーションとして Mitel アカウント ポータルを使用する場合
    1. **https://teamwork.shoretel.com** - 既定の Mitel アプリケーションとして Teamwork を使用する場合

    > [!NOTE]
    > 既定の Mitel アプリケーションとは、ユーザーがアクセス パネルの [Mitel Connect] タイルを選択したときにアクセスできるアプリケーションです。 また、Azure AD からテスト セットアップを実行した場合にアクセスされるアプリケーションでもあります。

8. Azure portal 内の **[基本的な SAML 構成]** ダイアログ ボックスで、 **[保存]** を選択します。

9. Azure portal 内の **[SAML ベースのサインオン]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択して **署名証明書** をダウンロードし、コンピューターに保存します。

    ![[SAML 署名証明書] ウィンドウを示すスクリーンショット。ここでは、証明書をダウンロードできます。](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. テキスト エディターで署名証明書ファイルを開き、ファイル内のデータをすべてコピーして、Mitel アカウント ポータルの **[Signing Certificate]\(署名証明書\)** フィールドに貼り付けます。 

      ![[署名証明書] フィールドを示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Azure portal の **[SAML ベースのサインオン]** ページの **[Setup Mitel Connect]\(Mitel Connect の設定\)** セクションで、次の手順を実行します。

     1. **[ログイン URL]** フィールドから URL をコピーして、Mitel アカウント ポータルの **[Sign-in URL]\(サインイン URL\)** フィールドに貼り付けます。

     1. **[Azure AD 識別子]** フィールドから URL をコピーして、Mitel アカウント ポータルの **[Entity ID]\(エンティティ ID\)** フィールドに貼り付けます。
         
         ![Azure portal と Mitel アカウント ポータルの [SAML ベースのサインオン] ページ間の関係を示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Mitel アカウント ポータル内の **[Connect Single Sign-On Settings]\(Connect シングル サインオンの設定\)** ダイアログ ボックスで、 **[Save]\(保存\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. ユーザーのプロパティのダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** フィールドに「brittasimon@\<yourcompanydomain\>.\<extension\>」と入力します。  たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    1. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mitel Connect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Mitel Connect]** を選択します。

    ![アプリケーションの一覧の Mitel Connect のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** を選択します。

6. SAML アサーション内に任意のロール値が必要な場合、ユーザーに適したロールを **[ロールの選択]** ダイアログ ボックスの一覧から選択し、画面の下部にある **[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel MiCloud Connect のテスト ユーザーの作成

このセクションでは、お使いの MiCloud Connect アカウントで Britta Simon というユーザーを作成します。 シングル サインオンを使用する前に、ユーザーを作成し、アクティブにする必要があります。

Mitel アカウント ポータルでのユーザーの追加の詳細については、Mitel Knowledge Base で「[Adding a User (ユーザーの追加)](https://shoretelcommunity.force.com/s/article/Adding-Users-092815)」を参照してください。

次の詳細情報を使用して、MiCloud Connect アカウントにユーザーを作成します。

* **[名前]:** Britta Simon
* **勤務先のメール アドレス:** `brittasimon@<yourcompanydomain>.<extension>`   
  (例: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **ユーザー名:** `brittasimon@<yourcompanydomain>.<extension>`  
  (例: [brittasimon@contoso.com](mailto:brittasimon@contoso.com)。ユーザー名は通常、ユーザーの勤務先のメール アドレスと同じ)

> [!NOTE]
> MiCloud Connect のユーザー名は、Azure 内のユーザーのメール アドレスと同じである必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Mitel Connect] タイルを選択すると自動的にリダイレクトされ、 **[サインオン URL]** フィールドで既定値として構成した MiCloud Connect アプリケーションにサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>CloudLink Platform に対する Azure AD SSO の構成とテスト

このセクションでは、Azure portal で CloudLink Platform に対して Azure AD SSO を有効にする方法と、Azure AD を使用したシングル サインオンを許可するように CloudLink Platform アカウントを構成する方法について説明します。

CloudLink プラットフォームで Azure AD シングル サインオンを構成する際は、Azure portal と CloudLink アカウント ポータルを横に並べて開くことをお勧めします。これは、Azure portal から CloudLink アカウント ポータルに (またはその逆方向に) いくつかの情報をコピーする必要があるためです。

1. [Azure portal](https://portal.azure.com/) で構成ページを開くには、次のようにします。

    1. **Mitel Connect** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

       ![シングル サインオン構成のリンク](common/select-sso.png)

    1. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML]** を選択します。

       ![シングル サインオン選択モード](common/select-saml-option.png)
    
       **[SAML ベースのサインオン]** ページが開き、 **[基本的な SAML 構成]** セクションが表示されます。

       ![[SAML ベースのサインオン] ページを示すスクリーンショット。[基本的な SAML 構成] が表示されています。](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. CloudLink アカウント ポータルで **[Azure AD Single Sign On]\(Azure AD シングル サインオン\)** 構成パネルにアクセスするには、次のようにします。

    1. 統合を有効にする顧客アカウントの **[Account Information]\(アカウント情報\)** ページに移動します。

    1. **[Integrations]\(統合\)** セクションで、 **[+ Add new]\(新規追加\)** を選択します。 ポップアップ画面に **[Integrations]\(統合\)** パネルが表示されます。

    1. **[3rd party]\(サード パーティ\)** タブを選択します。サポートされるサード パーティ アプリケーションの一覧が表示されます。 **[Azure AD Single Sign On]\(Azure AD シングル サインオン\)** に関連付けられている **[Add]\(追加\)** ボタンを選択し、 **[Done]\(完了\)** を選択します。

       ![[統合] ページを示すスクリーンショット。ここでは、Azure AD シングル サインオンを追加できます。](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       顧客アカウントの **Azure AD シングル サインオン** が有効になり、 **[Account Information]\(アカウント情報\)** ページの **[Integrations]\(統合\)** セクションに追加されます。   

   1. **[Complete Setup]\(セットアップの完了\)** を選択します。
    
      ![Azure AD シングル サインオンの [セットアップの完了] オプションを示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      **[Azure AD Single Sign On]\(Azure AD シングル サインオン\)** 構成パネルが開きます。
      
       ![Azure AD シングル サインオンの構成を示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel では、 **[Optional Mitel credentials]\(オプションの Mitel 資格情報\)** セクションの **[Enable Mitel Credentials (Optional)]\(Mitel の資格情報を有効にする (オプション)\)** チェック ボックスをオフにすることを勧めします。 このチェック ボックスをオンにするのは、シングル サインオン オプションに加え、Mitel 資格情報を使用して、ユーザーが CloudLink アプリケーションにサインインできるようにする場合のみです。

3. Azure portal の **[SAML ベースのサインオン]** ページで、 **[基本的な SAML 構成]** セクションの **[編集]** アイコンを選択します。 **[基本的な SAML 構成]** パネルが開きます。

    ![[基本的な SAML 構成] ウィンドウを示すスクリーンショット。[編集] アイコンが選択されています。](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. CloudLink アカウント ポータル内の **[Mitel Identifier (Entity ID)]\(Mitel 識別子 (エンティティ ID)\)** フィールドから URL をコピーし、Azure portal の **[識別子 (エンティティ ID)]** フィールドに貼り付けます。

 5. CloudLink アカウント ポータル内の **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (Assertion Consumer Service URL)\)** フィールドから URL をコピーし、Azure portal の **[応答 URL (Assertion Consumer Service URL)]** フィールドに貼り付けます。  
    
    ![CloudLink アカウント ポータルと Azure portal のページ間の関係を示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. **[サインオン URL]** テキスト ボックスに、CloudLink アカウント ポータルを既定の Mitel アプリケーションとして使用するための URL である「`https://accounts.mitel.io`」を入力します。
     
     ![[サインオン URL] テキスト ボックスを示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > 既定の Mitel アプリケーションとは、ユーザーがアクセス パネルの [Mitel Connect] タイルを選択したときに開くアプリケーションです。 また、ユーザーが Azure AD からテスト セットアップを構成するときにアクセス先となるアプリケーションでもあります。

7. **[基本的な SAML 構成]** ダイアログ ボックスで **[保存]** を選択します。

8. Azure portal 内の **[SAML ベースのサインオン]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択して **署名証明書** をダウンロードします。 お使いのコンピューターに証明書ファイルを保存します。
  
    ![[SAML 署名証明書] セクションを示すスクリーンショット。ここでは、Base64 の証明書をダウンロードできます。](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. テキスト エディターで署名証明書ファイルを開き、ファイル内のデータをすべてコピーして、CloudLink アカウント ポータルの **[Signing Certificate]\(署名証明書\)** フィールドに貼り付けます。  

    > [!NOTE]
    > 複数の証明書がある場合は、それらを 1 つずつ貼り付けることをお勧めします。 
       
    ![手順のステップ 2 を示すスクリーンショット。ここでは、Azure AD 統合から値を入力します。](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Azure portal の **[SAML ベースのサインオン]** ページの **[Set up Mitel Connect]\(Mitel Connect の設定\)** セクションで、次の手順を実行します。

     1. **[ログイン URL]** フィールドから URL をコピーして、CloudLink アカウント ポータルの **[Sign-in URL]\(サインイン URL\)** フィールドに貼り付けます。

     1. **[Azure AD 識別子]** フィールドから URL をコピーして、CloudLink アカウント ポータルの **[IDP Identifier (Entity ID)]\(IDP 識別子 (エンティティ ID)\)** フィールドに貼り付けます。
     
        ![Mintel Connect で説明されている値のソースを示すスクリーンショット。](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. CloudLink アカウント ポータルの **[Azure AD Single Sign On]\(Azure AD シングル サインオン\)** パネルで、 **[Save]\(保存\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. ユーザーのプロパティのダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** フィールドに「brittasimon@\<yourcompanydomain\>.\<extension\>」と入力します。  たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    1. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mitel Connect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Mitel Connect]** を選択します。

    ![アプリケーションの一覧の Mitel Connect のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** を選択します。

6. SAML アサーション内に任意のロール値が必要な場合、ユーザーに適したロールを **[ロールの選択]** ダイアログ ボックスの一覧から選択し、画面の下部にある **[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-cloudlink-test-user"></a>CloudLink テスト ユーザーの作成

このセクションでは、CloudLink プラットフォームに **_Britta Simon_** という名前のテスト ユーザーを作成する方法について説明します。 ユーザーがシングル サインオンを使用できるようにするには、事前にユーザーを作成し、アクティブにする必要があります。

CloudLink アカウント ポータルでユーザーを追加する方法の詳細については、[CloudLink アカウント ドキュメント](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html)の「**_ユーザーの管理_**」を参照してください。

次の詳細情報を使用して、CloudLink アカウント ポータルでユーザーを作成します。

* 名前:Britta Simon
* 名: Britta
* 姓: Simon
* 電子メール: BrittaSimon@contoso.com

> [!NOTE]
> ユーザーの CloudLink メール アドレスは、Azure portal の **ユーザー プリンシパル名** と同じにする必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルの [Mitel Connect] タイルを選択すると自動的にリダイレクトされ、 **[サインオン URL]** フィールドで既定値として構成した CloudLink アプリケーションにサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)