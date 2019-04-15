---
title: Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプション | Microsoft Docs
description: Azure Active Directory の事前統合アプリ用の SAML トークンの詳細な証明書署名オプションの使い方について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5548e7a5f60d9882fdfb4fb6eb777ab993e121
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916004"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory のギャラリー アプリ用の SAML トークンの詳細な証明書署名オプション

現在、Azure Active Directory (Azure AD) では、Azure Active Directory アプリ ギャラリーにある数千の事前統合アプリケーションをサポートしています。 500 以上のアプリケーションが、[NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) アプリケーションなどの [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 プロトコルを使用することでシングル サインオンをサポートしています。 お客様が Azure AD によって SAML を使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してお客様をサインインします。 これらの SAML トークンは、Azure AD で、特定の標準的なアルゴリズムで生成された一意の証明書で署名されます。

Azure AD では、ギャラリー アプリケーションにいくつかの既定の設定が使用されます。 これらの既定値はアプリケーションの要件に応じて設定されます。

Azure AD では、証明書署名オプションと証明書署名アルゴリズムを設定できます。

## <a name="certificate-signing-options"></a>証明書署名オプション

Azure AD では、次の 3 つの証明書署名オプションがサポートされています。

* **SAML アサーションへの署名**。 この既定のオプションは、ギャラリーのアプリケーションのほとんどに設定されます。 このオプションを選択すると、Azure AD が ID プロバイダー (IdP) として、アプリケーションの [X.509](https://wikipedia.org/wiki/X.509) 証明書を使って SAML アサーションと証明書に署名します。

* **SAML 応答の署名**。 このオプションを選択した場合、Azure AD が IdP として、アプリケーションの X.509 証明書を使って SAML 応答に署名します。

* **SAML 応答とアサーションへの署名**。 このオプションを選択した場合、Azure AD が IdP として、アプリケーションの X.509 証明書を使って SAML トークン全体に署名します。

## <a name="certificate-signing-algorithms"></a>証明書署名アルゴリズム

Azure AD では、SAML 応答に署名するための次の 2 つの署名アルゴリズム (セキュアハッシュ アルゴリズム (SHA)) がサポートされています。

* **SHA-256**。 Azure AD では、この既定のアルゴリズムを使用して、SAML 応答に署名します。 これは最新のアルゴリズムであり、SHA-1 よりも安全です。 ほとんどのアプリケーションで、SHA-256 アルゴリズムをサポートしています。 アプリケーションが署名アルゴリズムとして SHA-1 しかサポートしていない場合は、設定を変更することができます。 それ以外の場合、SAML 応答の署名には SHA-256 アルゴリズムの使用をお勧めします。

* **SHA-1**。 これは古いアルゴリズムであり、SHA-256 より安全性が低いと考えられています。 アプリケーションでこの署名アルゴリズムしかサポートされていない場合は、このオプションを **[署名アルゴリズム]** ドロップダウン リストから選択してください。 これで、Azure AD は SHA-1 アルゴリズムを使用して SAML 応答に署名します。

## <a name="change-the-certificate-signing-options-and-certificate-signing-algorithm"></a>証明書署名オプションと証明書署名アルゴリズムを変更する

アプリケーションの SAML 証明書署名オプションと証明書署名アルゴリズムを変更するには、対象のアプリケーションを選択します。

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)で、自分のアカウントにサインインします。 **Azure Active Directory 管理センター**のページが表示されます。
1. 左側のウィンドウで、**[エンタープライズ アプリケーション]** を選択します。 自分のアカウントのエンタープライズ アプリケーションの一覧が表示されます。
1. アプリケーションを選択します。 アプリケーションの概要ページが表示されます。

   ![アプリケーションの概要ページ](./media/certificate-signing-options/application-overview-page.png)

次に、そのアプリケーションの SAML トークンの証明書署名オプションを変更します。

1. アプリケーションの概要ページの左側のウィンドウで、**[シングル サインオン]** を選択します。

2. **[Set up Single Sign-On with SAML - Preview]\(SAML によるシングル サインオンの設定 - プレビュー\)** ページが表示されたら、手順 5 に進みます。

3. **[シングル サインオン方式の選択]** ページが表示されない場合は、**[シングル サインオン モードの変更]** を選択して表示します。

4. **[シングル サインオン方式の選択]** ページに **[SAML]** が表示されている場合は選択します。 (**[SAML]** が表示されていない場合、そのアプリケーションで SAML はサポートされていないため、残りの手順と記事は無視してもかまいません。)

5. **[Set up Single Sign-On with SAML - Preview]\(SAML によるシングル サインオンの設定 - プレビュー\)** ページで、**[SAML 署名証明書]** 見出しを探し、**[編集]** アイコン (鉛筆) を選択します。 **[SAML 署名証明書]** ページが表示されます。

   ![SAML の署名のページ](./media/certificate-signing-options/saml-signing-page.png)

6. **[署名オプション]** ドロップダウン リストで、**[SAML 応答への署名]**、**[SAML アサーションへの署名]**、または **[SAML 応答とアサーションへの署名]** を選択します。 これらのオプションの説明は、この記事の前半の「[証明書署名オプション](#certificate-signing-options)」で紹介しています。

7. **[署名アルゴリズム]** ドロップダウン リストで、**[SHA-1]** または **[SHA-256]** を選択します。 これらのオプションの説明は、この記事の前半の「[証明書署名アルゴリズム](#certificate-signing-algorithms)」で紹介しています。

8. 選択内容に問題がなければ、**[保存]** を選択して新しい SAML 署名証明書の設定を適用します。 それ以外の場合は、**[X]** を選択して変更を破棄します。

## <a name="next-steps"></a>次の手順

* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML に基づいたシングル サインオンのトラブルシューティング](../develop/howto-v1-debug-saml-sso-issues.md)
