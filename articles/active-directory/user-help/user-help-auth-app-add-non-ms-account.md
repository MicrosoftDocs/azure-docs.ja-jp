---
title: Microsoft 以外のアカウントを Microsoft Authenticator アプリに追加する - Azure AD
description: 2 要素検証を使用するときに、ID を確認するための Microsoft 以外のアカウント (Google や Facebook など) を Microsoft Authenticator アプリに追加します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359202"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Microsoft 以外のアカウントを Microsoft Authenticator アプリに追加する

Google、Facebook、GitHub など、Microsoft 以外のアカウントを持っている場合は、2 要素検証のために Microsoft Authenticator アプリに追加できます。 Microsoft Authenticator アプリは、2 要素検証を使用する任意のアプリ、および時間ベースのワンタイム パスワード (TOTP) 標準をサポートする任意のアカウントで動作します。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

## <a name="add-personal-accounts"></a>個人用アカウントを追加する

一般的に、すべての個人用アカウントについては、次の操作を行う必要があります。

1. ご利用のアカウントにサインインしてから、デバイスまたはコンピューターを使用して 2 要素検証を有効にします。

2. アカウントを Microsoft Authenticator アプリに追加します。 このプロセスの一環として QR コードをスキャンするように求められることがあります。

    >[!Note]
    >初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

ここでは、Facebook、Google、GitHub、および Amazon のアカウントに関するプロセスについて説明しますが、このプロセスは、Instagram や Adobe などの他のアプリの場合も同じです。

## <a name="add-your-google-account"></a>Google アカウントを追加する

2 要素検証を有効にし、アプリにアカウントを追加して、Google アカウントを追加します。

### <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご利用のコンピューターで、 https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome に移動し、 **[始める]** を選択してから本人確認を行います。

2. ページの手順に従って、個人用の Google アカウントに対して 2 段階認証を有効にします。

### <a name="add-your-google-account-to-the-app"></a>Google アカウントをアプリに追加する

