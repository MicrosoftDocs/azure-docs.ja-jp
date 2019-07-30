---
title: パスワード SSO 用に構成された Azure AD ギャラリー アプリへのサインインの問題 | Microsoft Docs
description: パスワード シングル サインオン用に構成された Azure AD ギャラリー アプリケーションの問題をトラブルシューティングする方法。
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190320"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>SSO 用に構成された Azure AD ギャラリー アプリのサインインの問題

アクセス パネルは Web ベースのポータルです。 Azure Active Directory (Azure AD) の職場または学校のアカウントを持つユーザーが、アクセス許可のあるクラウド ベースのアプリにアクセスできるようにします。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。

アクセス パネルは Azure portal とは別のものです。 ユーザーはアクセス パネルを使用するために Azure サブスクリプションを必要としません。

アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、お使いのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 この拡張機能は、パスワード ベースの SSO 用に構成されているアプリを選ぶと、自動的にダウンロードされます。

## <a name="browser-requirements-for-access-panel"></a>アクセス パネルのブラウザーの要件

アクセス パネルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。

次のブラウザーでは、パスワード ベースの SSO をサポートします。

- Internet Explorer 8、9、10、11 - Windows 7 以降

- Chrome - Windows 7 以降、または MacOS X 以降

- Firefox 26.0 以降 - Windows XP SP2 以降、または Mac OS X 10.6 以降

>[!NOTE]
>パスワードベースの SSO の拡張機能は、ブラウザー拡張機能のサポートが Microsoft Edge に追加されると、Windows 10 の Microsoft Edge で使用できるようになります。

## <a name="install-the-access-panel-browser-extension"></a>アクセス パネルのブラウザー拡張機能をインストールする

次の手順に従います。

