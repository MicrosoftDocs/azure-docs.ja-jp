---
title: IIS 認証と Azure MFA Server - Azure Active Directory
description: IIS 認証と Azure Multi-Factor Authentication Server のデプロイ。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7f76211fe810ce1db53e5afaa307d90317464a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057394"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>IIS Web アプリの Azure Multi-Factor Authentication Server の構成

Azure Multi-Factor Authentication (MFA) Server の [IIS 認証] セクションを使用して、IIS 認証を有効にし、Microsoft IIS Web アプリケーションと統合するように構成できます。 Azure MFA Server では、Azure Multi-Factor Authentication を追加するために、IIS Web サーバーに対して行われる要求をフィルター処理できるプラグインがインストールされます。 IIS プラグインは、フォームベースの認証と統合 Windows HTTP 認証のサポートを提供します。 さらに、信頼できる IP を構成して、内部 IP アドレスを 2 要素認証から除外できます。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイの MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

![MFA Server での IIS 認証](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でフォームベースの IIS 認証を使用する

フォームベースの認証を使用する IIS Web アプリケーションをセキュリティで保護するには、IIS Web サーバーに Azure Multi-Factor Authentication Server をインストールし、次の手順に従って Server を構成します。

1. Azure Multi-Factor Authentication Server で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. **[フォームベース]** タブをクリックします。
3. **[追加]** をクリックします。
4. ユーザー名、パスワード、およびドメインの変数を自動的に検出するには、[フォームベースの Web サイトの自動構成] ダイアログ ボックス内でログイン URL (例: `https://localhost/contoso/auth/login.aspx`) を入力し、 **[OK]** をクリックします。
5. すべてのユーザーを Server にインポート済みであるか、インポートする予定であり、多要素認証の対象となる場合は、 **[Multi-Factor Authentication のユーザー照合が必要]** チェック ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。
6. ページ変数を自動的に検出できない場合は、[フォームベースの Web サイトの自動構成] ダイアログ ボックスの **[手動で指定]** をクリックします。
7. [フォームベースの Web サイトの追加] ダイアログ ボックスで、ログイン ページの URL を [送信 URL] フィールドに入力し、アプリケーション名 (省略可能) を入力します。 アプリケーション名は Azure Multi-Factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
8. 適切な要求の形式を選択します。 ほとんどの Web アプリケーションでは、これは **POST または GET** に設定されます。
9. ユーザー名変数、パスワード変数、ドメイン変数を入力します ([ログイン] ページに表示される場合)。 入力ボックスの名前を検索するには、Web ブラウザーでログイン ページに移動し、ページを右クリックして **[ソースの表示]** を選択します。
10. すべてのユーザーを Server にインポート済みであるか、インポートする予定であり、多要素認証の対象となる場合は、 **[Multi-Factor Authentication のユーザー照合が必要]** チェック ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。
11. **[詳細設定]** をクリックして、次の詳細設定を確認します。

    - カスタム拒否ページ ファイルの選択
    - Cookie を使用して、Web サイトへの成功した認証を一定期間キャッシュします。
    - Windows ドメイン、LDAP ディレクトリ、または RADIUS サーバーに対してプライマリ資格情報を認証するかどうかを 選択します。

12. **[OK]** をクリックして、[フォームベースの Web サイトの追加] ダイアログ ボックスに戻ります。
13. Click **OK**.
14. URL とページの変数が検出または入力されたら、フォームベースのパネルにその Web サイトのデータが表示されます。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>統合 Windows 認証と Azure Multi-Factor Authentication Server を使用する

統合 Windows HTTP 認証を使用する IIS Web アプリケーションをセキュリティで保護するには、IIS Web サーバーに Azure MFA Server をインストールし、次の手順で Server を構成します。

1. Azure Multi-Factor Authentication Server で、左側のメニューの [IIS 認証] アイコンをクリックします。
2. **[HTTP]** タブをクリックします。
3. **[追加]** をクリックします。
4. [ベース URL の追加] ダイアログ ボックスで、HTTP 認証が実行される Web サイトの URL (例: <http://localhost/owa>) を入力し、アプリケーション名 (省略可能) を指定します。 アプリケーション名は Azure Multi-Factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
5. 既定値では不十分な場合は、アイドル状態のタイムアウトと最大セッション時間を調整します。
6. すべてのユーザーを Server にインポート済みであるか、インポートする予定であり、多要素認証の対象となる場合は、 **[Multi-Factor Authentication のユーザー照合が必要]** チェック ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。
7. 必要に応じて、 **[Cookie のキャッシュ]** チェック ボックスをオンにします。
8. Click **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server の IIS プラグインを有効にする

フォームベースまたは HTTP 認証 URL と設定を構成したら、Azure Multi-Factor Authentication の IIS プラグインを読み込んで IIS で有効にする必要がある場所を選択します。 次の手順に従います。

1. IIS 6 で実行する場合は、 **[ISAPI]** タブをクリックします。Web アプリケーションが実行される Web サイト (例: 既定の Web サイト) を選択して、そのサイトに対して Azure Multi-Factor Authentication の ISAPI フィルター プラグインを有効にします。
2. IIS 7 以降で実行する場合は、 **[ネイティブ モジュール]** タブをクリックします。希望のレベルで IIS プラグインを有効にするサーバー、Web サイト、またはアプリケーションを選択します。
3. 画面上部にある **[IIS 認証を有効にする]** ボックスをクリックします。 これで、Azure Multi-Factor Authentication で、選択された IIS アプリケーションが保護されるようになりました。 ユーザーが Server にインポートされていることを確認してください。

## <a name="trusted-ips"></a>信頼できる IP

信頼される IP は、特定の IP アドレスまたはサブネットから発生する Web サイト要求に関して、ユーザーが Azure Multi-Factor Authentication をバイパスできるようにします。 たとえば、社内からのログイン中は、Azure Multi-Factor Authentication からユーザーを除外することもできます。 そのためには、社内のサブネットを信頼できる IP エントリとして指定します。 信頼できる IP を構成するには、次の手順に従います。

1. [IIS 認証] セクションで、 **[信頼できる IP]** タブをクリックします。
2. **[追加]** をクリックします。
3. [信頼できる IP の追加] ダイアログ ボックスが表示されたら、 **[単一 IP]** 、 **[IP 範囲]** 、または **[サブネット]** をクリックします。
4. 許可される IP アドレス、IP アドレスの範囲、またはサブネットを入力します。 サブネットを入力する場合は、適切なネットマスクを選択し、 **[OK]** をクリックします。
