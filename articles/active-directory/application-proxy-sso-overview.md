---
title: "Azure AD アプリケーション プロキシの SSO を管理する | Microsoft Docs"
description: "アプリケーション プロキシを使用したシングル サインオンの基本について説明します"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1deb3d91049d45fe26791783e13bd23e0a7d9f95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Azure AD アプリケーション プロキシを使用したシングル サインオンの提供

シングル サインオンは、Azure AD アプリケーション プロキシの重要な要素です。  ユーザーに必要なのはクラウドの Azure Active Directory にサインインすることだけであるため、最善のユーザー エクスペリエンスが提供されます。 Azure Active Directory で認証された後、アプリケーション プロキシ コネクタがオンプレミスのアプリケーションでの認証を処理します。 バックエンド アプリケーションは、アプリケーション プロキシを通してサインインしているリモート ユーザーとドメインに参加しているデバイスでの通常使用の違いを見分けることはできません。 

アプリケーションへのシングル サインオンで Azure Active Directory を使用するには、事前認証方法として **Azure Active Directory** を選択する必要があります。 **パススルー**を選択した場合、ユーザーは Azure Active Directory での認証は行われず、アプリケーションに直接アクセスします。 この設定は、アプリケーションを初めて公開するときに構成できます。または、Azure ポータルでアプリケーションに移動し、アプリケーション プロキシの設定を編集できます。 

シングル サインオン オプションを表示するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[Azure Active Directory]**  > **[エンタープライズ アプリケーション]**  > **[すべてのアプリケーション]** に移動します。
3. シングル サインオン オプションを管理するアプリを選択します。
4. **[シングル サインオン]** を選択します。

   ![SSO ドロップダウン メニュー](./media/application-proxy-sso-overview/single-sign-on-mode.png)

このドロップダウン メニューには、アプリケーションにシングル サインオンするための次の 5 つのオプションが表示されます。

* Azure AD シングル サインオンが無効
* パスワード ベースのサインオン
* リンクされたサインオン
* 統合 Windows 認証
* ヘッダーベースのサインオン

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD シングル サインオンが無効

アプリケーションへのシングル サインオンで Azure Active Directory の統合を使用しない場合は、**[Azure AD シングル サインオンが無効]** を選択します。 このオプションを選択した場合、ユーザーは 2 回認証される可能性があります。 最初に Azure Active Directory で認証され、次にアプリケーション自体にサインインするときに認証されます。 

オンプレミスのアプリケーションではユーザーの認証を必要としないが、リモート アクセスに対するセキュリティ レイヤーとして Azure Active Directory を追加する場合は、このオプションをお勧めします。 

## <a name="password-based-sign-on"></a>パスワード ベースのサインオン

オンプレミスのアプリケーションのパスワード コンテナーとして Azure Active Directory を使用する場合は、**[パスワード ベースのサインオン]** を選択します。 アプリケーションがアクセス トークンまたはヘッダーの代わりにユーザー名とパスワードの組み合わせを使用して認証する場合は、このオプションをお勧めします。 パスワード ベースのサインオンでは、ユーザーは、アプリケーションの初回のアクセス時にサインインする必要があります。 その後、Azure Active Directory が、ユーザーに代わってユーザー名とパスワードを提供します。 

パスワード ベースのサインオンの設定の詳細については、「[アプリケーション プロキシを使用したシングル サインオン用のパスワードの保管](application-proxy-sso-azure-portal.md)」を参照してください。

## <a name="linked-sign-on"></a>リンクされたサインオン

オンプレミスの ID に対してシングル サインオン ソリューションを既に設定している場合は、**[リンクされたサインオン]** を選択します。 このオプションは、Azure Active Directory が既存の SSO ソリューションを利用して、ユーザーがアプリケーションにリモート アクセスできるようにします。 

リンクされたサインオン (公式には既存のシングル サインオンとして知られています) の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)」をご覧ください。

## <a name="integrated-windows-authentication"></a>統合 Windows 認証

オンプレミスのアプリケーションが統合 Windows 認証 (IWA) を使用している場合、またはシングル サインオンで Kerberos の制約付き委任 (KCD) を使用する場合は、**[統合 Windows 認証]** を選択します。 このオプションでは、ユーザーは Azure Active Directory の認証だけが必要であり、認証後は、アプリケーション プロキシ コネクタがユーザーに代わって Kerberos トークンを取得してアプリケーションにサインインします。 

統合 Windows 認証の設定の詳細については、[アプリケーション プロキシを使用したシングル サインオンでの Kerberos の制約付き委任](active-directory-application-proxy-sso-using-kcd.md)に関する記事を参照してください。

## <a name="header-based-sign-on"></a>ヘッダーベースのサインオン 

アプリケーションが認証用のヘッダーを使用する場合は、**[ヘッダーベースのサインオン]** を選択します。 このオプションでは、ユーザーに必要なのは、Azure Active Directory で認証されることだけです。 PingAccess という名前のサード パーティの認証サービスが Microsoft パートナーによって提供されています。このサービスは、Azure Active Directory のアクセス トークンをアプリケーションのヘッダー形式に変換します。 

ヘッダーベースのサインオンの設定の詳細については、[アプリケーション プロキシを使用したシングル サインオンでの ヘッダー ベースの認証](application-proxy-ping-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [アプリケーション プロキシを使用したシングル サインオンでのパスワードの保管](application-proxy-sso-azure-portal.md)
- [アプリケーション プロキシを使用したシングル サインオンでの Kerberos の制約付き委任](active-directory-application-proxy-sso-using-kcd.md)
- [アプリケーション プロキシを使用したシングル サインオンでのヘッダーベースの認証](application-proxy-ping-access.md) 
