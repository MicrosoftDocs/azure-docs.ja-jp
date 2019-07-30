---
title: アプリに手動でアカウントを追加する - Azure Active Directory | Microsoft Docs
description: 2 要素認証のために Microsoft Authenticator アプリにアカウントを手動で追加する方法。
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
ms.openlocfilehash: 7f2145811338200b38e84766459844b4c83e5a80
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382756"
---
# <a name="manually-add-an-account-to-the-app"></a>アプリにアカウントを手動で追加する

カメラで QR コードをキャプチャできない場合は、2 要素検証のために Microsoft Authenticator アプリにご自分のアカウント情報を手動で追加できます。 この方法は、職場または学校アカウントと Microsoft 以外のアカウントに対して使用できます。

アカウント用に提供されているコードでは大文字と小文字が区別されません。また、Microsoft Authenticator アプリに追加するときにスペースを入れる必要がありません。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

## <a name="add-your-work-or-school-account"></a>職場または学校のアカウントを追加する

1. コンピューター上で、 **[モバイル アプリケーションの構成]** ページの **[コード]** と **[URL]** の情報を書き留めます。 このページを開いたままにして、コードと URL を確認できるようにします。

    ![QR コードを提供する画面](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[職場または学校アカウント]** を選択します。

3. **[またはコードを手動で入力]** を選択します。

    ![QR コードをスキャンする画面](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. 手順 1 の **[コード]** と **[URL]** を入力して、 **[完了]** を選択します。

    ![コードと URL を入力する画面](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    アプリの **[アカウント]** 画面に、アカウント名と 6 桁の認証コードが表示されます。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

## <a name="add-your-google-account"></a>Google アカウントを追加する

1. コンピューター上で、QR コードが記載された **[Set up Authenticator]\(Authenticator の設定\)** ページから **[CAN'T SCAN IT]\(スキャンできない\)** を選択します。

    秘密コードが記載された **[Can't scan barcode]\(バーコードをスキャンできない\)** ページが表示されます。 このページを開いたままにして、秘密コードを確認できるようにします。

2. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。次に、 **[またはコードを手動で入力]** を選択します。

3. **[アカウント名]** (Google など) を入力し、手順 1 の **[秘密鍵]** を入力してから **[完了]** を選択します。

4. コンピューターの **[Set up Authenticator]\(Authenticator の設定\)** ページで、Google アカウント用にアプリで提供された 6 桁の確認コードを入力してから、 **[確認]** を選びます。

    アプリの **[アカウント]** 画面に、アカウント名と 6 桁の認証コードが表示されます。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

    >[!NOTE]
    >2 要素検証と Google アカウントの詳細については、「[2 段階認証プロセスを有効にする](https://support.google.com/accounts/answer/185839)」と「[2 段階認証プロセスの詳細](https://www.google.com/landing/2step/help.html)」を参照してください。

## <a name="add-your-facebook-account"></a>Facebook アカウントを追加する

1. **[Set up via Third Party Authenticator]\(サード パーティの Authenticator による設定)** ページを開きます。このページには、QR コードと、アプリに入力するためのコードが含まれています。 このページを開いたままにして、コードとを確認できるようにします。

2. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。次に、 **[またはコードを手動で入力]** を選択します。

3. **[アカウント名]** (Facebook など) を入力し、手順 1 の **[秘密鍵]** を入力してから **[完了]** を選択します。

4. コンピューターの **[2 要素認証]** ページで、Facebook アカウント用にアプリで提供された 6 桁の確認コードを入力してから、 **[確認]** を選択します。

    アプリの **[アカウント]** 画面に、アカウント名と 6 桁の認証コードが表示されます。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

    >[!NOTE]
    >2 要素検証と Facebook アカウントの詳細については、「[二段階認証の概要とそのしくみ](https://www.facebook.com/help/148233965247823)」に関するページを参照してください。

## <a name="add-your-amazon-account"></a>Amazon アカウントを追加する

2 要素検証を有効にし、アプリにアカウントを追加して、Amazon アカウントを追加できます。

1. コンピューター上で、QR コードが記載された **[Choose how you'll receive codes]\(コードの受信方法の選択\)** ページから **[Can't scan barcode]\(バーコードをスキャンできない\)** を選択します。

    秘密コードとともに、 **[Can't scan barcode]\(バーコードをスキャンできない\)** メッセージが表示されます。 このメッセージを開いたままにして、秘密コードを確認できるようにします。

2. Microsoft Authenticator アプリを開き、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[他のアカウント (Google、Facebook など)]** を選びます。次に、 **[またはコードを手動で入力]** を選択します。

3. **[アカウント名]** (Amazon など) を入力し、手順 1 の **[秘密鍵]** を入力してから **[完了]** を選択します。

4. テキスト メッセージなどのバックアップの確認方法の追加を含め、残りのサインイン プロセスを完了してから、 **[コードの送信]** を選択します。

5. コンピューターの **[Add a backup verification method]\(バックアップの確認方法の追加\)** ページで、Amazon アカウントのバックアップの確認方法で提供された 6 桁の確認コードを入力してから、 **[Verify code and continue]\(コードを確認して続行\)** を選びます。

6. [まもなく完了します] ページで、ご利用のコンピューターを信頼済みデバイスにするかどうかを決定してから、 **[了解しました。2 段階認証を有効にしてください]** を選択します。

    **[セキュリティの詳細設定]** ページが表示され、更新した 2 要素検証の詳細が示されます。

    >[!NOTE]
    >2 要素検証と Amazon アカウントの詳細については、「[About Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)」 (2 段階認証について) と「[Signing in with Two-Step Verification](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)」 (2 段階認証によるサインイン) を参照してください。    

## <a name="next-steps"></a>次の手順

- アプリにアカウントを追加すると、デバイスで Microsoft Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- 個人用 Microsoft アカウントの確認コードの取得に問題がある場合は、「[Microsoft アカウントのセキュリティ情報と確認コード](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes)」の記事の「**確認コードの問題のトラブルシューティング**」セクションを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
