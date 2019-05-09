---
title: アクセス パネルの Web サイトにサインインできない問題 | Microsoft Docs
description: サインインしてアクセス パネルを使用するときに発生する問題のトラブルシューティングに関するガイダンス
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
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f7418c16d5d5312d2a9a7913da49941ef0a7870
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090459"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>アクセス パネルの Web サイトにサインインできない問題

Web ベースのポータルであるアクセス パネルを使用すると、Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーは、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループおよびアプリ管理機能を利用することもできます。 アクセス パネルは Azure Portal から独立していて、ユーザーが Azure サブスクリプションを持っている必要はありません。

Azure AD の職場または学校アカウントを持つユーザーは、次の方法でアクセス パネルにサインインできます。

-   ユーザーは、Azure AD で直接認証できます。

-   ユーザーは、Active Directory フェデレーション サービス (AD FS) を使用して認証できます。

-   ユーザーは、Windows Server Active Directory で認証できます。

ユーザーが Azure または Office 365 のサブスクリプションを保持し、Azure Portal または Office 365 アプリケーションを使用している場合は、サインインし直さなくてもアクセス パネルをシームレスに使用できます。 認証されていないユーザーは、Azure AD アカウントのユーザー名とパスワードを使用してサインインするように求められます。 組織がフェデレーションを構成している場合は、ユーザー名を入力するだけで十分です。

## <a name="general-issues-to-check-first"></a>最初にチェックすべき一般的な問題 

