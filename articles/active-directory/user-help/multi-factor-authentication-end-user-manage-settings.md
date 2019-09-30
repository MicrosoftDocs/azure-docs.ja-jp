---
title: 2 要素認証の設定を管理する - Azure Active Directory | Microsoft Docs
description: 2 要素認証に関連するセキュリティ検証方法の詳細を変更する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ed7d812a9c1cba356277a4454234531ce8d41ef
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616145"
---
# <a name="manage-your-two-factor-verification-method-settings"></a>2 要素認証方法の設定を管理する

職場または学校アカウントのセキュリティ検証方法を設定した後は、次のような関連する詳細情報を更新できます。

- 既定のセキュリティ検証方法の変更。

- 電話番号など、セキュリティ検証方法の詳細の追加または更新。

- 新しい Authenticator アプリ の設定、または Authenticator アプリからのデバイスの削除。

## <a name="using-the-additional-security-verification-page"></a>[追加のセキュリティ確認] ページを使用する

2 要素認証の有効化と管理について組織から具体的な手順が提供されている場合は、その手順に従う必要があります。 それ以外の場合は、[[追加のセキュリティ確認]](https://aka.ms/mfasetup) ページからセキュリティ検証方法の設定にアクセスできます。

>[!Note]
>画面に表示される内容と、この記事の内容が一致しない場合は、管理者によってセキュリティ情報 (プレビュー) エクスペリエンスが有効にされているか、または組織に独自のカスタム ポータルがあることを意味します。 セキュリティ情報エクスペリエンスについて詳しくは、「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」をご覧ください。 組織のカスタム ポータルについて詳しくは、ヘルプ デスクにお問い合わせください。

### <a name="to-get-to-the-additional-security-verification-page"></a>[追加のセキュリティ確認] ページに移動するには

- [https://aka.ms/MFASetup](https://aka.ms/mfasetup ) にアクセスします。

    ![[追加のセキュリティ確認] 画面と、利用可能なセキュリティ検証方法の詳細](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    このリンクをクリックしても機能しない場合は、以下の手順に従って **[追加のセキュリティ確認]** ページにアクセスすることもできます。

    1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。

    2. 右上でアカウント名を選択し、**プロファイル**を選択します。

    3. **[追加のセキュリティ確認]** を選択します。  

        ![[追加のセキュリティ確認] ページへのマイ アプリのリンク](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>**[追加のセキュリティ確認]** ページの **[アプリ パスワード]** セクションについて詳しくは、「[2 段階認証のアプリ パスワードを管理する](multi-factor-authentication-end-user-app-passwords.md)」をご覧ください。 アプリ パスワードは、2 要素認証がまだサポートされていないアプリに対してのみ使用する必要があります。

## <a name="change-your-default-security-verification-method"></a>既定のセキュリティ検証方法を変更する

ユーザー名とパスワードを使用して職場または学校アカウントにサインインすると、選択したセキュリティ検証方法が自動的に表示されます。 組織の要件によっては、これは Authenticator アプリ、テキスト メッセージ、または電話呼び出しによる通知または確認コードである場合があります。

使用する既定のセキュリティ検証方法を変更する場合は、ここから実行できます。

### <a name="to-change-your-default-security-verification-method"></a>既定のセキュリティ検証方法を変更するには

1. **[追加のセキュリティ確認]** ページで、 **[必要に応じて設定オプションを変更してください]** のドロップダウン リストから、使用する方法を選択します。 すべてのオプションが表示されますが、組織で使用できるオプションのみを選択できます。

    - **[アプリケーションで通知する]** 。 検証プロンプトが待機していることを示す通知を Authenticator アプリで受け取ります。

    - **[認証用電話に電話をかける]** 。 情報の確認を求める電話呼び出しをモバイル デバイスで受け取ります。

    - **[認証用電話にコードを送信する]** 。 モバイル デバイスでテキスト メッセージの一部として確認コードを受け取ります。 職場または学校アカウントの検証プロンプトに、このコードを入力する必要があります。

    - **[会社電話に発信]** 。 情報の確認を求める電話呼び出しを会社の電話で受け取ります。

    - **[アプリの確認コードを使用する]** 。 Authenticator アプリを使用して、職場または学校アカウントからのプロンプトに入力する確認コードを受け取ります。

2. **[保存]** を選択します。

## <a name="add-or-change-your-phone-number"></a>電話番号を追加または変更する

**[追加のセキュリティ確認]** ページでは、新しい電話番号を追加したり、既存の番号を更新したりすることができます。

>[!Important]
>メインの電話を紛失したり盗まれたりした場合、または新しい電話を入手して元のメインの電話番号を使用しなくなった場合に、アカウントからロックアウトされないようにするため、サブの電話番号を追加することを強くお勧めします。

### <a name="to-change-your-phone-numbers"></a>代表電話番号を変更するには

1. **[追加のセキュリティ確認]** ページの **[応答に使用する方法を選択してください]** セクションで、 **[認証用電話]** (メインのモバイル デバイス) と **[会社電話]** の電話番号情報を更新します。

2. **[代替の認証用電話]** オプションの横にあるボックスをオンにして、メインのデバイスにアクセスできない場合にテキスト メッセージまたは電話を受けることができるサブの電話番号を入力します。

3. **[保存]** を選択します。

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>新しいアカウントを Microsoft Authenticator アプリに追加する

[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) または [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458) 用の Microsoft Authenticator アプリで、職場または学校アカウントを設定できます。

以前に Microsoft Authenticator アプリで職場または学校アカウントを設定したことがある場合、もう一度行う必要はありません。

1. **[追加のセキュリティ確認]** ページの **[応答に使用する方法を選択してください]** セクションで、 **[Authenticator アプリの設定]** を選択します。

    ![Microsoft Authenticator アプリで職場または学校アカウントを設定する](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. モバイル デバイスを使用した QR コードのスキャンなど、画面の指示に従った後、 **[次へ]** を選択します。

    Microsoft Authenticator アプリで通知を承認し、情報を確認するように求められます。

3. **[保存]** を選択します。

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリからアカウントまたはデバイスを削除する

Microsoft Authenticator アプリからアカウントを削除することができ、職場または学校アカウントからデバイスを削除することができます。 通常、紛失したデバイス、盗まれたデバイス、または古いデバイスをアカウントから完全に削除するにはデバイスを削除し、接続の問題を解決したり、アカウントの変更 (新しいユーザー名など) に対応したりするには、アカウントを削除します。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>職場または学校アカウントからデバイスを削除するには

1. **[追加のセキュリティ確認]** ページの **[応答に使用する方法を選択してください]** セクションで、 **[Authenticator アプリの設定]** を選択します。

2. **[保存]** を選択します。

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリからアカウントを削除するには

- Microsoft Authenticator アプリから、削除するデバイスの横にある **[削除]** ボタンを選択します。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>信頼されたデバイスで 2 要素認証のプロンプトを有効にする

組織の設定によっては、ブラウザーで 2 段階認証を実行するときに、 **[今後 X 日間はこのメッセージを表示しない]** というチェック ボックスが表示される場合があります。 このチェック ボックスをオンにして 2 要素認証のプロンプトを停止した場合、デバイスを紛失したとき、またはデバイスが侵害された可能性があるときは、アカウントを保護するために、2 要素認証のプロンプトを再び有効にする必要があります。 残念ながら、1 台のデバイスについてプロンプトを再び有効にすることはできません。 すべてのデバイスのプロンプトを同時に有効にする必要があります。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>デバイスに対する 2 要素認証のプロンプトを再び有効にするには

- **[追加のセキュリティ確認]** ページで、 **[以前の信頼済みデバイスに Multi-Factor Authentication を復元する]** を選択します。

    任意のデバイスで次回サインインするときに、2 要素認証を実行するよう求められます。

## <a name="next-steps"></a>次の手順

2 要素認証を追加または更新したら、アプリ パスワードを管理したり、サインインしたり、2 要素認証関連の一般的な問題でサポートを受けたりできます。

- 2 要素認証がサポートされていないアプリについて、[2 要素認証のアプリ パスワードを管理](multi-factor-authentication-end-user-app-passwords.md)します。

- [2 要素認証を使用してサインインする](multi-factor-authentication-end-user-signin.md)

- [2 要素認証のサポートを受ける](multi-factor-authentication-end-user-troubleshoot.md)
