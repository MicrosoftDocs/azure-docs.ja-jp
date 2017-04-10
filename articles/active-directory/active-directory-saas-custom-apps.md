---
title: "アプリケーション用の Azure AD SSO の構成 | Microsoft Docs"
description: "SAML およびパスワード ベースの SSO を使用して、Azure Active Directory にアプリをセルフサービス接続する方法について説明します。"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: asmalser
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 18415c92d50a00c14823685857ab7e2624334ec7
ms.openlocfilehash: b4a1bb3211da8c02d48ebad69d5e1cbb4de2c45d
ms.lasthandoff: 03/01/2017


---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成
この記事では、管理者が *コードを記述せずに*Azure Active Directory アプリケーション ギャラリーに存在しないアプリケーションへのシングル サインオンを構成できるようにする機能について説明します。 この機能は、2015 年 11 月 18 日に技術プレビューでリリースされ、 [Azure Active Directory Premium](active-directory-editions.md)に含まれています。 コードを使用してカスタム アプリケーションと Azure AD を統合する方法に関する開発者向けガイダンスをお探しの場合は、「 [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。

[この記事](active-directory-appssoaccess-whatis.md)で説明されているように、Azure Active Directory アプリケーション ギャラリーには、Azure Active Directory によるシングル サインオンの形式をサポートすることがわかっているアプリケーションの一覧が表示されます。 (組織内の IT スペシャリストまたはシステム インテグレーターとして) 接続するアプリケーションを見つけたら、Microsoft Azure 管理ポータルに示される詳細な手順に従って、シングル サインオンを有効にすることができます。

[Azure Active Directory Premium](active-directory-editions.md) ライセンスを所有するお客様も、これらの追加機能を使用できます。

* SAML 2.0 ID プロバイダーをサポートする任意のアプリケーションのセルフサービス統合 (SP または IdP によって開始)
* [パスワードベースの SSO](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* ユーザー プロビジョニング用の SCIM プロトコルを使用するアプリケーションのセルフサービス接続 ([ここで説明](active-directory-scim-provisioning.md))
* [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)または [Azure AD アクセス パネル](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)での任意のアプリケーションへのリンクの追加機能

これには、使用するが Azure AD アプリケーション ギャラリーにはまだ追加されていない SaaS アプリケーションだけでなく、組織がクラウドまたはオンプレミスで制御対象のサーバーにデプロイしたサードパーティの Web アプリケーションも含まれる場合があります。

これらの機能は、 *アプリ統合テンプレート*とも呼ばれ、SAML、SCIM、またはフォーム ベース認証をサポートするアプリに標準ベースの接続ポイントを提供し、さまざまなアプリケーションとの互換性のために柔軟なオプションや設定を用意しています。 

## <a name="adding-an-unlisted-application"></a>一覧にないアプリケーションの追加
アプリケーション統合テンプレートを使用してアプリケーションを接続するには、Azure Active Directory 管理者アカウントを使用して Azure 管理ポータルにサインインし、**[Active Directory] > [ディレクトリ] > [アプリケーション]** を参照します。**[追加]** を選択し、**[ギャラリーからアプリケーションを追加します]** を選択します。 

![][1]

アプリケーション ギャラリーでは、左側の **[カスタム]** カテゴリを使用するか、必要なアプリが見つからない場合は検索結果に表示されている **[私の組織で使用している、一覧にないアプリケーションを追加]** リンクを選択することで、一覧にないアプリを追加できます。 アプリケーションの [名前] を入力すると、シングル サインオン オプションとその動作を構成できます。 

**ポップ ヒント**: ベスト プラクティスとして、検索機能を使用して、アプリケーションがアプリケーション ギャラリーに既に存在するかどうかを確認します。 アプリが検出され、その説明に "シングル サインオン" と記載されている場合は、フェデレーション シングル サインオンで既にそのアプリケーションがサポートされています。 

![][2]

このようにアプリケーションを追加することで、事前に統合されたアプリケーションで使用可能なものによく似たエクスペリエンスが提供されます。 作業を開始するには、 **[シングル サインオンの構成]**を選択します。 次の画面には、以降のセクションで説明されているシングル サインオンを構成するための&3; つのオプションが示されています。

![][3]

## <a name="azure-ad-single-sign-on"></a>Azure AD Single Sign-On
アプリケーションの SAML ベースの認証を構成するには、このオプションを選択します。 その場合、アプリケーションで SAML 2.0 がサポートされている必要があり、ユーザーは作業を続行する前に、アプリケーションの SAML 機能の使用方法に関する情報を収集する必要があります。 **[次へ]**を選択すると、アプリケーションの SAML エンドポイントに対応する&3; つの異なる URL を入力するように求められます。 

![][4]

次のとおりです。

* **サインオン URL (SP によって開始される場合のみ)** - このアプリケーションにサインインするときにユーザーがアクセスする場所。 サービス プロバイダーによって開始されるシングル サインオンを実行するようにアプリケーションが構成されている場合は、ユーザーがこの URL に移動すると、認証とログオンを行うために、そのユーザーはサービス プロバイダーによって Azure AD にリダイレクトされます。 このフィールドに URL が入力されている場合、Azure AD はその URL を使用して Office 365 と Azure AD アクセス パネルからアプリケーションを起動します。 このフィールドへの入力が省略されている場合、Azure AD は、アプリケーションが Office 365、Azure AD アクセス パネル、または Azure AD シングル サインオン URL ([ダッシュボード] タブからコピー可能) から起動されたときに、ID プロバイダーによって開始されるサインオンを実行します。
* **発行者の URL** - 発行者の URL は、シングル サインオンの構成対象のアプリケーションを一意に識別する URL であることが必要です。 これは、SAML トークンの **Audience** パラメーターとして Azure AD からアプリケーションに返される値であり、アプリケーションではこの値を検証する必要があります。 また、この値はアプリケーションによって提供される SAML メタデータ内に **Entity ID** として表示されます。 SAML トークンの Entity ID または Audience 値の詳細については、アプリケーションの SAML ドキュメントを確認してください。 アプリケーションに返された SAML トークン内に表示される Audience URL の例を以下に示します。

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **応答 URL** - 応答 URL は、アプリケーションが SAML トークンを受け取ることになっている場所です。 これは **Assertion Consumer Service (ACS) URL**とも呼ばれています。 SAML トークンの応答 URL または ACS URL の詳細については、アプリケーションの SAML ドキュメントを確認してください。
  これらを入力したら、 **[次へ]** をクリックして次の画面に進みます。 この画面には、Azure AD からの SAML トークンを受け入れられるようにアプリケーション側で構成する必要がある内容についての情報が示されます。 

![][5]

必要な値はアプリケーションによって異なるため、詳細についてはアプリケーションの SAML ドキュメントを参照してください。 **サインオン**および**サインアウト** サービス URL の両方が、Azure AD のインスタンスの SAML 要求処理エンドポイントである同じエンドポイントに解決されます。 発行者の URL は、アプリケーションに発行される SAML トークン内の "発行者" として表示される値です。 

アプリケーションを構成したら、**[次へ]** をクリックし、**[完了]** をクリックしてダイアログを閉じます。 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>SAML アプリケーションにユーザーとグループを割り当てる
SAML ベースの ID プロバイダーとして Azure AD を使用するようにアプリケーションを構成すれば、テストの準備はほぼできています。 セキュリティ コントロールの目的で、Azure AD では、ユーザーに Azure AD を使用するアクセス権が付与されない限り、アプリケーションへのサインインを許可するトークンは発行されません。 ユーザーには、直接、またはユーザーがメンバーであるグループを介してアクセス権が付与される場合があります。 

アプリケーションにユーザーまたはグループを割り当てるには、 **[ユーザーの割り当て]** ボタンをクリックします。 割り当てるユーザーまたはグループを選択してから、 **[割り当て]** ボタンをクリックします。 

![][6]

ユーザーを割り当てると、Azure AD でユーザーのトークンを発行できるようになり、ユーザーのアクセス パネルにこのアプリケーションのタイルも表示されるようになります。 アプリケーション タイルは、ユーザーが Office 365 を使用している場合、Office 365 アプリケーション ランチャーでも表示されます。 

アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>SAML トークンで発行された要求のカスタマイズ
アプリケーションに対するユーザーの認証時に、Azure AD は、一意に識別するユーザーに関する情報 (または要求) を含む SAML トークンをアプリに発行します。 既定では、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。 

**[属性]** タブで、アプリケーションに SAML トークンで送信された要求を表示または編集できます。 

![][7]

SAML トークンで発行された要求の編集が必要になる場合がある理由として&2; つが考えられます。1 つは、異なる要求 URL または要求値のセットを必要とするようにアプリケーションが作成されているという理由です。もう&1; つは、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名ともいう) 以外の名前にするために NameIdentifier 要求を必要とする方法でアプリケーションがデプロイされているという理由です。 

これらのシナリオの要求を追加および編集する方法の詳細については、この [要求のカスタマイズに関する記事](active-directory-saml-claims-customization.md)を参照してください。 

### <a name="testing-the-saml-application"></a>SAML アプリケーションのテスト
SAML URL と証明書が Azure AD とアプリケーションで構成されると、Azure でユーザーまたはグループはアプリケーションに割り当てられ、要求は確認され、必要に応じて編集されている状態です。したがって、ユーザーはアプリケーションへのサインインの準備はできています。 

テストを行うには、アプリケーションに割り当てたユーザー アカウントを使用して、https://myapps.microsoft.com で Azure AD アクセス パネルにサインインし、アプリケーションのタイルをクリックしてシングル サインオン プロセスを開始します。 または、アプリケーションのサインオン URL を直接参照し、そこからサインインすることもできます。 

デバッグのヒントについては、この [アプリケーションへの SAML ベースのシングル サインオンのデバッグ方法に関する記事](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>パスワード シングル サインオン
HTML サインイン ページがある Web アプリケーションの [パスワード ベースのシングル サインオン](active-directory-appssoaccess-whatis.md) を構成するには、このオプションを選択します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

**[次へ]**を選択すると、アプリケーションの Web ベースのサインイン ページの URL を入力するように求められます。 このページには、ユーザー名とパスワードの入力フィールドが含まれている必要があることに注意してください。 入力すると、Azure AD が、ユーザー名とパスワードを入力するためのサインイン ページを解析するプロセスを開始します。 プロセスが失敗した場合は、フィールドを手動でキャプチャできるようにするブラウザー拡張機能 (Internet Explorer、Chrome、または Firefox が必要) をインストールする代替プロセスが示されます。

サインイン ページをキャプチャしたら、ユーザーとグループを割り当てることができ、資格情報ポリシーは通常の [パスワード SSO アプリ](active-directory-appssoaccess-whatis.md)の場合と同様に設定できます。

注: アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。 

## <a name="existing-single-sign-on"></a>既存のシングル サインオン
組織の Azure AD アクセス パネルまたは Office 365 ポータルにアプリケーションへのリンクを追加するには、このオプションを選択します。 これを使用して、認証用に Azure AD の代わりに現在 Azure Active Directory フェデレーション サービス (または他のフェデレーション サービス) を使用しているカスタム Web アプリへのリンクを追加することができます。 または、ユーザーのアクセス パネルに表示するだけの特定の SharePoint ページまたは他の Web ページにディープ リンクを追加することもできます。 

**[次へ]**を選択すると、リンクするアプリケーションの URL を入力するように求められます。 入力したら、ユーザーとグループをアプリケーションに割り当てることができます。これにより、アプリケーションが [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)またはユーザーの [Azure AD アクセス パネル](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)に表示されるようになります。

注: アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