1. サポートされているブラウザーで[アクセス パネル](https://myapps.microsoft.com)を開き、Azure AD のユーザーとしてサインインします。

2. アクセス パネルで、パスワード SSO 対応アプリを選択します。

3. メッセージが表示されたら、 **[今すぐインストール]** を選択します。

4. お使いのブラウザーに基づいて、ダウンロード リンクが表示されます。 **[追加]** をクリックしてブラウザー拡張機能をインストールします。

5. メッセージが表示されたら、 **[有効にする]** または **[許可する]** を選択します。

6. インストール後、お使いのブラウザーを再起動します。

7.  アクセス パネルにサインインして、パスワード SSO 対応アプリを開始できるかどうか確認します。

これらのリンクから、Chrome および Firefox 向けの拡張機能を直接ダウンロードすることもできます。

-   [Chrome アクセス パネル拡張機能](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox アクセス パネル拡張機能](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer のグループ ポリシーの設定

Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールできるようにグループ ポリシーを設定できます。

前提条件は次のとおりです。

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) が設定され、ユーザーのコンピューターがドメインに参加している必要があります。

-   グループ ポリシー オブジェクト (GPO) を編集するための "設定の編集" アクセス許可が必要です。 既定でこのアクセス許可を持っているセキュリティ グループのメンバーはDomain Administrators、Enterprise Administrators、Group Policy Creator Owners です。 [詳細情報](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

グループ ポリシーを構成してユーザーにデプロイするには、「[方法: グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)」を参照してください。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Internet Explorer でアクセス パネルをトラブルシューティングする

診断ツールにアクセスして拡張機能を構成する手順については、「[Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)」を参照してください。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Azure AD ギャラリーアプリのパスワード SSO を構成する

Azure AD ギャラリーからアプリを構成するには、以下を実行する必要があります。

-   Azure AD ギャラリーからアプリを追加する
-   [パスワード シングル サインオンに対応するようにアプリを構成する](#configure-the-app-for-password-sso)
-   [アプリにユーザーを割り当てる](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Azure AD ギャラリーからアプリを追加する

次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウから **[エンタープライズ アプリケーション]** を選択します。

5. **[エンタープライズ アプリケーション]** ウィンドウの右上隅にある **[追加]** を選択します。

6. **[ギャラリーから追加する]** セクションで、 **[名前を入力してください]** ボックスにアプリの名前を入力します。

7. SSO を構成するアプリを選択します。

8. *省略可能:* アプリを追加する前に、 **[名前]** ボックスでその名前を変更できます。

9. **[追加]** をクリックしてアプリを追加します。

   しばらくすると、アプリの構成ウィンドウが表示できるようになります。

### <a name="configure-the-app-for-password-sso"></a>パスワード SSO に対応するようにアプリを構成する

次の手順に従います。

1. [Azure portal](https://portal.azure.com/) を開き、グローバル管理者または共同管理者としてサインインします。

2. 左側のナビゲーション ウィンドウの上部にある **[すべてのサービス]** を選択して、Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、ご自分のアプリの一覧を表示します。

   > [!NOTE]
   > 目的のアプリが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. SSO を構成するアプリを選択します。

7. アプリが読み込まれたら、アプリの左側のウィンドウで **[シングル サインオン]** を選択します。

8. **[パスワード ベースのサインオン]** モードを選択します。

9. アプリにユーザーを割り当てます。

10. ユーザーの資格情報を指定することもできます。 (指定しない場合、ユーザーはアプリの起動時に資格情報の入力を求められます。)これを行うには、ユーザーの行を選択します。 次に、 **[資格情報の更新]** を選択して、ユーザー名とパスワードを入力します。

### <a name="assign-users-to-the-app"></a>アプリにユーザーを割り当てる

ユーザーをアプリに直接割り当てるには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) を開き、グローバル管理者としてサインインします。

2. 左側のナビゲーション ウィンドウで **[すべてのサービス]** を選択して Azure AD 拡張機能を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** を選択します。

4. Azure AD ナビゲーション ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

5. **[すべてのアプリケーション]** を選択して、アプリケーションの一覧を表示します。

   > [!NOTE]
   > 目的のアプリが表示されない場合、 **[すべてのアプリケーションの一覧]** の上部にある **[フィルター]** コントロールを使用します。 **[表示]** オプションを [すべてのアプリケーション] に設定します。

6. 一覧から、ユーザーを割り当てるアプリを選択します。

7. アプリケーションが読み込まれたら、左側にあるアプリのナビゲーション ウィンドウから **[ユーザーとグループ]** を選択します。

8. **[ユーザーとグループ]** 一覧の上部にある **[追加]** を選択して、 **[割り当ての追加]** ウィンドウを開きます。

9. **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

10. **[名前または電子メール アドレスで検索]** ボックスに、割り当てるユーザーのフル ネームまたは電子メール アドレスを入力します。

11. 一覧内のユーザーにマウス カーソルを合わせます。 ユーザーのプロフィール写真またはロゴの横にあるチェック ボックスをオンにして、そのユーザーを **[選択済み]** リストに追加します。

12. *省略可能:* 別のユーザーを追加するには、 **[名前または電子メール アドレスで検索]** ボックスに別の名前または電子メール アドレスを入力し、チェック ボックスをオンにして、そのユーザーを **[選択済み]** リストに追加します。

13. ユーザーの選択が完了したら、 **[選択]** をクリックして、アプリに割り当てるユーザーとグループの一覧に追加します。

14. *省略可能:* **[割り当ての追加]** ウィンドウで **[ロールの選択]** をクリックして、選択したユーザーに割り当てるロールを選択します。

15. **[割り当て]** を選択して、選択したユーザーにアプリを割り当てます。

    しばらくすると、ユーザーはアクセス パネルからこれらのアプリにアクセスできるようになります。

## <a name="request-support"></a>サポートの要求 
SSO を設定してユーザーを割り当てるときにエラー メッセージが表示される場合は、サポート チケットを開きます。 次の情報をできるだけお知らせください。

-   関連エラー ID
-   UPN (ユーザーの電子メール アドレス)
-   TenantId
-   ブラウザーの種類
-   タイム ゾーンと、エラーが発生したときの時刻/時間帯
-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](application-proxy-configure-single-sign-on-with-kcd.md)
