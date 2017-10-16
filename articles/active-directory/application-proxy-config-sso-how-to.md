---
title: "アプリケーション プロキシ アプリケーションに対するシングル サインオンを構成する方法 | Microsoft Docs"
description: "アプリケーション プロキシ アプリケーションに対するシングル サインオンを迅速に構成する方法"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ccab427857b1439f37f3d9f193e35a4fc2237014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションに対するシングル サインオンを構成する方法

シングル サインオン (SSO) を導入すると、ユーザーに何度も認証することなく、アプリケーションにアクセスできるようになります。 クラウド内で Azure Active Directory に対して 1 回認証を行えば、それ以降アプリケーションから追加で認証要求があったとしても、サービスまたはコネクタがユーザーに代わって本人性を証明します。

## <a name="how-to-configure-single-sign-on"></a>シングル サインオンの構成方法
SSO を構成するにはまず、Azure Active Directory で事前認証を行うようにアプリケーションを構成する必要があります。 そのためには、**[Azure Active Directory]** -&gt; **[エンタープライズ アプリケーション]** -&gt; **[すべてのアプリケーション]** -&gt; <目的のアプリケーション> -&gt; **[アプリケーション プロキシ]** の順に移動します。 このページに [事前認証] フィールドがあるので、それを [Azure Active Directory] に設定してください。 

個々の事前認証方法の詳細については、[アプリの発行に関するドキュメント](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)の手順 4. を参照してください。

   ![Azure Portal の事前認証方法](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>アプリケーション プロキシ アプリケーションに使用するシングル サインオン モードの構成
次に、具体的なシングル サインオンの種類を構成します。 サインオン方法は、バックエンド アプリケーションで使用される認証の種類に基づいて分類されます。 アプリケーション プロキシ アプリケーションは、次の 3 種類のサインオンに対応します。

-   **[パスワード ベースのサインオン]**: サインオンにユーザー名フィールドとパスワード フィールドを使うアプリケーションには、パスワード ベースのサインオンを使用できます。 構成手順については、[パスワード SSO の構成に関するドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#bring-your-own-password-sso-applications)を参照してください。

-   **[統合 Windows 認証]**: 統合 Windows 認証 (IWA) を使うアプリケーションでは、Kerberos の制約付き委任 (KCD) を通じてシングル サインオンが実現されます。 この場合 Active Directory において、見かけ上ユーザーとして振る舞い、本人に代わってトークンを送受信する権限がアプリケーション プロキシ コネクタに与えられます。 KCD の構成の詳細については、[KCD によるシングル サインオンに関するドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)を参照してください。

-   **[ヘッダーベースのサインオン]**: ヘッダーベースのサインオンはパートナーシップを通じて実現されるものであり、特別な構成は不要です。 パートナーシップに関する詳しい情報と、認証にヘッダーを使うアプリケーションに対してシングル サインオンを構成する具体的な手順については、[PingAccess for Azure AD のドキュメント](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access)を参照してください。

上記の各オプションは、[エンタープライズ アプリケーション] で目的のアプリケーションに移動し、左側のメニューで **[シングル サインオン]** ページを開くと表示されます。 そのアプリケーションが以前のポータルで作成されたものである場合は一部のオプションが表示されない可能性があルことに注意してください。

このページには、さらに [リンクされたサインオン] というオプションも表示されます。 このオプションもアプリケーション プロキシでサポートされています。 ただしこのオプションは、アプリケーションにシングル サインオンを追加するものではありません。 とは言うものの、アプリケーションには、Active Directory フェデレーション サービスなどの別のサービスを使って既にシングル サインオンが実装されている場合があります。 

管理者はこのオプションを選択することで、ユーザーがアプリケーションにアクセスしたときに最初に目にする、アプリケーションへのリンクを作成することができます。 たとえば、Active Directory Federation Services 2.0 を使用してユーザーを認証するように構成されたアプリケーションがある場合、管理者は、[リンクされたサインオン] オプションを使用して、アクセス パネルにそのアプリケーションへのリンクを作成することができます。

## <a name="next-steps"></a>次のステップ
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](active-directory-application-proxy-sso-using-kcd.md)
