---
title: My Apps ポータルの操作についてお困りの場合 - Azure Active Directory| Microsoft Docs
description: My Apps ポータルへのサインイン、My Apps ポータルでの一般的なタスクの実行に関するヘルプです。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11a7376720661e13a03be65df4ee180e0bca20c3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383066"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>My Apps ポータルに関する問題を解決する

**My Apps** ポータルにサインインするとき、またはそのポータルの使用中に問題が発生した場合は、ヘルプデスクや管理者に連絡する前に、ここで示すトラブルシューティングのヒントをお試しください。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>My Apps によるセキュリティで保護されたサインイン拡張機能を正しくインストールできません

My Apps によるセキュリティで保護されたサインイン拡張機能を正しくインストールできない場合:

- 次のようなサポートされているブラウザーを使用しているかどうかを確認する:

    - **Microsoft Edge。** Windows 10 Anniversary Edition 以降で実行。

    - **Google Chrome。** Windows 7 以降か Mac OS X 以降で実行。

    - **Mozilla Firefox 26.0 以降。** Windows XP SP2 以降か Mac OS X 10.6 以降で実行。

    - **Internet Explorer 11。** Windows 7 以降で実行 (制限付きサポート)。

- ブラウザーの拡張機能設定がオンになっていることを確認してください。

- ブラウザーを再起動し、**My Apps** ポータルに再度サインインしてみます。

- ブラウザーの Cookie を消去して **My Apps** ポータルに再度サインインしてみます。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>**My Apps** ポータルにサインインできません

**My Apps** ポータルにサインインできない場合、次をお試しください。

- 使用している URL が正しいことを確認します。 [https://myapps.microsoft.com](https://myapps.microsoft.com ) のようになるか、 https://myapps.microsoft.com/contoso.com のような組織向けにカスタマイズされたページになるはずです。

- パスワードが正しいことと有効期限が切れていないことを確認します。 詳細については、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」を参照してください。

- 確認情報が最新の状態であり、正しいことを確認してください。 詳細については、「[Azure Multi-Factor Authentication とは何ですか](multi-factor-authentication-end-user.md)」または[セキュリティ情報のメソッドと情報の変更](security-info-add-update-methods-overview.md)に関するページを参照してください。

- **[インターネット プロパティ]、[セキュリティ] の順に選択し、[信頼されているサイト]** 設定で **My App** ポータルの URL を追加します。

- ブラウザーのキャッシュを消去し、もう一度サインインしてみます。

## <a name="my-password-isnt-working"></a>パスワードが機能しません

パスワードを忘れた場合、組織からパスワードを受け取っていない場合、アカウントからロックアウトされた場合、またはパスワードを変更する場合は、[Azure AD パスワードを忘れたときの対処方法](active-directory-passwords-update-your-own-password.md)に関するページをご覧ください。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>自分のパスワードをリセットしたい場合はどうすればよいですか。

お使いのパスワードをリセットできるようにするには、まず、管理者が組織に対してその機能をオンにする必要があります。その後、必須の検証方法をご自分で更新し、確認する必要があります。 検証方法を更新する方法については、「[セルフサービスによるパスワードのリセットを登録する](active-directory-passwords-reset-register.md)」を参照してください。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>アプリを起動すると、アクセス拒否のメッセージが表示されます

**My App** ポータルからアプリを起動した後、 **[アクセスが拒否されました]** というメッセージが表示される場合は、次をお試しください。

- [マイ アプリによるセキュリティで保護されたサインイン拡張機能](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)がインストールされていることと[サポートされているブラウザー](my-apps-portal-end-user-access.md#supported-browsers)を使用していることを確認してください。

- アプリの URL が正しいことと、 **[インターネット プロパティ]、[セキュリティ] の順に選択して表示される、[信頼されているサイト]** の一覧にその URL が記載されていることを確認します。

- パスワードが正しいことと有効期限が切れていないことを確認します。 詳細については、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」を参照してください。

- 確認情報が最新の状態であり、正しいことを確認してください。 詳細については、「[Azure Multi-Factor Authentication とは何ですか](multi-factor-authentication-end-user.md)」または[セキュリティ情報のメソッドと情報の変更](security-info-add-update-methods-overview.md)に関するページを参照してください。

- ブラウザーのキャッシュを消去し、もう一度サインインしてみます。

以上の措置を試してもアプリを起動できない場合、組織のヘルプ デスクに連絡し、サポートを依頼してください。

## <a name="next-steps"></a>次の手順

**My Apps** ポータルにサインインした後、自分のプロファイルとアカウント情報、自分のグループの情報、アクセス レビューの情報を更新することができます (アクセス許可が与えられている場合)。

- [My Apps ポータルでアプリにアクセスし、使用する](my-apps-portal-end-user-access.md)。

- [プロファイル情報を変更する](my-apps-portal-end-user-update-profile.md)。

- [グループ関連の情報を表示し、更新する](my-apps-portal-end-user-groups.md)。

- [自分のアクセス レビューを実行する](my-apps-portal-end-user-access-reviews.md)。
