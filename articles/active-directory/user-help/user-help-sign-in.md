---
title: 2 段階認証またはセキュリティ情報を使ったサインイン - Azure Active Directory | Microsoft Docs
description: セキュリティ情報での各種本人確認方法を使ったサインイン方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60e4d9a4cb555dfbc9f05961487a8f794d19e1bf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268357"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>2 段階認証またはセキュリティ情報を使ったサインイン方法
2 段階認証またはセキュリティ情報を設定したら、指定した認証方法を使って、アカウントにサインインできるようになります。

> [!Note]
> 引き続き 2 段階認証を使用している場合は、「[アカウントへの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)」の手順に従って認証方法を設定する必要があります。
> 
> 管理者によってセキュリティ情報エクスペリエンスが有効化されている場合は、次のステップ バイ ステップ記事を使用して認証方法を設定する必要があります。<ul><li>[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)</li><li>[テキスト メッセージを使用するようにセキュリティ情報を設定する](security-info-setup-text-msg.md)</li><li>[電話呼び出しを使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>モバイル デバイスの認証アプリの通知を使ってサインインする

1. ユーザー名とパスワードを使って、アカウントにサインインします。

2. モバイル デバイスに送信された承認通知から、**[承認]** を選択します。

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>モバイル デバイスの認証アプリのコードを使ってサインインする

1. ユーザー名とパスワードを使って、アカウントにサインインします。

2. 認証アプリを開き、アカウントに対してランダムに生成されたコードを、**[コードの入力]** ボックスに入力します。

## <a name="sign-in-using-your-phone-number"></a>電話番号を使ってサインインする

1. ユーザー名とパスワードを使って、アカウントにサインインします。

2. 電話に応答し、指示に従います。

## <a name="sign-in-using-a-text-message"></a>テキスト メッセージを使ってサインインする

1. ユーザー名とパスワードを使って、アカウントにサインインします。

2. テキスト メッセージを開き、テキスト メッセージにあるコードを **[コードの入力]** ボックスに入力します。

## <a name="sign-in-using-another-verification-method"></a>別の認証方法を使用してサインインする
なんらかの理由で主要なサインイン方法を使用できない場合は、事前に設定した別の認証方法を使用することができます。

1. 通常どおりアカウントにサインインし、**[2 段階認証]** ページで **[別の方法でサインイン]** リンクを選択します。

    ![サインインの検証方法を変更する](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**[別の方法でサインイン]** リンクが表示されない場合、別の認証方法の設定が済んでいないことを意味します。アカウントにサインインするには、管理者に連絡してサポートを受ける必要があります。 管理者のサポートの下でサインインした後に、必ず新たに認証方法を追加してください。 検証方法を追加する方法の詳細については、[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)に関する記事を参照してください。
    > 
    >**[別の方法でサインイン]** リンクは表示されるが他の認証方法が表示されない場合、アカウントにサインインするには、管理者に連絡してサポートを受ける必要があります。

2. 他の認証方法を選択し、2 段階認証を続行します。

3. 自分のアカウントにサインインできたら、検証方法を更新できます (必要な場合)。 検証方法の追加または変更の詳細については、[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- 「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」の記事でセキュリティ情報について学習する。

- 「[2 段階認証の概要](user-help-two-step-verification-overview.md)」の記事で 2 段階認証について学習する。 

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)でパスワードをリセットする (パスワードをなくしたか忘れた場合)

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。
