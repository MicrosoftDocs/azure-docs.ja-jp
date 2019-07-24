---
title: 2 段階認証のトラブルシューティング - Azure Active Directory |Microsoft Docs
description: Azure 多要素認証と 2 段階認証の問題が発生した場合の対処方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: b74049833b055caa112c346b74798893f2c0febf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181834"
---
# <a name="get-help-with-two-step-verification"></a>2 段階認証のサポートを受ける

2 段階認証は、ユーザー アカウントを保護するために使用することを組織が選択するセキュリティ機能です。 2 段階認証では、ユーザーが知っているものとユーザーが所有するものの 2 つの形式の認証を使用するため、パスワードだけよりも安全性が高くなります。 ユーザーが知っているものはパスワードで、ユーザーが所有するものは携帯電話やデバイスです。 2 段階認証を使用すると、悪意のあるハッカーがユーザーのパスワードを入手した場合でも、そのユーザーになりすましてサインインするのを防止するために役立ちます。

Microsoft は、2 段階認証を提供していますが、その機能を使用するかどうかを決定するのは組織です。 組織によって必須に設定された場合、アカウントを保護するパスワードの使用をユーザーが無効にできないのと同様に、これを無効にすることはできません。

>[!Note]
>個人用 Microsoft アカウントに 2 段階認証を使用することの詳細については、[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)の記事を参照してください。

## <a name="why-do-i-need-to-use-another-verification-method"></a>別の認証方法を使用する必要がある理由は何ですか。

アカウントで別の認証方法を使用する必要がある理由はいくつかあります。 例: 

- **携帯電話またはデバイスを忘れた場合。** 携帯電話を自宅に忘れたものの、職場でサインインする必要がある場合があります。 まず、携帯電話が必要ない別の方法を使用してサインインしてください。

- **携帯電話を紛失した、または新しい電話番号を取得した。** 携帯電話を紛失した、または新しい番号を取得した場合は、別の方法を使用してサインインするか、管理者に設定を消去するよう依頼してください。 アカウントを適切に更新できるように、携帯電話を紛失した、または携帯電話が盗難にあったことを管理者に知らせることを強くお勧めします。 設定が消去されると、次回のサインイン時に [2 段階認証の登録](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが表示されます。

- **認証テキストが受信できない、または電話呼び出しが着信できない。** テキストまたは電話呼び出しが受信できない理由はいくつか考えられます。 過去に同じ電話器でテキストまたは通話を正常に受け取っている場合は、アカウントではなく携帯電話会社に問題がある可能性があります。 多くの場合、電波状態が悪いと時間がかかります。この場合、モバイル デバイスで [Microsoft Authenticator アプリ](user-help-auth-app-download-install.md)を使用することをお勧めします。 このアプリは、電波またはインターネット接続を一切必要とせず、サインイン用のランダムなセキュリティ コードを生成できます。<br><br>テキスト メッセージを受信しようとしている場合は、テキストを受信できることをテストするために友人にテキストを送信するよう頼んでください。また、複数のメッセージを受信した場合は、必ず最近のものからのコードを使用してください。

- **アプリのパスワードが機能しない場合** 2 段階認証をサポートしていない古いバージョンのデスクトップ アプリケーションでは、アプリ パスワードによって通常のパスワードが置き換えられます。 まず、パスワードを正しく入力したことを確認してください。 それでも解決しない場合は、[新しいアプリ パスワードを作成](multi-factor-authentication-end-user-app-passwords.md)するか、新しいアプリ パスワードを作成できるよう[既存のアプリ パスワードを削除](../authentication/howto-mfa-userdevicesettings.md)するよう管理者に依頼してください。

## <a name="sign-in-using-another-verification-method"></a>別の認証方法を使用してサインインする

1. 通常どおりアカウントにサインインし、**[2 段階認証]** ページで **[Sign in another way]\(別の方法でサインイン\)** リンクを選択します。

    ![サインインの検証方法を変更する](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**[Sign in another way]\(別の方法でサインイン\)** リンクが表示されない場合は、他の検証方法を設定していないことを意味します。 アカウントにサインインするには、管理者に連絡してサポートを受ける必要があります。 サインインした後に、必ず他の検証方法を追加してください。 検証方法を追加する方法の詳細については、[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)に関する記事を参照してください。<br><br>リンクが表示されるが他の検証方法が表示されない場合、アカウントにサインインするには、管理者に連絡してサポートを受ける必要があります。

2. 他の認証方法を選択し、2 段階認証を続行します。

3. 自分のアカウントにサインインできたら、検証方法を更新できます (必要な場合)。 検証方法の追加または変更の詳細については、[2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)に関する記事を参照してください。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>問題に対する回答が見つからなかった場合

これらの手順を試してもまだ問題が発生する場合は、管理者に連絡してサポートを受けてください。

## <a name="related-topics"></a>関連トピック

* [2 段階認証設定の管理](multi-factor-authentication-end-user-manage-settings.md)

* [Microsoft Authenticator アプリケーションに関する FAQ](user-help-auth-app-faq.md)