-   ユーザーが**正しい URL** (<https://myapps.microsoft.com>) にサインインしていることを確認します。

-   ユーザーのブラウザーが、この URL を**信頼済みサイト**に追加していることを確認します。

-   ユーザーのアカウントがサインイン用に**有効になっている**ことを確認します。

-   ユーザーのアカウントが**ロックアウトされていない**ことを確認します。

-   ユーザーの**パスワードが期限切れになったり、忘れられたりしていない**ことを確認します。

-   **多要素認証**がユーザー アクセスをブロックしていないことを確認します。

-   **条件付きアクセス ポリシー**または **ID 保護**ポリシーがユーザー アクセスをブロックしていないことを確認します。

-   ユーザーの**認証の連絡先情報**が最新のものであり、多要素認証または条件付きアクセス ポリシーを強制できることを確認します。

-   ブラウザーの Cookie を削除してから、再度サインインを試行して、サインインできることを確認します。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>アクセス パネルのブラウザーの要件を満たす

アクセス パネルには、JavaScript をサポートする CSS 対応のブラウザーが必要です。 アクセス パネルでパスワード ベースのシングル サインオン (SSO) を使用するためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

パスワードベースの SSO の場合、エンド ユーザーのブラウザーには次のいずれかを使用できます。

-   Internet Explorer 8、9、10、11 - Windows 7 以降

-   Microsoft Edge - Windows 10 Anniversary Edition 以降 

-   Chrome - Windows 7 以降、MacOS X 以降

-   Firefox 26.0 以降 - Windows XP SP2 以降、Mac os X 10.6 以降


## <a name="problems-with-the-users-account"></a>ユーザーのアカウントに関する問題

ユーザーのアカウントに関する問題により、アクセス パネルへのアクセスがブロックされることがあります。 ユーザーとそのアカウントの設定に関する問題をトラブルシューティングして解決するための方法を次に示します。

-   [ユーザー アカウントが Azure Active Directory に存在するかどうかを調べる](#check-if-a-user-account-exists-in-azure-active-directory)

-   [ユーザーのアカウントの状態を確認する](#check-a-users-account-status)

-   [ユーザーのパスワードをリセットする](#reset-a-users-password)

-   [セルフ サービスのパスワード リセットを有効にする](#enable-self-service-password-reset)

-   [ユーザーの多要素認証の状態を確認する](#check-a-users-multi-factor-authentication-status)

-   [ユーザーの認証の連絡先情報を確認する](#check-a-users-authentication-contact-info)

-   [ユーザーのグループ メンバーシップを確認する](#check-a-users-group-memberships)

-   [ユーザーに割り当てられているライセンスを確認する](#check-a-users-assigned-licenses)

-   [ユーザーにライセンスを割り当てる](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>ユーザー アカウントが Azure Active Directory に存在するかどうかを調べる

ユーザーのアカウントが存在するかどうかを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  ユーザー オブジェクトのプロパティを調べて、予想した通りに表示されていることと、紛失したデータがないことを確認します。

### <a name="check-a-users-account-status"></a>ユーザーのアカウントの状態を確認する

ユーザーのアカウントの状態を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[Profile]** をクリックします。

8.  **[設定]** で、**[サインインのブロック]** が **[いいえ]** に設定されていることを確認します。

### <a name="reset-a-users-password"></a>ユーザーのパスワードをリセットする

ユーザーのパスワードをリセットするには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  ユーザー ウィンドウの上部にある **[パスワードのリセット]** をクリックします。

8.  表示される **[パスワードのリセット]** ウィンドウ上の **[パスワードのリセット]** をクリックします。

9.  **一時パスワード**をコピーするか、ユーザーの**新しいパスワードを入力**します。

10. この新しいパスワードをユーザーに知らせます。ユーザーは次の Azure Active Directory サインイン時にこのパスワードを変更する必要があります。

### <a name="enable-self-service-password-reset"></a>セルフ サービスのパスワード リセットを有効にする

セルフ サービスのパスワード リセットを有効にするには、次のデプロイ手順に従います。

-   [Azure Active Directory パスワードをユーザーがリセットできるようにする](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [オンプレミス Active Directory パスワードをユーザーがリセットまたは変更できるようにする](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>ユーザーの多要素認証の状態を確認する

ユーザーの多要素認証の状態を確認するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5. **[すべてのユーザー]** をクリックします。

6. ウィンドウの上部にある **[多要素認証]** をクリックします。

7. **多要素認証管理ポータル**をロードしたら、自分が **[ユーザー]** タブに存在していることを確認します。

8. ユーザーの一覧で、検索、フィルター処理、または並べ替えによってユーザーを見つけます。

9. ユーザーの一覧からユーザーを選択し、多要素認証を必要に応じて**有効**、**無効**、または**適用**します。

   >[!NOTE]
   >ユーザーが**適用**状態の場合は、一時的に**無効**に設定してユーザーが自分のアカウントに戻れるようにできます。 ユーザーがアカウントに戻ったら、ユーザーの状態を再度**有効**に変更して、ユーザーの連絡先情報を次のサインイン時に再登録するように要求します。 あるいは、[ユーザーの認証の連絡先情報を確認する](#check-a-users-authentication-contact-info)の手順に従って、このユーザーのデータを検証または設定することができます。
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>ユーザーの認証の連絡先情報を確認する

多要素認証、条件付きアクセス、ID 保護、およびパスワード リセットで使用されるユーザーの認証の連絡先情報を確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[Profile]** をクリックします。

8.  下の **[認証の連絡先情報]** までスクロールします。

9.  登録されたユーザーのデータを**レビュー**して、必要に応じて更新します。

### <a name="check-a-users-group-memberships"></a>ユーザーのグループ メンバーシップを確認する

ユーザーのグループ メンバーシップを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[グループ]** をクリックすると、ユーザーがメンバーになっているグループが表示されます。

### <a name="check-a-users-assigned-licenses"></a>ユーザーに割り当てられているライセンスを確認する

ユーザーに割り当てられているライセンスを確認するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、ユーザーに現在割り当てられているライセンスを確認します。

### <a name="assign-a-user-a-license"></a>ユーザーにライセンスを割り当てる 

ユーザーにライセンスを割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[すべてのユーザー]** をクリックします。

6.  対象のユーザーを**検索**し、**行をクリック**して選択します。

7.  **[ライセンス]** をクリックして、ユーザーが現在割り当てられているライセンスを確認します。

8.  **[割り当て]** ボタンをクリックします。

9.  **1 つまたは複数の製品**を利用可能な製品の一覧から選択します。

10. **省略可能** より細かい単位で製品を割り当てるには、**割り当てオプション**項目をクリックします。 完了したら **[OK]** をクリックします。

11. **[割り当て]** ボタンをクリックしてこれらのライセンスをこのユーザーに割り当てます。

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>これらのトラブルシューティング手順で問題が解決しない場合、

使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID

-   UPN (ユーザーの電子メール アドレス)

-   テナント ID

-   ブラウザーの種類

-   タイム ゾーンとエラーが発生した時刻/タイムフレーム

-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](application-proxy-configure-single-sign-on-with-kcd.md)
