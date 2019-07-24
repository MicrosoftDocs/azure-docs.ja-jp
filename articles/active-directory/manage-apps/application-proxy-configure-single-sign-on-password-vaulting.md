---
title: Azure AD アプリケーション プロキシを使用したアプリへのシングル サインオン | Microsoft Docs
description: Azure Portal で Azure AD アプリケーション プロキシを使用してオンプレミス アプリケーションへのシングルサインオンを有効にします。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2339ce23379ab51090d536ba071a42017f6941b5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174182"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>アプリケーション プロキシを使用したシングル サインオン用のパスワードの保管

Azure Active Directory アプリケーション プロキシを使用してオンプレミス アプリケーションを発行して、遠隔地の従業員がオンプレミス アプリケーションに安全にアクセスできるようにすることで生産性を上げることができます。 Azure Portal で、これらのアプリに対するシングル サインオン (SSO) を設定することもできます。 ユーザーは、Azure AD で認証されれば、もう一度サインインしなくても、エンタープライズ アプリケーションにアクセスできるようになります。

アプリケーション プロキシは、さまざまな[シングル サインオン モード](what-is-single-sign-on.md#choosing-a-single-sign-on-method)をサポートします。 パスワード ベースのサインオンは、ユーザー名とパスワードの組み合わせを使用して認証を行うアプリケーションを対象としています。 アプリケーションでパスワード ベースのサインオンを構成すると、ユーザーは、オンプレミスのアプリケーションに 1 度だけサインインする必要があります。 その後、Azure Active Directory はサインイン情報を格納し、ユーザーがアプリケーションにリモートでアクセスしたときにサインイン情報をアプリケーションに自動的に提供します。 

アプリケーション プロキシを使用したアプリの発行とテストを既に行っている必要があります。 そうでない場合は、「[Azure AD アプリケーション プロキシを使用したアプリの発行](application-proxy-add-on-premises-application.md)」の手順に従った後、この記事に戻ってください。 

## <a name="set-up-password-vaulting-for-your-application"></a>アプリケーションのパスワード保管の設定

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
3. 一覧から、SSO を設定するアプリを選択します。  
4. **[シングル サインオン]** を選択します。

   ![[シングル サインオン] の選択](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. SSO モードとして、**[パスワード ベースのサインオン]** を選択します。
6. [サインオン URL] に、ユーザーが企業ネットワークの外部からアプリにサインインするためのユーザー名とパスワードを入力するページの URL を入力します。 これは、アプリケーション プロキシを使用してアプリを発行したときに作成した外部 URL にすることができます。 

   ![パスワード ベースのサインオンの選択と URL の入力](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. **[保存]** を選択します。

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>アプリをテストする

アプリケーションへのリモート アクセス用に構成した外部 URL に移動します。 そのアプリの資格情報 (または設定したアクセス権を持つテスト アカウントの資格情報) でサインインします。 正常にサインインした場合は、アプリを終了すると、資格情報をもう一度入力しなくても前の場所に戻ることができます。 

## <a name="next-steps"></a>次の手順

- [シングル サインオン](what-is-single-sign-on.md)を実装するための他の方法を参照します。
- [Azure AD アプリケーション プロキシを使用したアプリへのリモート アクセス時のセキュリティに関する注意事項](application-proxy-security.md)を確認します。
