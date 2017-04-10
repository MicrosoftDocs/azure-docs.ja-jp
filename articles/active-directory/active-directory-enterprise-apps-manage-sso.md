---
title: "Azure Active Directory プレビューでのエンタープライズ アプリケーションのシングル サインオン管理 | Microsoft Docs"
description: "Azure Active Directory を使用してエンタープライズ アプリケーションのシングル サインオンを管理する方法について説明します"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2f03079498568f52802b34ce57242a414e648fe3


---
# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>プレビュー: 新しい Azure Portal でエンタープライズ アプリケーションのシングル サインオンを管理する
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-enterprise-apps-manage-sso.md)
> * [Azure クラシック ポータル](active-directory-sso-integrate-saas-apps.md)
> 
> 

この記事では、[Azure Portal](https://portal.azure.com) を使用して、アプリケーション (特に [Azure Active Directory (Azure AD) アプリケーション ギャラリー](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)から追加されたアプリケーション) のシングル サインオン設定を管理する方法について説明します。 Azure AD のシングル サインオンの管理エクスペリエンスは現在パブリック プレビュー中です。この記事では、新機能と、プレビュー期間中のみに適用されるいくつかの一時的な制限について説明します。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md)

## <a name="finding-your-apps-in-the-new-portal"></a>新しいポータルでアプリを検索する
2016 年 9 月の時点で、Azure Portal では、ディレクトリ管理者が [Azure クラシック ポータル](https://manage.windowsazure.com)内の [Azure Active Directory アプリケーション ギャラリー](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)を使用してディレクトリでのシングル サインオンを構成したすべてのアプリケーションを表示および管理できます。

これらのアプリケーションは、[Azure Portal](https://portal.azure.com) の **[エンタープライズ アプリケーション]** セクション (ポータルの **[More Services (その他のサービス)]** の一覧からアクセスできます) で見つけることができます。 エンタープライズ アプリケーションとは、組織のユーザーが使用するデプロイ済みのアプリケーションです。

![Enterprise Applications blade][1]

**[すべてのアプリケーション]** を選択すると、ギャラリーから追加されたアプリを含め、構成済みのアプリの一覧が表示されます。 アプリを選択すると、そのアプリのリソース ブレードが読み込まれます。リソース ブレードでは、そのアプリのレポートを表示することや、さまざまな設定を管理することができます。

シングル サインオン設定を管理するには、 **[シングル サインオン]**を選択します。

![Application resource blade][2]

## <a name="single-sign-on-modes"></a>シングル サインオン モード
**[シングル サインオン]** ブレードの先頭には **[モード]** メニューがあり、シングル サインオン モードを構成できます。 利用可能なオプションは、次のとおりです。

* **[SAML-based sign on (SAML ベースのサインオン)]** - アプリケーションで Azure Active Directory と SAML 2.0 プロトコルを使用した完全なフェデレーション シングル サインオンがサポートされている場合、このオプションを使用できます。
* **[Password-based sign on (パスワードベースのサインオン)]** - Azure AD でこのアプリケーションのパスワード フォームの入力がサポートされている場合、このオプションを使用できます。
* **[リンクされたサインオン]** - 以前は "既存のシングル サインオン" という名前でした。このオプションを使用すると、ユーザーの Azure AD アクセス パネルまたは Office 365 アプリケーション起動プログラムにこのアプリケーションへのリンクを配置できます。

これらのモードの詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

## <a name="saml-based-sign-on"></a>[SAML-based sign on (SAML ベースのサインオン)]
**[SAML-based sign on (SAML ベースのサインオン)]** オプションでは、4 つのセクションに分かれたブレードが表示されます。

### <a name="domains-and-urls"></a>[Domains and URLs (ドメインと URL)]
ここでは、アプリケーションのドメインと URL に関する詳しい情報をすべて Azure AD ディレクトリに追加します。 アプリでシングル サインオンを機能させるために必要なすべての入力が画面に直接表示されます。**[詳細な URL 設定の表示]** チェック ボックスをオンにすると、オプションの入力をすべて表示できます。 サポートされている入力の完全な一覧は次のとおりです。

* **[サインオン URL]** - このアプリケーションにサインインするときにユーザーがアクセスする場所。 サービス プロバイダーによって開始されるシングル サインオンを実行するようにアプリケーションが構成されている場合は、ユーザーがこの URL に移動すると、認証とサインインを行うために、そのユーザーはサービス プロバイダーによって Azure AD にリダイレクトされます。 このフィールドに URL が入力されている場合、Azure AD はその URL を使用して Office 365 と Azure AD アクセス パネルからアプリケーションを起動します。 このフィールドへの入力が省略されている場合、Azure AD は、アプリケーションが Office 365、Azure AD アクセス パネル、または Azure AD シングル サインオン URL から起動されたときに、ID プロバイダーによって開始されるサインオンを実行します。
* **[識別子]** - この URI は、シングル サインオンの構成対象のアプリケーションを一意に識別する URI であることが必要です。 これは、SAML トークンの Audience パラメーターとして Azure AD からアプリケーションに返される値であり、アプリケーションではこの値を検証する必要があります。 また、この値はアプリケーションによって提供される SAML メタデータ内に Entity ID として表示されます。
* **[応答 URL]** - 応答 URL は、アプリケーションが SAML トークンを受け取ることになっている場所です。 これは Assertion Consumer Service (ACS) URL とも呼ばれています。 これらを入力したら、[次へ] をクリックして次の画面に進みます。 この画面には、Azure AD からの SAML トークンを受け入れられるようにアプリケーション側で構成する必要がある内容についての情報が示されます。
* **[リレー状態]** - リレー状態は省略可能なパラメーターです。認証が完了した後にユーザーをリダイレクトする場所をアプリケーションに指示できます。 通常、値はアプリケーションで有効な URL です。ただし、一部のアプリケーションでは、このフィールドを異なる方法で使用します (詳細については、アプリのシングル サインオンに関するドキュメントを参照してください)。 リレー状態の設定は、新しい Azure Portal に固有の新機能です。

### <a name="user-attributes"></a>[User Attributes (ユーザー属性)]
ここでは、ユーザーがサインインするたびに Azure AD がアプリケーションに発行する SAML トークンで送信される属性を確認および編集できます。

最初のプレビュー リリースでは、サポートされている編集可能な属性は、 **[User Identifier (ユーザー識別子)]** 属性のみです。 この属性の値は、アプリケーション内の各ユーザーを一意に識別する Azure AD のフィールドです。 たとえば、ユーザー名と一意識別子として "Email address" を使用してアプリがデプロイされた場合、値は Azure AD の "user.mail" フィールドに設定されます。

後のプレビューでは、追加の属性の編集がサポートされる予定です。

### <a name="saml-signing-certificate"></a>[SAML Signing Certificate (SAML 署名証明書)]
このセクションには、ユーザーが認証するたびにアプリケーションに発行される SAML トークンに署名するために Azure AD が使用する証明書の詳細が表示されます。 有効期限の日付など、現在の証明書のプロパティを確認できます。

証明書のロールオーバーおよび追加の証明書オプションの管理機能は、後のプレビュー リリースでサポートされる予定です。 証明書の完全な管理は、 [Azure クラシック ポータル](active-directory-sso-certs.md)で引き続き実行できます。

### <a name="application-configuration"></a>[Application Configuration (アプリケーションの構成)]
最後のセクションでは、Azure Active Directory を ID プロバイダーとして使用するようにアプリケーション自体を構成するために必要なドキュメントやコントロールを提供します。

**[アプリケーションの構成]** フライアウト メニューでは、アプリケーションを構成するための、新しい簡潔な組み込みの手順が提供されます。 これは、新しい Azure Portal に固有の新機能の 1 つです。

> [!NOTE]
> 組み込みのドキュメントの完全な例については、Salesforce.com アプリケーションを参照してください。 その他のアプリのドキュメントについては、プレビュー期間中に随時追加されます。
> 
> 

![Embedded docs][3]

## <a name="password-based-sign-on"></a>[Password-based sign on (パスワードベースのサインオン)]
アプリケーションでサポートされている場合は、パスワードベースの SSO モードを選択し、 **[保存]** を選択すると、パスワードベースの SSO を実行するようにアプリケーションがすぐに構成されます。 パスワードベースの SSO のデプロイの詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

![[Password-based sign on (パスワードベースのサインオン)]][4]

## <a name="linked-sign-on"></a>リンクされたサインオン
アプリケーションでサポートされている場合は、リンクされた SSO モードを選択すると、このアプリでユーザーがクリックしたときに Azure AD アクセス パネルまたは Office 365 がリダイレクトする URL を入力できます。 リンクされた SSO (以前の "既存の SSO") の詳細については、「 [Azure Active Directory によるシングル サインオンのしくみ](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」を参照してください。

![Linked sign-on][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG



<!--HONumber=Feb17_HO3-->


