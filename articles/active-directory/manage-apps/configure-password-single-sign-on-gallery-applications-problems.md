---
title: Azure AD ギャラリー アプリケーションのパスワード シングル サインオンを構成する場合の問題 | Microsoft Docs
description: Azure AD アプリケーション ギャラリーに既に表示されているアプリケーションのパスワード シングル サインオンを構成する場合に直面する一般的な問題について説明します
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
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6c313cc654461ea22c126fede317ed230408b82
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079198"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションのパスワード シングル サインオンを構成する場合の問題

この記事では、Azure AD ギャラリー アプリケーションで**パスワード シングル サインオン**を構成するときに直面する一般的な問題について説明します。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>資格情報を入力したが、拡張機能によって送信されない

この問題は通常、アプリケーション ベンダーがサインイン ページを最近変更して、フィールドを追加したり、ユーザー名とパスワードのフィールドを検出するために使用されていた識別子を変更したり、アプリケーションのサインイン エクスペリエンスのしくみを変更したりした場合に発生します。 多くの場合は、Microsoft がアプリケーション ベンダーと協力して、これらの問題を迅速に解決することができます。

Microsoft には、統合の破綻を自動的に検出するテクノロジがありますが、このような問題をすぐに発見できなかったり、修正に時間がかかったりする場合があります。 このような統合が正しく機能しない場合は、サポート ケースを開いていただければ、できるだけ早く対処いたします。

**このアプリケーションのベンダーに連絡する**場合は、アプリケーションを Azure Active Directory にネイティブに統合できるように、Microsoft と連係するよう伝えてください。 連係を開始するために、「[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](../develop/howto-app-gallery-listing.md)」をベンダーに参照してもらいます。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>資格情報を入力して送信したが、ページには資格情報が正しくないと表示される

この問題を解決するには、まず次のことを試してください。

