---
title: チュートリアル:Azure Active Directory と Qlik Sense Enterprise の統合 | Microsoft Docs
description: Azure Active Directory と Qlik Sense Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2d046f5f039555e58d9ce4c028e750ce083fd5f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733691"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>チュートリアル:Qlik Sense Enterprise と Azure Active Directory の統合

このチュートリアルでは、Qlik Sense Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。 Qlik Sense Enterprise と Azure AD を統合すると、次のことができます。

* Qlik Sense Enterprise にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Qlik Sense Enterprise に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Qlik Sense Enterprise でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 
* Qlik Sense Enterprise では、**SP** によって開始される SSO がサポートされます。
* Qlik Sense Enterprise では、**ジャストインタイム プロビジョニング** がサポートされます。

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>ギャラリーからの Qlik Sense Enterprise の追加

Azure AD への Qlik Sense Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Qlik Sense Enterprise を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Qlik Sense Enterprise**」と入力します。
1. 結果のパネルから **[Qlik Sense Enterprise]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-qlik-sense-enterprise"></a>Qlik Sense Enterprise の Azure AD SSO の構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Qlik Sense Enterprise に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Qlik Sense Enterprise の関連ユーザーとの間にリンク関係を確立する必要があります。

Qlik Sense Enterprise に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Qlik Sense Enterprise SSO の構成](#configure-qlik-sense-enterprise-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Qlik Sense Enterprise のテスト ユーザーの作成](#create-qlik-sense-enterprise-test-user)** - Qlik Sense Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Qlik Sense Enterprise** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次のいずれかのパターンで URL を入力します。

    | 識別子 |
    |-------------|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com` |
    | `https://<Fully Qualified Domain Name>.qliksense.com` |
    |
   

    c. **[応答 URL]** ボックスに、 のパターンを使用して URL を入力します。

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は実際のサインオン URL、識別子、応答 URL に変更してください。これらの値の取得方法については、このチュートリアルの後半の説明を参照するか、[Qlik Sense Enterprise クライアント サポート チーム](https://www.qlik.com/us/services/support)に問い合わせてください。 URL の既定のポートは 443 ですが、組織のニーズに合わせてカスタマイズできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Qlik Sense Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Qlik Sense Enterprise]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-qlik-sense-enterprise-sso"></a>Qlik Sense Enterprise SSO の構成

1. フェデレーション メタデータ XML ファイルを準備して、Qlik Sense サーバーにアップロードできるようにします。

    > [!NOTE]
    > IdP メタデータを Qlik Sense サーバーにアップロードする前にファイルを編集して情報を削除し、Azure AD と Qlik Sense サーバーの間の処理が正しく行われるようにする必要があります。

    ![Visual Studio Code ウィンドウとフェデレーション メタデータ X M L ファイルを示すスクリーンショット。][qs24]

    a. Azure Portal からダウンロードした FederationMetaData.xml ファイルを、テキスト エディターで開きます。

    b. **RoleDescriptor** を検索します。  エントリは 4 つあります (開始要素タグと終了要素タグのペアが 2 つあります)。

    c. RoleDescriptor タグと、タグ間のすべての情報をファイルから削除します。

    d. このファイルは、このドキュメントで後ほど使用するため、わかりやすい場所に保存してください。

2. Qlik Sense Qlik Management Console (QMC) に、仮想プロキシ構成を作成できるユーザーとして移動します。

3. QMC で、メニュー項目の **[Virtual Proxies]** をクリックします。

    ![[CONFIGURE SYSTEM]\(構成システム\) から選択された仮想プロキシを示すスクリーンショット。][qs6]

4. 画面の下部にある **[Create new]** ボタンをクリックします。

    ![[Create new]\(新規作成\) オプションを示すスクリーンショット。][qs7]

5. [Virtual Proxies (仮想プロキシ)] 編集画面が表示されます。  画面の右側のメニューで、構成オプションを表示できます。

    ![[Properties]\(プロパティ\) から選択された [Identification]\(識別\) を示すスクリーンショット。][qs9]

6. [Identification (識別)] メニュー オプションを選択し、Azure 仮想プロキシ構成の識別情報を入力します。

    ![[Edit virtual proxy Identification]\(仮想プロキシ ID の編集\) セクションのスクリーンショット。ここで、説明されている値を入力できます。][qs8]  

    a. **[Description]** フィールドは、仮想プロキシ構成のフレンドリ名です。  値として説明を入力します。

    b. **[Prefix]** フィールドでは、Azure AD シングル サインオンで Qlik Sense に接続するための仮想プロキシ エンドポイントを指定します。  この仮想プロキシに一意のプレフィックス名を入力します。

    c. **[Session inactivity timeout (minutes)]** は、この仮想プロキシを経由する接続のタイムアウトです。

    d. **[Session cookie header name]** は、認証が成功した後にユーザーが受け取る Qlik Sense セッション用のセッション識別子を格納する Cookie 名です。  この名前は一意である必要があります。

7. [Authentication (認証)] メニュー オプションをクリックして表示します。  [Authentication (認証)] 画面が表示されます。

    ![[Edit virtual proxy]\(仮想プロキシの編集\) の [Authentication]\(認証\) セクションのスクリーンショット。ここで、説明されている値を入力できます。][qs10]

    a. **[Anonymous access mode]** ドロップ ダウンで、匿名ユーザーによる仮想プロキシを介した Qlik Sense へのアクセスを許可するかどうかを設定します。  既定のオプションは、[No anonymous user (匿名ユーザーを許可しない)] です。

    b. **[Authentication method]** ドロップ ダウンで、仮想プロキシで使用する認証スキームを設定します。  ドロップダウン リストから SAML を選択してください。  その結果、さらにオプションが表示されます。

    c. **[SAML host URI]** フィールドに、ユーザーが SAML 仮想プロキシを介して Qlik Sense にアクセスする際に入力するホスト名を入力します。  ホスト名は、Qlik Sense サーバーの URI です。

    d. **[SAML entity ID]** に、[SAML host URI] フィールドに入力したのと同じ値を入力します。

    e. **[SAML IdP metadata]** に、以前に **Azure AD 構成からのフェデレーション メタデータの編集** に関するセクションで編集したファイルを指定します。  **IdP メタデータをアップロードする前に、このファイルを編集する必要があります**。Azure AD と Qlik Sense サーバーの間で処理が正しく行われるように、ファイルの情報を削除してください。  **まだファイルを編集していない場合は、上記の手順に従ってください。**  ファイルを編集済みの場合は、[Browse (参照)] ボタンをクリックし、編集したメタデータ ファイルを選択して、仮想プロキシ構成にアップロードします。

    f. これらは Azure AD が Qlik Sense サーバーに送信する **UserID** を表します。  スキーマ リファレンス情報は、構成が終了した後に Azure アプリの画面から取得できます。  名前属性を使用するには、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    g. **ユーザー ディレクトリ** 用の値を入力します。この値は、ユーザーが Azure AD を介して Qlik Sense サーバーで認証を行うときに、ユーザーにアタッチされます。  ハードコーディングされた値は **角かっこ []** で囲む必要があります。  Azure AD SAML アサーション内で送信される属性を使用するには、属性の名前をこのボックスに角かっこ **なし** で入力します。

    h. **[SAML signing algorithm]** で、仮想プロキシ構成用のサービス プロバイダー (この場合は Qlik Sense サーバー) 証明書の署名を設定します。  Microsoft Enhanced RSA and AES Cryptographic Provider を使用して生成された、信頼された証明書を Qlik Sense サーバーで使用する場合は、SAML 署名アルゴリズムを **[SHA-256]** に変更します。

    i. [SAML attribute mapping (SAML 属性マッピング)] セクションでは、セキュリティ規則での使用を目的とした、Qlik Sense への追加の属性 (グループなど) の送信を許可します。

8. **[LOAD BALANCING]** メニュー オプションをクリックして表示します。  [Load balancing (負荷分散)] 画面が表示されます。

    ![[Virtual proxy edit]\(仮想プロキシの編集\) 画面の [LOAD BALANCING]\(負荷分散\) を示すスクリーンショット。ここで、[Add new server node]\(新しいサーバー ノードの追加\) を選択できます。][qs11]

9. **[Add new server node]** ボタンをクリックし、エンジン ノードまたは Qlik Sense が負荷分散のためにセッションを送信する複数のノードを選択して、 **[Add]** ボタンをクリックします。

    ![[Add server nodes to load balance on]\(負荷分散するサーバー ノードの追加\) ダイアログのサーバーの追加ボタンを示すスクリーンショット。][qs12]

10. [Advanced (詳細設定)] メニュー オプションをクリックして表示します。 [Advanced (詳細設定)] 画面が表示されます。

    ![[Edit virtual proxy]\(仮想プロキシの編集\) の [Advanced]\(詳細設定\) 画面のスクリーンショット。][qs13]

    ホストの許可リストでは、Qlik Sense サーバーへの接続時に受け入れられるホスト名を指定します。  **ユーザーが Qlik Sense サーバーへ接続する際に指定するホスト名を入力します。** ホスト名は、[SAML host URI (SAML ホスト URI)] の値から "https://" を除いたものです。

11. **[適用]** をクリックします。

    ![[Apply]\(適用\) ボタンのスクリーンショット。][qs14]

12. [OK] をクリックし、仮想プロキシにリンクされているプロキシが再起動されることを示す警告メッセージを受け入れます。

    ![[Apply changes to virtual proxy]\(仮想プロキシに変更を適用する\) 確認メッセージのスクリーンショット。][qs15]

13. 画面の右側に、[Associated items (関連項目)] メニューが表示されます。  **[Proxies]** メニュー オプションをクリックします。

    ![[Associated items]\(関連項目\) から選択された [Proxies]\(プロキシ\) のスクリーンショット。][qs16]

14. [Proxies (プロキシ)] 画面が表示されます。  下部にある **[Link]** ボタンをクリックして、仮想プロキシにプロキシをリンクさせます。

    ![[Link]\(リンク\) ボタンのスクリーンショット。][qs17]

15. この仮想プロキシ接続をサポートするプロキシ ノードを選択し、 **[Link]** ボタンをクリックします。  リンクを作成すると、そのプロキシが [Associated proxies (関連プロキシ)] の下に一覧表示されます。

    ![[Select proxy services]\(プロキシ サービスの選択\) のスクリーンショット。][qs18]
  
    ![[Virtual proxy associated items]\(仮想プロキシの関連項目\) ダイアログ ボックスの [Associated proxies]\(関連プロキシ\) のスクリーンショット。][qs19]

16. 約 5 ～ 10 秒後に、QMC の更新メッセージが表示されます。  **[Refresh QMC]** ボタンをクリックします。

    !["Your session has ended (セッションが終了しました)" メッセージのスクリーンショット。][qs20]

17. QMC が更新されたら、 **[Virtual Proxies]** メニュー項目をクリックします。 新しい SAML 仮想プロキシのエントリが画面の表に表示されます。  仮想プロキシのエントリをクリックします。

    ![1 件のエントリを含む [Virtual proxies]\(仮想プロキシ\) のスクリーンショット。][qs51]

18. 画面の下部にある [Download SP metadata (SP メタデータのダウンロード)] ボタンがアクティブになります。  **[Download SP metadata]** ボタンをクリックして、メタデータをファイルに保存します。

    ![[Download S P metadata]\(S P メタデータのダウンロード\) ボタンのスクリーンショット。][qs52]

19. SP メタデータ ファイルを開きます。  **entityID** エントリと **AssertionConsumerService** エントリを確認します。  これらの値は、Azure AD アプリケーション構成の **識別子**、**サインオン URL**、**応答 URL** に対応しています。 一致しない場合は Azure AD アプリケーションの構成の **[Qlik Sense Enterprise のドメインと URL]** セクションにこれらの値を貼り付けて、Azure AD アプリケーションの構成ウィザードで置換する必要があります。

    ![EntityDescriptor が表示されたプレーンテキスト エディターのスクリーンショット。entityID と AssertionConsumerService が強調表示されている。][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Qlik Sense Enterprise のテスト ユーザーの作成

Qlik Sense Enterprise は **ジャストインタイム プロビジョニング** をサポートしているため、ユーザーは SSO 機能を使用すると、Qlik Sense Enterprise の "USERS" リポジトリに自動的に追加されます。 加えて、クライアントは QMC を使用して UDC (User Directory Connector) を作成することにより、任意の LDAP (Active Directory など) から Qlik Sense Enterprise にユーザーを事前設定することができます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Qlik Sense Enterprise のサインオン URL にリダイレクトされます。 

* Qlik Sense Enterprise のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Qlik Sense Enterprise] タイルをクリックすると、Qlik Sense Enterprise のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Qlik Sense Enterprise を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png