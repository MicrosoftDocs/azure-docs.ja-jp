---
title: 個人用の Microsoft アカウントを Microsoft 認証アプリに追加する - Azure AD
description: 2 要素検証を使用するときに、ID を確認するために、Microsoft Authenticator アプリに Outlook.com や Xbox LIVE などの個人用の Microsoft アカウントを追加します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83741624"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>個人の Microsoft アカウントを Microsoft Authenticator アプリに追加する

標準の 2 要素検証プロセスとパスワードレスの電話によるサインインの両方のために、Microsoft Authenticator アプリに個人用の Microsoft アカウント (Outlook.com アカウントや Xbox LIVE アカウントなど) を追加します。

- **標準的な 2 要素検証方法。** サインインしているデバイスにユーザー名とパスワードを入力した後、Microsoft Authenticator アプリが通知を送信するようにするか、または Microsoft Authenticator アプリの **[アカウント]** 画面から関連付けられた確認コードをコピーするかを選択します。

- **パスワードなしのサインイン方法。** 個人用の Microsoft アカウントを使用して、サインインしているデバイスにユーザー名を入力した後、モバイル デバイスを使用し、指紋、顔、または PIN を使用して本人であることを確認します。 この方法では、パスワードを入力する必要がありません。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

個人用の Microsoft アカウントを追加するには、最初に 2 要素検証を有効にしてから、アプリにアカウントを追加します。 アカウントに対してパスワードレスの電話によるサインインのみを使用するために 2 要素検証を有効にする必要はありませんが、アカウントのセキュリティを強化するために 2 要素検証を有効にすることを強くお勧めします。

## <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご使用のコンピューターで、[[Security basics]\(セキュリティの基礎\)](https://account.microsoft.com/security) ページに移動して、個人用の Microsoft アカウントを使用してサインインします。 たとえば、「 alain@outlook.com 」のように入力します。

2. **[Security basics]\(セキュリティの基礎\)** ページの下部で、 **[more security options]\(さらに多くのセキュリティ オプション\)** のリンクを選択します。

    ![[more security options]\(さらに多くのセキュリティ オプション\) リンクが強調表示された [Security basics]\(セキュリティの基礎\) ページ](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. **[2 段階認証]** セクションに移動して、この機能を **オン** にします。 個人用アカウントでこの機能を使用しない場合は、オフにすることもできます。

## <a name="add-your-microsoft-account-to-the-app"></a>アプリに Microsoft アカウントを追加する

1. モバイル デバイスで Microsoft Authenticator アプリを開きます。

1. Android では、右上の **[Customize and Control] (カスタマイズと制御)** アイコンから **[アカウントの追加]** を選択します。

    ![Android のアカウント選択ページ](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    iOS では、右上のプラス記号アイコンを選択します。

    ![iOS バージョンのアカウント選択エクスペリエンス](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. **[アカウントの追加]** ページで、 **[個人用アカウント]** を選択します。

1. **[Microsoft アカウントでサインイン]** を選択してアカウントを追加します。 使用可能な場合は QR コードを使用できますが、ユーザー名とパスワードでサインインすることによって、いつでもアカウントを追加できます。

    ![Microsoft アカウントを選択するか、または使用可能な場合は QR コードをスキャンします。](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 適切なメール アドレス (alain@outlook.com など) を使用して個人用アカウントにサインインし、 **[次へ]** を選択します。

    >[!Note]
    >個人用の Microsoft アカウントがない場合は、[ここで作成できます](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)。

1. パスワードを入力し、 **[サインイン]** を選択します。 個人用アカウントが Microsoft Authenticator アプリに追加されます。

## <a name="next-steps"></a>次のステップ

- アプリにアカウントを追加すると、デバイスで Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- 個人用 Microsoft アカウントの確認コードの取得に問題がある場合は、「[Microsoft アカウントのセキュリティ情報と確認コード](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)」の記事の「**確認コードの問題のトラブルシューティング**」セクションを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