- まずユーザーに、格納されている資格情報で**アプリケーション Web サイトに直接サインイン**してみてもらいます。

  * サインインできた場合は、[アプリケーション アクセス パネル](https://myapps.microsoft.com/)の **[アプリ]** セクションにある**アプリケーション タイル**の **[資格情報の更新]** ボタンをユーザーにクリックしてもらい、動作している最新の既知のユーザー名とパスワードに資格情報を更新します。

  * 自分または別の管理者がこのユーザーに資格情報を割り当てた場合は、アプリケーションの **[Users & Groups (ユーザーとグループ)]** タブに移動してユーザーまたはグループのアプリケーション割り当てを見つけ、割り当てを選択してから **[資格情報の更新]** ボタンをクリックします。

- ユーザーが自分の資格情報を割り当てた場合は、ユーザーに**パスワードがアプリケーションで期限切れになっていないことを確認**してもらい、期限切れになっている場合はアプリケーションに直接サインインして、**期限切れになっているパスワードを更新**してもらいます。

  * アプリケーションでパスワードが更新された後で、[アプリケーション アクセス パネル](https://myapps.microsoft.com/)の **[アプリ]** セクションにある**アプリケーション タイル**の **[資格情報の更新]** ボタンをユーザーにクリックしてもらい、動作している最新の既知のユーザー名とパスワードに資格情報を更新します。

  * 自分または別の管理者がこのユーザーに資格情報を割り当てた場合は、アプリケーションの **[Users & Groups (ユーザーとグループ)]** タブに移動してユーザーまたはグループのアプリケーション割り当てを見つけ、割り当てを選択してから **[資格情報の更新]** ボタンをクリックします。

- 以下の「[アクセス パネルのブラウザー拡張機能をインストールする方法](#how-to-install-the-access-panel-browser-extension)」セクションの手順に従って、アクセス パネルのブラウザー拡張機能をユーザーに更新してもらいます。

- ユーザーのブラウザーで、アクセス パネルのブラウザー拡張機能が実行され、有効になっていることを確認します。

- ユーザーが **incognito、inPrivate、または Private モード**でアクセス パネルからアプリケーションにサインインしようとしていないことを確認します。 アクセス パネルの拡張機能は、これらのモードではサポートされていません。

これでうまくいかない場合は、アプリケーション側で変更が行われ、アプリケーションと Azure AD との統合が一時的に破綻している可能性があります。 たとえば、アプリケーション ベンダーがページに導入したスクリプトの動作が手動入力と自動入力で異なることが原因で、Azure AD との場合のような自動化された統合が破綻することがあります。 多くの場合は、Microsoft がアプリケーション ベンダーと協力して、これらの問題を迅速に解決することができます。

Microsoft には、アプリケーションの統合の破綻を自動的に検出するテクノロジがありますが、このような問題をすぐに発見できなかったり、修正に時間がかかったりする場合があります。 統合が正常に機能しないときは、サポート ケースを開いていただければ、できるだけ早く対処いたします。 

さらに、**このアプリケーションのベンダーに連絡する**場合は、アプリケーションを Azure Active Directory にネイティブに統合できるように、**Microsoft と連係するようお伝えください**。 連係を開始するために、「[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](../develop/howto-app-gallery-listing.md)」をベンダーに参照してもらいます。

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>拡張機能が Chrome と Firefox で機能するが、Internet Explorer で機能しない

この問題には、次の 2 つの主な原因があります。

- Internet Explorer で有効になっているセキュリティ設定によっては、Web サイトが**信頼済みゾーン**の一部ではない場合、アプリケーションのスクリプトの実行がブロックされることがあります。

  *  この問題を解決するには、**Internet Explorer のセキュリティ設定**で **[信頼済みサイト]** の一覧に**アプリケーションの Web サイトを追加する**ようユーザーに指示します。 詳細な手順については、[サイトを信頼済みサイトの一覧に追加する方法](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5)に関する記事をユーザーに参照してもらいます。

- まれに、Internet Explorer のセキュリティ検証のために、ページの読み込みがスクリプトの実行よりも遅くなる場合があります。

  * 残念ながら、このような状況は、ブラウザーのバージョン、コンピューターの速度、またはアクセスしたサイトによって異なります。 この場合は、この特定のアプリケーションの統合を修正できるように、サポートに問い合わせることをお勧めします。

さらに、**このアプリケーションのベンダーに連絡する**場合は、アプリケーションを Azure Active Directory にネイティブに統合できるように、**Microsoft と連係するようお伝えください**。 連係を開始するために、「[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](../develop/howto-app-gallery-listing.md)」をベンダーに参照してもらいます。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>アプリケーションのログイン ページが最近変更されたり、追加のフィールドが必要になったりしたかどうかを確認する

アプリケーションのログイン ページが大幅に変更されると、それによって統合が破綻することがあります。 たとえば、アプリケーション ベンダーがエクスペリエンスにサインイン フィールド、CAPTCHA、または多要素認証を追加した場合です。 多くの場合は、Microsoft がアプリケーション ベンダーと協力して、これらの問題を迅速に解決することができます。

Microsoft には、アプリケーションの統合の破綻を自動的に検出するテクノロジがありますが、このような問題をすぐに発見できなかったり、修正に時間がかかったりする場合があります。 統合が正常に機能しないときは、サポート ケースを開いていただければ、できるだけ早く対処いたします。 

さらに、**このアプリケーションのベンダーに連絡する**場合は、アプリケーションを Azure Active Directory にネイティブに統合できるように、**Microsoft と連係するようお伝えください**。 連係を開始するために、「[Azure Active Directory アプリケーション ギャラリーでのアプリケーションの表示](../develop/howto-app-gallery-listing.md)」をベンダーに参照してもらいます。

## <a name="how-to-install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能をインストールする方法

アクセス パネルのブラウザー拡張機能をインストールするには、次の手順に従います。

1.  サポートされている任意のブラウザーで、[アクセス パネル](https://myapps.microsoft.com)を開き、Azure AD の**ユーザー**としてサインインします。

2.  アクセス パネルで、**パスワード SSO アプリケーション**をクリックします。

3.  ソフトウェアのインストールを確認するプロンプトで、**[今すぐインストール]** を選択します。

4.  お使いのブラウザーに基づいて、ダウンロード リンクが表示されます。 お使いのブラウザーに拡張機能を**追加**します。

5.  お使いのブラウザーに確認メッセージが表示されたら、拡張機能を **[有効にする]** または **[許可する]** のいずれかを選択します。

6.  インストールが完了したら、ブラウザー セッションを**再起動**します。

7.  アクセス パネルにサインインし、パスワード SSO アプリケーションを**起動**できるかどうかを確認します。

以下のダイレクト リンクから、Chrome および Firefox 対応の拡張機能をダウンロードすることもできます。

-   [Chrome アクセス パネル拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox アクセス パネル拡張機能](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>次の手順
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](application-proxy-configure-single-sign-on-with-kcd.md)

