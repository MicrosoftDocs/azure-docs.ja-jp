---
title: チュートリアル:Azure Active Directory と SharePoint オンプレミスの統合 | Microsoft Docs
description: Azure Active Directory と SharePoint オンプレミスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862311"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>チュートリアル:Azure Active Directory シングル サインオンと SharePoint オンプレミスの統合

このチュートリアルでは、SharePoint オンプレミスと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SharePoint オンプレミスを統合すると、次のことができます。

* SharePoint オンプレミスにアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して SharePoint オンプレミスに自動的にサインインできるようにする。
* Azure portal でアカウントを管理する。

## <a name="prerequisites"></a>前提条件

Azure AD と SharePoint オンプレミスの統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SharePoint 2013 以降のファーム。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオン (SSO) を構成してテストします。 Azure AD のユーザーは SharePoint オンプレミスにアクセスできます。

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Azure portal でエンタープライズ アプリケーションを作成する

Azure AD への SharePoint オンプレミスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SharePoint オンプレミスを追加する必要があります。

ギャラリーから SharePoint オンプレミスを追加するには:

1. Azure portal の左端のペインで、 **[Azure Active Directory]** を選択します。

   > [!NOTE]
   > この要素を使用できない場合は、左端のペインの上部にある **[すべてのサービス]** リンクを使用して開くこともできます。 次の概要では、 **[Azure Active Directory]** リンクが **[ID]** セクションにあります。 また、フィルター ボックスを使用して検索することもできます。

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

