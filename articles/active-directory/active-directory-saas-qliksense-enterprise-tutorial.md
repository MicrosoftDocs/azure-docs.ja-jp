---
title: "チュートリアル: Azure Active Directory と Qlik Sense Enterprise の統合 | Microsoft Docs"
description: "Azure Active Directory と Qlik Sense Enterprise の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 00dcedb09ea63b4337b2b730746fb2f5a1f27e64
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>チュートリアル: Azure Active Directory と Qlik Sense Enterprise の統合
このチュートリアルでは、Qlik Sense Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

Qlik Sense Enterprise と Azure AD の統合には、次の利点があります。

* Qlik Sense Enterprise にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Qlik Sense Enterprise にサインオン (シングル サインオン) する機能を有効にすることができます。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Qlik Sense Enterprise と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Qlik Sense Enterprise でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Qlik Sense Enterprise の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>ギャラリーからの Qlik Sense Enterprise の追加
Azure AD への Qlik Sense Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Qlik Sense Enterprise を追加する必要があります。

**ギャラリーから Qlik Sense Enterprise を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]

6. 検索ボックスに、「 **Qlik Sense Enterprise**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. 結果ウィンドウで **[Qlik Sense Enterprise]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Qlik Sense Enterprise で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Qlik Sense Enterprise ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Qlik Sense Enterprise の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Qlik Sense Enterprise の **[Username]** の値として割り当てることで確立されます。