1. コンピューターの Google アカウントのセキュリティ ページ (https://myaccount.google.com/security) で、 **[2 つ目の手順を追加してログインを確認する]** セクションに移動し、 **[認証システム アプリ]** セクションから **[設定]** を選びます。

2. **[Get codes from the Authenticator app]\(Authenticator アプリからコードを入手\)** ページで、ご利用の電話の種類に基づいて **[Android]** または **[iPhone]** を選択してから **[次へ]** を選びます。

    アカウントを自動的に Microsoft Authenticator アプリに関連付けるために使用できる QR コードが表示されます。 このウィンドウは閉じないでください。

3. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。

4. デバイスのカメラを使用して、コンピューターの **[Set up Authenticator]\(Authenticator の設定\)** ページから QR コードをスキャンします。

    >[!Note]
    >カメラが適切に動作しない場合は、QR コードと URL を手動で入力できます。

5. デバイス上の Microsoft Authenticator アプリの **[アカウント]** ページを調べ、アカウント情報が正しく、6 桁の確認コードが関連付けられていることを確認します。

    セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

6. コンピューターの **[Set up Authenticator]\(Authenticator の設定\)** ページで **[次へ]** を選択し、Google アカウント用にアプリで提供された 6 桁の確認コードを入力してから、 **[確認]** を選びます。

7. アカウントが確認されたら、 **[完了]** を選択して、 **[Set up Authenticator]\(Authenticator の設定\)** ページを閉じることができます。

    >[!NOTE]
    >2 要素検証と Google アカウントの詳細については、「[2 段階認証プロセスを有効にする](https://support.google.com/accounts/answer/185839)」と「[2 段階認証プロセスの詳細](https://www.google.com/landing/2step/help.html)」を参照してください。

## <a name="add-your-facebook-account"></a>Facebook アカウントを追加する

2 要素検証を有効にし、アプリにアカウントを追加して、Facebook アカウントを追加します。

### <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご利用のコンピューターで、Facebook を開き、右上隅にあるドロップダウン メニューを選択してから、 **[設定]**  >  **[セキュリティとログイン]** の順に移動します。

    **[セキュリティとログイン]** ページが表示されます。

2. **[2 要素認証]** セクションの **[Use two-factor authentication]\(2 要素認証を使用\)** オプションまで下に移動し、 **[編集]** を選択します。

    **[2 要素認証]** ページが表示されます。

3. **[オンにする]** を選択します。

### <a name="add-your-facebook-account-to-the-app"></a>Facebook アカウントをアプリに追加する

1. ご利用のコンピューターの Facebook ページで、 **[バックアップを追加]** セクションに移動し、 **[Authentication アプリ]** 領域から **[セットアップ]** を選択します。

    アカウントを自動的に Microsoft Authenticator アプリに関連付けるために使用できる QR コードが表示されます。 このウィンドウは閉じないでください。

2. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。

3. デバイスのカメラを使用して、コンピューターの **[2 要素認証]** ページから QR コードをスキャンします。

    >[!Note]
    >カメラが適切に動作しない場合は、QR コードと URL を手動で入力できます。

4. デバイス上の Microsoft Authenticator アプリの **[アカウント]** ページを調べ、アカウント情報が正しく、6 桁の確認コードが関連付けられていることを確認します。

    セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

5. コンピューターの **[2 要素認証]** ページで **[次へ]** を選択し、Facebook アカウント用にアプリで提供された 6 桁の確認コードを入力します。

    アカウントが確認されます。これで、アプリを使用してアカウントを確認できます。

    >[!NOTE]
    >2 要素検証と Facebook アカウントの詳細については、[二段階認証の概要とそのしくみ](https://www.facebook.com/help/148233965247823)に関するページを参照してください。

## <a name="add-your-github-account"></a>GitHub アカウントを追加する

2 要素検証を有効にし、アプリにアカウントを追加して、GitHub アカウントを追加します。

### <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご利用のコンピューターで、GitHub を開き、右上隅のイメージを選択してから **[設定]** を選びます。

    **[2 要素認証]** ページが表示されます。

2. **[個人設定]** サイドバーの **[セキュリティ]** を選択し、 **[2 要素認証]** 領域から **[Enable two-factor authentication]\(2 要素認証を有効にする\)** を選びます。

### <a name="add-your-github-account-to-the-app"></a>GitHub アカウントをアプリに追加する

1. ご利用のコンピューターの **[2 要素認証]** ページで、 **[Set up using an app]\(アプリを使用して設定\)** を選択します。

2. アクセスできなくなった場合にアカウントを復元できるように回復用コードを保存してから、 **[次へ]** を選択します。

    コードは、デバイスにダウンロードするか、印刷するか、パスワード マネージャー ツールにコピーすることで保存できます。

3. **[2 要素認証]** ページで、 **[Set up using an app]\(アプリを使用して設定\)** を選択します。

    ページが変わり、QR コードが表示されます。 このページは閉じないでください。

4. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。その後、ページの上部にあるテキストから **[enter this text code]\(このテキスト コードを入力\)** を選択します。

    Microsoft Authenticator アプリでは QR コードをスキャンできないため、手動でコードを入力する必要があります。

5. **[アカウント名]** (GitHub など) を入力し、手順 4 の **[秘密鍵]** を入力してから **[完了]** を選択します。

6. コンピューターの **[2 要素認証]** ページで、GitHub アカウント用にアプリで提供された 6 桁の確認コードを入力してから、 **[有効にする]** を選択します。

    アプリの **[アカウント]** ページに、アカウント名と 6 桁の確認コードが表示されます。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

    >[!NOTE]
    >2 要素検証と GitHub アカウントの詳細については、「[About two-factor authentication](https://help.github.com/articles/about-two-factor-authentication/)」 (2 要素認証について) を参照してください。

## <a name="add-your-amazon-account"></a>Amazon アカウントを追加する

2 要素検証を有効にし、アプリにアカウントを追加して、Amazon アカウントを追加します。

### <a name="turn-on-two-factor-verification"></a>2 要素検証を有効にする

1. ご利用のコンピューターで、Amazon を開き、 **[Account & Lists]\(アカウントとリスト\)** ドロップダウン メニューを選択し、 **[自分のアカウント]** を選びます。

2. **[Login & security]\(ログインとセキュリティ\)** を選択し、Amazon アカウントにサインインしてから、 **[セキュリティの詳細設定]** 領域で **[編集]** を選びます。

    **[セキュリティの詳細設定]** ページが表示されます。

3. **[開始]** を選択します。

4. **[Choose how you'll receive codes]\(コードの受信方法の選択\)** ページで **[Authenticator アプリ]** を選択します。

    ページが変わり、QR コードが表示されます。 このページは閉じないでください。

5. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。

6. デバイスのカメラを使用して、コンピューターの **[Choose how you'll receive codes]\(コードの受信方法の選択\)** ページから QR コードをスキャンします。

    >[!Note]
    >カメラが適切に動作しない場合は、QR コードと URL を手動で入力できます。

7. デバイス上の Microsoft Authenticator アプリの **[アカウント]** ページを調べ、アカウント情報が正しく、6 桁の確認コードが関連付けられていることを確認します。

    セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

8. コンピューターの **[Choose how you'll receive codes]\(コードの受信方法の選択\)** ページで、Amazon アカウント用にアプリで提供された 6 桁の確認コードを入力してから、 **[Verify code and continue]\(コードを確認して続行\)** を選びます。

9. テキスト メッセージなどのバックアップの確認方法の追加を含め、残りのサインイン プロセスを完了してから、 **[コードの送信]** を選択します。

10. コンピューターの **[Add a backup verification method]\(バックアップの確認方法の追加\)** ページで、Amazon アカウントのバックアップの確認方法で提供された 6 桁の確認コードを入力してから、 **[Verify code and continue]\(コードを確認して続行\)** を選びます。

11. **[まもなく完了します]** ページで、ご利用のコンピューターを信頼済みデバイスにするかどうかを決定してから、 **[了解しました。2 段階認証を有効にしてください]** を選択します。

    **[セキュリティの詳細設定]** ページが表示され、更新した 2 要素検証の詳細が示されます。

    >[!NOTE]
    >2 要素検証と Amazon アカウントの詳細については、「[About Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)」 (2 段階認証について) と「[Signing in with Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)」 (2 段階認証によるサインイン) を参照してください。

## <a name="next-steps"></a>次のステップ

- アプリにアカウントを追加すると、デバイスで Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