1. 検索ボックスに、「**SharePoint on-premises**」と入力します。 結果ペインで **[SharePoint on-premises]\(SharePoint オンプレミス\)** を選択します。

    <kbd>![結果リストの SharePoint オンプレミス](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. SharePoint オンプレミスのインスタンスの名前を指定し、 **[追加]** を選択してアプリケーションを追加します。

1. 新しいエンタープライズ アプリケーションで **[プロパティ]** を選択し、 **[ユーザーの割り当てが必要ですか?]** の値を確認します。

   <kbd>![[ユーザーの割り当てが必要ですか?] のトグル](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   このシナリオでは、この値が **[いいえ]** に設定されています。

## <a name="configure-and-test-azure-ad"></a>Azure AD を構成してテストする

このセクションでは、SharePoint オンプレミスに対する Azure AD SSO を構成します。 SSO を機能させるには、Azure AD ユーザーと SharePoint オンプレミスの関連ユーザーの間にリンク関係を確立します。

SharePoint オンプレミスで Azure AD SSO を構成してテストするには、次の項目を完了します。

- [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
- [SharePoint オンプレミスを構成する](#configure-sharepoint-on-premises) - アプリケーション側で SSO 設定を構成します。
- [Azure portal で Azure AD テスト ユーザーを作成する](#create-an-azure-ad-test-user-in-the-azure-portal) - SSO 用に Azure AD で新しいユーザーを作成します。
- [Azure portal で Azure AD セキュリティ グループを作成する](#create-an-azure-ad-security-group-in-the-azure-portal) - SSO 用に Azure AD で新しいセキュリティ グループを作成します。
- [SharePoint オンプレミスで Azure AD アカウントにアクセス許可を付与する](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) - Azure AD ユーザーにアクセス許可を付与します。
- [SharePoint オンプレミスで Azure AD グループにアクセス許可を付与する](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) - Azure AD グループにアクセス許可を付与します。
- [Azure portal で SharePoint オンプレミスへのアクセス権をゲスト アカウントに付与する](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) - SharePoint オンプレミスへのアクセス許可を Azure AD のゲスト アカウントに付与します。
- [複数の Web アプリケーションに対して信頼できる ID プロバイダーを構成する](#configure-the-trusted-identity-provider-for-multiple-web-applications) - 複数の Web アプリケーションに対して同一の信頼できる ID プロバイダーを使用します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal で Azure AD の SSO を有効にします。

SharePoint オンプレミスで Azure AD SSO を構成するには

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 以前に作成したエンタープライズ アプリケーションの名前を選択し、 **[シングル サインオン]** を選択します。

1. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML]** モードを選択して SSO を有効にします。
 
1. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SharePoint オンプレミス ドメインと URL] の SSO 情報](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. **[識別子]** ボックスに、次のパターンを使用して URL を入力します: `urn:<sharepointFarmName>:<federationName>`。

    1. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します: `https://<YourSharePointSiteURL>/_trust/`。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します: `https://<YourSharePointSiteURL>/`。
    1. **[保存]** を選択します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際のサインオン URL、識別子、応答 URL で更新してください。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** を選択して、要件に基づいて指定したオプションの **証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. **[SharePoint オンプレミスのセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。
    
    - **ログイン URL**
    
        ログイン URL をコピーし、末尾の **/saml2** を **/wsfed** に置き換えて、 https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed となるようにします (この URL は正確ではありません)。

    - **Azure AD 識別子**
    - **ログアウト URL**

    > [!NOTE]
    > この URL は、SharePoint ではそのまま使用できません。 **/saml2** を **/wsfed** に置き換える必要があります。 SharePoint オンプレミス アプリケーションは SAML 1.1 トークンを使用するため、Azure AD では SharePoint サーバーからの WS 取り込み要求が必要です。 認証後、SAML 1.1 トークンが発行されます。

### <a name="configure-sharepoint-on-premises"></a>SharePoint オンプレミスを構成する

1. SharePoint Server 2016 で、信頼できる ID プロバイダーを新しく作成します。

    この SharePoint Server にサインインし、SharePoint 管理シェルを開きます。 次の値を入力します。
    - **$realm** は、Azure portal の [SharePoint オンプレミス ドメインと URL] セクションにある識別子の値です。
    - **$wsfedurl** は SSO サービスの URL です。
    - **$filepath** は、Azure portal から証明書ファイルをダウンロードしたファイル パスです。

    次のコマンドを実行して、信頼できる ID プロバイダーを新しく構成します。

    > [!TIP]
    > PowerShell を使用した経験がない場合、または PowerShell の動作の詳細については、[SharePoint PowerShell](/powershell/sharepoint/overview) に関する記事をご覧ください。


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. 対象のアプリケーションの信頼できる ID プロバイダーを有効にします。

    1. **[サーバーの全体管理]** で **[Web アプリケーションの管理]** に移動し、Azure AD を使用してセキュリティ保護する Web アプリケーションを選択します。

    1. リボンの **[認証プロバイダー]** を選択し、使用するゾーンを選択します。

    1. **[信頼できる ID プロバイダー]** を選択し、*AzureAD* という名前で先ほど登録した ID プロバイダーを選択します。

    1. **[OK]** を選択します。

    ![認証プロバイダーを構成する](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure portal で Azure AD テスト ユーザーを作成する

このセクションの目的は、Azure portal でテスト ユーザーを作成することです。

1. Azure portal の左端のペインで、 **[Azure Active Directory]** を選択します。 **[管理]** ペインで **[ユーザー]** を選択します。

1. 画面の上部で **[すべてのユーザー]**  >  **[新しいユーザー]** を選択します。

1. **[ユーザーの作成]** を選択し、ユーザーのプロパティで次の手順を実行します。 テナントのサフィックスまたは任意の検証済みドメインを使用して、自分の Azure AD にユーザーを作成できる場合があります。 

    1. **[名前]** ボックスにユーザー名を入力します。 ここでは、「**TestUser**」を使用しました。
  
    1. **[ユーザー名]** ボックスに「`TestUser@yourcompanydomain.extension`」と入力します。 この例では、`TestUser@contoso.com` が示されます。

       ![[ユーザー] ダイアログ ボックス](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示される値を書き留めます。

    1. **［作成］** を選択します

    1. これで、サイトを TestUser@contoso.com と共有し、このユーザーにアクセスを許可することができます。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを作成する

1. **[Azure Active Directory]**  >  **[グループ]** を選択します。

1. **[新しいグループ]** を選びます。

1. **[グループの種類]** 、 **[グループ名]** 、 **[グループの説明]** 、および **[メンバーシップの種類]** ボックスに入力します。 矢印を選択してメンバーを選択した後、グループに追加するメンバーを検索または選択します。 **[選択]** を選択して、選択したメンバーを追加した後、 **[作成]** を選択します。

![Azure AD セキュリティ グループを作成する](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>SharePoint オンプレミスで Azure AD アカウントにアクセス許可を付与する

SharePoint オンプレミスで Azure AD ユーザーにアクセス権を付与するには、サイト コレクションを共有するか、サイト コレクションのグループのいずれかに Azure AD ユーザーを追加します。 これで、ユーザーは Azure AD の ID を使用して SharePoint 201x にサインインできるようになりましたが、ユーザー エクスペリエンスを改善する余地がまだあります。 たとえば、ユーザーを検索すると、ユーザーの選択ウィンドウに複数の検索結果が表示されます。 要求のマッピングで作成された要求の種類ごとに検索結果が表示されます。 ユーザー ピッカーを使用してユーザーを選択するには、ユーザー名を正確に入力し、**名前** 要求結果を選択する必要があります。

![要求検索結果](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

検索対象の値の検証を実行しないため、スペル ミスが生じたり、ユーザーが誤って正しくない種類の要求を選択したりする可能性があります。 この場合、ユーザーはリソースに正常にアクセスできなくなる可能性があります。

ユーザー ピッカーを使用したこのシナリオを修正するために、[AzureCP](https://yvand.github.io/AzureCP/) と呼ばれるオープンソース ソリューションには SharePoint 2013、2016、2019 向けのカスタム クレーム プロバイダーが用意されています。 これは、Microsoft Graph API を使用して、ユーザーが入力した内容を解決し、検証を実行します。 詳細については、「[AzureCP](https://yvand.github.io/AzureCP/)」を参照してください。

  > [!NOTE]
  > AzureCP がない場合は、Azure AD グループの ID を追加することでグループを追加できますが、この方法はユーザー フレンドリではなく、信頼性も高くありません。 しくみは次のとおりです。
  > 
  >![SharePoint グループに ID ごとの Azure AD グループを追加する](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>SharePoint オンプレミスで Azure AD グループにアクセス許可を付与する

SharePoint オンプレミスに Azure AD セキュリティ グループを割り当てるには、SharePoint Server 用のカスタム クレーム プロバイダーを使用する必要があります。 この例では、AzureCP を使用します。

 > [!NOTE]
 > AzureCP は Microsoft 製品ではないため、Microsoft サポートではサポートされません。 オンプレミスの SharePoint ファームで AzureCP をダウンロード、インストール、構成するには、[AzureCP](https://yvand.github.io/AzureCP/) Web サイトを参照してください。 

1. オンプレミスの SharePoint ファームまたは代わりのカスタム クレーム プロバイダー ソリューションで、AzureCP を構成します。 AzureCP を構成するには、この [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) Web サイトを参照してください。

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 以前に作成したエンタープライズ アプリケーションの名前を選択し、 **[シングル サインオン]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[ユーザー属性とクレーム]** セクションを編集します。

1. **[グループ要求を追加する]** を選択します。

1. この要求で返される必要がある、ユーザーに関連付けられたグループを選択します。 この場合は **[すべてのグループ]** を選択します。 **[基になる属性]** セクションで、 **[グループ ID]** を選択し、 **[保存]** を選択します。

SharePoint オンプレミスで Azure AD セキュリティ グループにアクセス権を付与するには、サイト コレクションを共有するか、サイト コレクションのグループのいずれかに Azure AD セキュリティ グループを追加します。

1. **[Sharepoint サイト コレクション]** に移動します。 サイト コレクションの **[サイトの設定]** で **[ユーザーとグループ]** を選択します。 

1. SharePoint グループを選択し、 **[新規]**  >  **[Add Users to this Group]\(ユーザーをこのグループに追加する\)** を選択します。 グループの名前を入力すると、ユーザー ピッカーに Azure AD セキュリティ グループが表示されます。

    ![SharePoint グループに Azure AD グループを追加する](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Azure portal で SharePoint オンプレミスへのアクセス権をゲスト アカウントに付与する

UPN が変更されるようになったため、一貫した方法で SharePoint サイトへのアクセス権をゲスト アカウントに付与できるようになりました。 たとえば、ユーザー `jdoe@outlook.com` は `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` と表されます。 外部ユーザーとサイトを共有するには、Azure portal の **[ユーザー属性とクレーム]** セクションでいくつかの変更を加える必要があります。

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 以前に作成したエンタープライズ アプリケーションの名前を選択し、 **[シングル サインオン]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[ユーザー属性とクレーム]** セクションを編集します。

1. **[必要な要求]** ゾーンで、 **[一意のユーザー識別子 (名前 ID)]** を選択します。

1. **[基になる属性]** プロパティを値 **user.localuserprincipalname** に変更し、 **[保存]** を選択します。

    ![[ユーザー属性とクレーム] の最初の [基になる属性]](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. リボンを使用して、 **[SAML ベースのサインオン]** に戻ります。 **[ユーザー属性とクレーム]** セクションは次のようになります。 

    ![[ユーザー属性とクレーム] の最終的な設定](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > この設定では、姓と名を指定する必要はありません。

1. Azure portal の左端のペインで、 **[Azure Active Directory]** を選択し、 **[ユーザー]** を選択します。

1. **[新しいゲスト ユーザー]** を選択します。

1. **[ユーザーの招待]** オプションを選択します。 ユーザーのプロパティを入力し、 **[招待する]** を選択します。

1. これで、サイトを MyGuestAccount@outlook.com と共有し、このユーザーにアクセスを許可することができます。

    ![ゲスト アカウントとのサイトの共有](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>複数の Web アプリケーションに対して信頼できる ID プロバイダーを構成する

この構成は 1 つの Web アプリケーションに役立ちますが、複数の Web アプリケーションに対して同一の信頼できる ID プロバイダーを使用する場合は追加の構成が必要になります。 たとえば、URL `https://sales.contoso.com` を使用するよう Web アプリケーションを拡張し、`https://marketing.contoso.com` に対してユーザーを認証する必要があるとします。 このためには、WReply パラメーターを受け入れるよう ID プロバイダーを更新し、Azure AD でアプリケーションの登録を更新して応答 URL を追加します。

1. Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** を選択します。 以前に作成したエンタープライズ アプリケーションの名前を選択し、 **[シングル サインオン]** を選択します。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** を編集します。

    ![基本的な SAML 構成](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. **[応答 URL (Assertion Consumer Service URL)]** で、追加の Web アプリケーションの URL を追加し、 **[保存]** を選択します。

    ![[基本的な SAML 構成] の編集](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. SharePoint サーバーで SharePoint 201x 管理シェルを開き、次のコマンドを実行します。 以前に使用した信頼できる ID トークンの発行者の名前を使用します。
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. **[サーバーの全体管理]** で、その Web アプリケーションに移動し、既存の信頼できる ID プロバイダーを有効にします。

SharePoint オンプレミス インスタンスへのアクセス権を内部ユーザーに付与する必要がある他のシナリオもあります。 このシナリオでは、Microsoft Azure Active Directory Connect をデプロイして、オンプレミス ユーザーを Azure AD と同期することを許可する必要があります。 この設定については、別の記事で説明します。

## <a name="next-steps"></a>次の手順

SharePoint オンプレミスを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
