---
title: パスワード シングル サインオン用に構成された、Azure AD ギャラリー アプリケーションにサインインできない問題 | Microsoft Docs
description: パスワード シングル サインオン用に構成された Azure AD ギャラリー アプリケーションへのサインインに関連する問題をトラブルシューティングするガイダンスを提供する問題領域について説明します
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11411a780333268e19d53ea2bd472fe36bc0eb62
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088368"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>パスワード シングル サインオン用に構成された、Azure AD ギャラリー アプリケーションにサインインできない問題

Web ベースのポータルであるアクセス パネルを使用すると、Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーは、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。 アクセス パネルは Azure Portal から独立していて、ユーザーが Azure サブスクリプションを持っている必要はありません。

アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>アクセス パネルのブラウザーの要件を満たす

アクセス パネルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

パスワードベースの SSO の場合、エンド ユーザーのブラウザーには次のいずれかを使用できます。

-   Internet Explorer 8、9、10、11 - Windows 7 以降

-   Chrome - Windows 7 以降、MacOS X 以降

-   Firefox 26.0 以降 - Windows XP SP2 以降、Mac os X 10.6 以降

>[!NOTE]
>パスワードベースの SSO の拡張機能は、ブラウザーの拡張機能が Windows 10 の Microsoft Edge でサポートされるようになると、Microsoft Edge で使用できるようになります。
>
>

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

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer のグループ ポリシーの設定

Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールできるようにグループ ポリシーを設定できます。

前提条件は次のとおりです。

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。

-   グループ ポリシー オブジェクト (GPO) を編集するには、"設定の編集" アクセス許可が必要です。 既定でこのアクセス許可を持っているセキュリティ グループのメンバーはDomain Administrators、Enterprise Administrators、Group Policy Creator Owners です。 [詳細情報](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

グループ ポリシーを構成し、ユーザーにデプロイする方法の詳しい手順については、「[グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)」チュートリアルを参照してください。

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Internet Explorer でアクセス パネルをトラブルシューティングする

診断ツールへのアクセスと IE 対応の拡張機能を構成する方法の詳しい手順については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)」ガイドを参照してください。

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>ギャラリー以外のアプリケーションのパスワード シングル サインオンを構成する方法

Azure AD ギャラリーからアプリケーションを構成するには、以下の手順を実行する必要があります。

-   [ギャラリー以外のアプリケーションを追加する](#add-a-non-gallery-application)

-   [パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する](#configure-the-application-for-password-single-sign-on)

-   [アプリケーションにユーザーを割り当てる](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>ギャラリー以外のアプリケーションを追加する

Azure AD ギャラリーからアプリケーションを追加するには、次の手順に従います。

1.  [Azure Portal](https://portal.azure.com) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[エンタープライズ アプリケーション]** ウィンドウの右上隅にある **[追加]** ボタンをクリックします。

6.  **[ギャラリー以外のアプリケーション]** をクリックします。

7.  **[名前]** ボックスに、アプリケーションの名前を入力します。 **[追加]** を選択します。

しばらくすると、アプリケーションの構成ウィンドウが表示されます。

### <a name="configure-the-application-for-password-single-sign-on"></a>パスワード シングル サインオンに対応するようにアプリケーションを構成する

アプリケーションのシングル サインオンを構成するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. シングル サインオンを構成するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8. **[パスワード ベースのサインオン]** モードを選択します。

9. **サインオン URL** を入力します。 これは、ユーザーが自分のユーザー名とパスワードを入力してサインインする URL です。 この URL にサインイン フィールドが表示されていることを確認します。

10. アプリケーションにユーザーを割り当てます。

11. さらに、ユーザーの行を選び、**[資格情報の更新]** をクリックしてユーザーに代わってユーザー名とパスワードを入力すると、ユーザーに代わって資格情報を提供することもできます。 そうしないと、起動時に自分で資格情報を入力するように求めるプロンプトがユーザーに表示されます。

### <a name="assign-users-to-the-application"></a>アプリケーションにユーザーを割り当てる

1 人以上のユーザーをアプリケーションに直接割り当てるには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6. ユーザーを割り当てるアプリケーションを一覧から選びます。

7. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[ユーザーとグループ]** をクリックします。

8. **[ユーザーとグループ]** 一覧の上部にある **[追加]** ボタンをクリックして、**[割り当ての追加]** ウィンドウを開きます。

9. **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターをクリックします。

10. 割り当てる対象のユーザーの**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力します。

11. リスト内で**ユーザー**にポインターを合わせ、**チェック ボックス**を表示します。 ユーザーのプロファイル写真またはロゴの横にあるチェック ボックスをオンにして、ユーザーを **[選択済み]** リストに追加します。

12. **省略可能:** **複数のユーザーを追加**する場合は、別の**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力し、チェック ボックスをオンにして **[選択済み]** リストにこのユーザーを追加します。

13. ユーザーの選択が完了したら、**[選択]** ボタンをクリックして、アプリケーションに割り当てるユーザーとグループの一覧に追加します。

14. **省略可能:** **[割り当ての追加]** ウィンドウで **[ロールの選択]** セレクターをクリックして、選択したユーザーに割り当てるロールを選択します。

15. **[割り当て]** ボタンをクリックして、選択したユーザーにアプリケーションを割り当てます。

しばらくすると、選択したユーザーがアクセス パネルでこれらのアプリケーションを起動できるようになります。

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>これらのトラブルシューティング手順で問題が解決しない場合

使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID

-   UPN (ユーザーの電子メール アドレス)

-   TenantId

-   ブラウザーの種類

-   タイム ゾーンとエラーが発生した時刻/タイムフレーム

-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](application-proxy-configure-single-sign-on-with-kcd.md)