Qlik Sense Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Qilk Sense Enterprise のテスト ユーザーの作成](#creating-a-qliksense-enterprise-test-user)** - Qilk Sense Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Qlik Sense Enterprise アプリケーションでシングル サインオンを構成します。

**Qlik Sense Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Qlik Sense Enterprise** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 

2. **[ユーザーの Qlik Sense Enterprise へのアクセスを設定してください]** ページで、**[Azure AD Single Sign-On] \(Azure AD のシングル サインオン)** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、**https://\<Qlik Sense の完全修飾ホスト名\>:443/<仮想プロキシのプレフィックス\>/samlauthn/** というパターンで、ユーザーが Qlik Sense Enterprise アプリケーションへのサインオンに使用する URL を入力します。
   
    > [!NOTE]
    > この URI 末尾のスラッシュに注意してください。  これは必須です。
    > 
    > 
   
    b. click **[次へ]**

4. **[Qlik Sense Enterprise でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[メタデータのダウンロード]** をクリックし、コンピューターにファイルを保存します。  Qlik Sense サーバーにアップロードする前に、このメタデータ ファイルを編集する準備をしておいてください。
   
    b. ページの下部にある **[次へ]**」を参照してください。

5. フェデレーション メタデータ XML ファイルを準備して、Qlik Sense サーバーにアップロードできるようにします。
   
    > [!NOTE]
    > IdP メタデータを Qlik Sense サーバーにアップロードする前にファイルを編集して情報を削除し、Azure AD と Qlik Sense サーバーの間の処理が正しく行われるようにする必要があります。
    > 
    > 
   
    ![QlikSense][qs24]
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 Azure からダウンロードした FederationMetaData.xml ファイルを、テキスト エディターで開きます。
   
    b. **RoleDescriptor** を検索します。  エントリは&4; つあります (開始要素タグと終了要素タグのペアが&2; つあります)。
   
    c. RoleDescriptor タグと、タグ間のすべての情報をファイルから削除します。
   
    d. このファイルは、このドキュメントで後ほど使用するため、わかりやすい場所に保存してください。
6. Qlik Sense Qlik Management Console (QMC) に、仮想プロキシ構成を作成できるユーザーとして移動します。
7. QMC で、メニュー項目の [Virtual Proxies (仮想プロキシ)] をクリックします。
   
    ![QlikSense][qs6] 
8. 画面の下部にある [Create new (新規作成)] ボタンをクリックします。
   
    ![QlikSense][qs7]
9. [Virtual Proxies (仮想プロキシ)] 編集画面が表示されます。  画面の右側のメニューで、構成オプションを表示できます。
   
    ![QlikSense][qs9]
10. [Identification (識別)] メニュー オプションを選択し、Azure 仮想プロキシ構成の識別情報を入力します。
    
    ![QlikSense][qs8]  
    
    a. [Description (説明)] フィールドは、仮想プロキシ構成のフレンドリ名です。  値として説明を入力します。
    
    b. [Prefix (プレフィックス)] フィールドでは、Azure AD シングル サインオンで Qlik Sense に接続するための仮想プロキシ エンドポイントを指定します。  この仮想プロキシに一意のプレフィックス名を入力します。
    
    c. [Session inactivity timeout (minutes) (セッションの無通信のタイムアウト (分))] は、この仮想プロキシを経由する接続のタイムアウトです。
    
    d. [Session cookie header name (セッション Cookie ヘッダー名)] は、認証が成功した後にユーザーが受け取る Qlik Sense セッション用のセッション識別子を格納する Cookie 名です。  この名前は一意である必要があります。
11. [Authentication (認証)] メニュー オプションをクリックして表示します。  [Authentication (認証)] 画面が表示されます。
    
    ![QlikSense][qs10]
    
    a. **[Anonymous access mode]** ドロップ ダウンで、匿名ユーザーによる仮想プロキシを介した Qlik Sense へのアクセスを許可するかどうかを設定します。  既定のオプションは、[No anonymous user (匿名ユーザーを許可しない)] です。
    
    b. **[Authentication method]** ドロップ ダウンで、仮想プロキシで使用する認証スキームを設定します。  ドロップダウン リストから SAML を選択してください。  その結果、さらにオプションが表示されます。
    
    c. **[SAML host URI]** フィールドに、ユーザーが SAML 仮想プロキシを介して Qlik Sense にアクセスする際に入力するホスト名を入力します。  ホスト名は、Qlik Sense サーバーの URI です。
    
    d. **[SAML entity ID]** に、[SAML host URI] フィールドに入力したのと同じ値を入力します。
    
    e. **[SAML IdP metadata]** に、以前に **Azure AD 構成からのフェデレーション メタデータの編集**に関するセクションで編集したファイルを指定します。  **IdP メタデータをアップロードする前に、このファイルを編集する必要があります**。Azure AD と Qlik Sense サーバーの間で処理が正しく行われるように、ファイルの情報を削除してください。  **まだファイルを編集していない場合は、上記の手順に従ってください。**  ファイルを編集済みの場合は、[Browse (参照)] ボタンをクリックし、編集したメタデータ ファイルを選択して、仮想プロキシ構成にアップロードします。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 これらは Azure AD が Qlik Sense サーバーに送信する **ID** を表します。  スキーマ リファレンス情報は、構成が終了した後に Azure アプリの画面から取得できます。  名前属性を使用する場合は**「enter http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name」と入力します**。
    
    g. **ユーザー ディレクトリ**用の値を入力します。この値は、ユーザーが Azure AD を介して Qlik Sense サーバーで認証を行うときに、ユーザーにアタッチされます。  ハードコーディングされた値は**角かっこ []** で囲む必要があります。  Azure AD SAML アサーション内で送信される属性を使用するには、属性の名前をこのボックスに角かっこ **なし** で入力します。
    
    h. **[SAML signing algorithm]** で、仮想プロキシ構成用のサービス プロバイダー (この場合は Qlik Sense サーバー) 証明書の署名を設定します。  Microsoft Enhanced RSA and AES Cryptographic Provider を使用して生成された、信頼された証明書を Qlik Sense サーバーで使用する場合は、SAML 署名アルゴリズムを **[SHA-256]** に変更します。
    
    i. [SAML attribute mapping (SAML 属性マッピング)] セクションでは、セキュリティ規則での使用を目的とした、Qlik Sense への追加の属性 (グループなど) の送信を許可します。
12. [Load balancing (負荷分散)] メニュー オプションをクリックして表示します。  [Load balancing (負荷分散)] 画面が表示されます。
    
    ![QlikSense][qs11]
13. [Add new server node (新しいサーバー ノードの追加)] ボタンをクリックし、エンジン ノードまたは Qlik Sense が負荷分散のためにセッションを送信する複数のノードを選択して、[Add (追加)] ボタンをクリックします。
    
    ![QlikSense][qs12]
14. [Advanced (詳細設定)] メニュー オプションをクリックして表示します。 [Advanced (詳細設定)] 画面が表示されます。
    
    ![QlikSense][qs13]
    
    a. [Host white list (ホスト名の許可リスト)] では、Qlik Sense サーバーへの接続時に受け入れられるホスト名を指定します。  **ユーザーが Qlik Sense サーバーへ接続する際に指定するホスト名を入力します。** ホスト名は、[SAML host URI (SAML ホスト URI)] の値から "https://" を除いたものです。
15. [Apply (適用)] ボタンをクリックします。
    
    ![QlikSense][qs14]
16. [OK] をクリックし、仮想プロキシにリンクされているプロキシが再起動されることを示す警告メッセージを受け入れます。
    
    ![QlikSense][qs15]
17. 画面の右側に、[Associated items (関連項目)] メニューが表示されます。  [Proxies (プロキシ)] メニュー オプションをクリックします。
    
    ![QlikSense][qs16]
18. [Proxies (プロキシ)] 画面が表示されます。  下部にある [Link (リンク)] ボタンをクリックして、仮想プロキシにプロキシをリンクさせます。
    
    ![QlikSense][qs17]
19. この仮想プロキシ接続をサポートするプロキシ ノードを選択し、[Link (リンク)] ボタンをクリックします。  リンクを作成すると、そのプロキシが [Associated proxies (関連プロキシ)] の下に一覧表示されます。
    
    ![QlikSense][qs18]
    ![QlikSense][qs19]
20. 約&5; ～&10; 秒後に、QMC の更新メッセージが表示されます。  [Refresh QMC (QMC の更新)] ボタンをクリックします。
    
    ![QlikSense][qs20]
21. QMC が更新されたら、[Virtual Proxies (仮想プロキシ)] メニュー項目をクリックします。 新しい SAML 仮想プロキシのエントリが画面の表に表示されます。  仮想プロキシのエントリをクリックします。
    
    ![QlikSense][qs51]
22. 画面の下部にある [Download SP metadata (SP メタデータのダウンロード)] ボタンがアクティブになります。  [Download SP metadata (SP メタデータのダウンロード)] ボタンをクリックして、メタデータをファイルに保存します。
    
    ![QlikSense][qs52]
23. SP メタデータ ファイルを開きます。  **entityID** エントリと **AssertionConsumerService** エントリを確認します。  これらの値は、Azure AD アプリケーション構成の**識別子**と**サインオン URL** に対応しています。 これらが一致していない場合、Azure AD アプリケーション構成ウィザードで値を置き換える必要があります。
    
    ![QlikSense][qs53]
24. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
    ![Azure AD のシングル サインオン][10]
25. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。 ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 
   
    a. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。   

### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise のテスト ユーザーの作成
このセクションでは、Qlik Sense Enterprise で Britta Simon というユーザーを作成します。 Qlik Sense Enterprise サポート チームと連携し、Qlik Sense Enterprise プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Qlik Sense Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Qlik Sense Enterprise に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、 **[Qlik Sense Enterprise]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

## <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Qlik Sense Enterprise] タイルをクリックすると、自動的に Qlik Sense Enterprise アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

