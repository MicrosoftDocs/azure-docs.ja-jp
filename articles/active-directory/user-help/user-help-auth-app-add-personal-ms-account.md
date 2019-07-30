---
title: Microsoft Authenticator アプリに個人用の Microsoft アカウントを追加する - Azure Active Directory | Microsoft Docs
description: 2 要素検証のために、Microsoft Authenticator アプリに Outlook.com や Xbox LIVE などの個人用の Microsoft アカウントを追加する方法。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 502407bbf1cdb47fc8105ed0220babcd9f292faa
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382698"
---
# <a name="add-your-personal-microsoft-accounts"></a>個人用の Microsoft アカウントを追加する

標準的な 2 要素検証プロセスとパスワードなしの電話によるサインイン方法向けに、Outlook.com や Xbox LIVE などの個人用の Microsoft アカウントを Microsoft Authenticator アプリに追加します。

- **標準的な 2 要素検証方法。** ログインするデバイスにご自分のユーザー名とパスワードを入力し、Microsoft Authenticator アプリが通知を送信するかどうか、または Microsoft Authenticator アプリの **[アカウント]** 画面から関連付けられている確認コードをコピーするかを選択します。

- **パスワードなしのサインイン方法。** ログインするデバイスに個人用の Microsoft アカウントのユーザー名を入力してから、モバイル デバイスを使って、ご自分の指紋、顔、または暗証番号 (PIN) を使用して本人確認を行います。 この方法では、パスワードを入力する必要はありません。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

## <a name="add-your-personal-microsoft-account"></a>個人用の Microsoft アカウントを追加する

個人用の Microsoft アカウントを追加するには、最初に 2 要素検証を有効にしてから、アプリにアカウントを追加します。

>[!Note]
>個人用の Microsoft アカウントにパスワードなしの電話によるサインインのみ使用する場合は、2 要素認証を有効にする必要はありません。 しかし、アカウントのセキュリティを強化するために、2 要素認証を有効にすることをお勧めします。

### <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご使用のコンピューターで、[[Security basics]\(セキュリティの基礎\)](https://account.microsoft.com/security) ページに移動して、個人用の Microsoft アカウントを使用してサインインします。 たとえば、「 alain@outlook.com 」のように入力します。

2. **[Security basics]\(セキュリティの基礎\)** ページの下部で、 **[more security options]\(さらに多くのセキュリティ オプション\)** のリンクを選択します。

    ![[more security options]\(さらに多くのセキュリティ オプション\) リンクが強調表示された [Security basics]\(セキュリティの基礎\) ページ](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **[2 段階認証]** セクションに移動して、この機能を**オン**にします。 個人用アカウントでこの機能を使用しない場合は、オフにすることもできます。

### <a name="add-your-microsoft-account-to-the-app"></a>アプリに Microsoft アカウントを追加する

1. モバイル デバイスで Microsoft Authenticator アプリを開きます。

2. 右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから、 **[アカウントの追加]** を選択します。

    ![[Customize and control]\(カスタマイズと制御\) アイコンが強調表示された [アカウント] ページ](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. **[アカウントの追加]** ページで、 **[個人用アカウント]** を選択します。

4. 適切なメール アドレス (alain@outlook.com など) を使用して、個人用アカウントにサインインし、 **[次へ]** を選択します。

    >[!Note]
    >個人用の Microsoft アカウントをお持ちでない場合は、ここで作成することができます。

5. パスワードを入力し、 **[サインイン]** を選択します。

    個人用アカウントが Microsoft Authenticator アプリに追加されます。

## <a name="next-steps"></a>次の手順

- アプリにアカウントを追加すると、デバイスで Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- 個人用 Microsoft アカウントの確認コードの取得に問題がある場合は、「[Microsoft アカウントのセキュリティ情報と確認コード](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes)」の記事の「**確認コードの問題のトラブルシューティング**」セクションを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
