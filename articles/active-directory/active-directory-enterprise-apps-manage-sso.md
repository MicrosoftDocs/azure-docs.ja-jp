---
title: "Azure Active Directory でのエンタープライズ アプリのシングル サインオン管理 | Microsoft Docs"
description: "Azure Active Directory アプリケーション ギャラリーから、組織内のエンタープライズ アプリケーションのシングル サインオン設定を管理します"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: 882180a553fffee05a612cf70ddd9a0f30108415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>エンタープライズ アプリのシングル サインオンの管理

この記事では、[Azure Portal](https://portal.azure.com) を使用してエンタープライズ アプリケーションのシングル サインオンの設定を管理する方法を説明します。 エンタープライズ アプリとは、組織内で使用されるデプロイ済みのアプリです。 この記事は、[Azure Active Directory アプリケーション ギャラリー](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)から追加されたアプリに特に当てはまります。 

## <a name="finding-your-apps-in-the-portal"></a>ポータルでアプリを検索する
シングル サインオン用に設定されているすべてのエンタープライズ アプリを、Azure Portal で表示し、管理できます。 これらのアプリケーションは、ポータルの **[その他のサービス]** &gt; **[エンタープライズ アプリケーション]** セクションで見つけることができます。 

![Enterprise Applications blade][1]

**[すべてのアプリケーション]** を選択して、構成されているすべてのアプリの一覧を表示します。 アプリを選択すると、そのアプリのリソースが表示され、アプリのレポートを表示したり、さまざまな設定を管理したりできます。

シングル サインオン設定を管理するには、 **[シングル サインオン]**を選択します。

![Application resource blade][2]

## <a name="single-sign-on-modes"></a>シングル サインオン モード
**[シングル サインオン]**ページの先頭には **[モード]** メニューがあり、これを使用してシングル サインオン モードを構成できます。 利用可能なオプションは、次のとおりです。

* **SAML ベースのサインオン** - Azure Active Directory に SAML 2.0 プロトコル、WS-Federation、OpenID Connect プロトコルのいずれかを組み合わせた完全なフェデレーション シングル サインオンがアプリケーションでサポートされている場合は、このオプションを使用できます。
* **パスワード ベースのサインオン** - Azure AD でこのアプリケーションのパスワード フォームの入力がサポートされている場合は、このオプションを使用できます。
* **リンクされたサインオン** - 以前は "既存のシングル サインオン" という名前でした。管理者は、このオプションを使用して、ユーザーの Azure AD アクセス パネルまたは Office 365 アプリケーション起動プログラムにこのアプリケーションへのリンクを配置できます。

これらのモードの詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

## <a name="saml-based-sign-on"></a>SAML ベースのサインオン
**[SAML ベースのサインオン]** オプションは、4 つのセクションに分かれています。

### <a name="domains-and-urls"></a>[Domains and URLs (ドメインと URL)]
ここでは、アプリケーションのドメインと URL に関する詳しい情報をすべて Azure AD ディレクトリに追加します。 アプリでシングル サインオンを機能させるために必要なすべての入力が画面に直接表示されます。**[詳細な URL 設定の表示]** チェック ボックスをオンにすると、オプションの入力をすべて表示できます。 サポートされている入力の完全な一覧は次のとおりです。

* **サインオン URL** - このアプリケーションにサインインするときにユーザーがアクセスする場所。 サービス プロバイダーによって開始されるシングル サインオンを実行するようにアプリケーションが構成されている場合は、ユーザーがこの URL を開くと、ユーザーの認証とサインインを行うためにサービス プロバイダーによって Azure AD にリダイレクトされます。 
  * このフィールドに URL が入力されている場合、Azure AD はその URL を使用して Office 365 と Azure AD アクセス パネルからアプリケーションを起動します。
  * このフィールドへの入力が省略されているときに、アプリケーションが Office 365、Azure AD アクセス パネル、または Azure AD シングル サインオン URL から起動された場合は、Azure AD が ID プロバイダーによって開始されたサインオンを実行します。
* **[識別子]** - この URI は、シングル サインオンの構成対象のアプリケーションを一意に識別する URI であることが必要です。 これは、SAML トークンの Audience パラメーターとして Azure AD からアプリケーションに返される値であり、アプリケーションではこの値を検証する必要があります。 また、この値はアプリケーションによって提供される SAML メタデータ内に Entity ID として表示されます。
* **[応答 URL]** - 応答 URL は、アプリケーションが SAML トークンを受け取ることになっている場所です。 これは Assertion Consumer Service (ACS) URL とも呼ばれています。 これらを入力したら、[次へ] をクリックして次の画面に進みます。 この画面には、Azure AD からの SAML トークンを受け入れられるようにアプリケーション側で構成する必要がある内容についての情報が示されます。
* **[リレー状態]** - リレー状態は省略可能なパラメーターです。認証が完了した後にユーザーをリダイレクトする場所をアプリケーションに指示できます。 通常、値はアプリケーションで有効な URL です。ただし、一部のアプリケーションでは、このフィールドを異なる方法で使用します (詳細については、アプリのシングル サインオンに関するドキュメントを参照してください)。 リレー状態の設定は、新しい Azure Portal に固有の新機能です。

### <a name="user-attributes"></a>[User Attributes (ユーザー属性)]
ここでは、ユーザーがサインインするたびに Azure AD がアプリケーションに発行する SAML トークンで送信される属性を確認および編集できます。

サポートされている編集可能な属性は、**[User Identifier (ユーザー識別子)]** 属性のみです。 この属性の値は、アプリケーション内の各ユーザーを一意に識別する Azure AD のフィールドです。 たとえば、ユーザー名と一意識別子として "Email address" を使用してアプリがデプロイされた場合、値は Azure AD の "user.mail" フィールドに設定されます。

### <a name="saml-signing-certificate"></a>[SAML Signing Certificate (SAML 署名証明書)]
このセクションには、ユーザーが認証するたびにアプリケーションに発行される SAML トークンに署名するために Azure AD が使用する証明書の詳細が表示されます。 有効期限の日付など、現在の証明書のプロパティを確認できます。

### <a name="application-configuration"></a>[Application Configuration (アプリケーションの構成)]
最後のセクションでは、Azure Active Directory を ID プロバイダーとして使用するようにアプリケーション自体を構成するために必要なドキュメントやコントロールを提供します。

**[アプリケーションの構成]** フライアウト メニューでは、アプリケーションを構成するための、新しい簡潔な組み込みの手順が提供されます。 これは、新しい Azure Portal に固有の新機能の 1 つです。

> [!NOTE]
> 組み込みのドキュメントの完全な例については、Salesforce.com アプリケーションを参照してください。 その他のアプリのドキュメントについては、随時追加されます。
> 
> 

![Embedded docs][3]

## <a name="password-based-sign-on"></a>パスワード ベースのサインオン
アプリケーションでサポートされている場合は、パスワードベースの SSO モードを選択し、 **[保存]** を選択すると、パスワードベースの SSO を実行するようにアプリケーションがすぐに構成されます。 パスワードベースの SSO のデプロイの詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

![パスワード ベースのサインオン][4]

## <a name="linked-sign-on"></a>Linked sign-on
アプリケーションでサポートされている場合は、リンクされた SSO モードを選択すると、このアプリでユーザーがクリックしたときに Azure AD アクセス パネルまたは Office 365 がリダイレクトする URL を入力できます。 リンクされた SSO (以前の "既存の SSO") の詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

![Linked sign-on][5]

##<a name="feedback"></a>フィードバック

改良された Azure AD エクスペリエンスを気に入っていただけることを期待しております。 ぜひフィードバックをお寄せください。 フィードバックや機能の向上についてのアイデアを、[フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)の **[管理ポータル]** セクションにご投稿ください。  マイクロソフトでは、優れた新しい機能を日々開発しています。ユーザーのアドバイスは、次に何を具体化し、どのように定義するかを考えるうえで非常に有用です。

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
