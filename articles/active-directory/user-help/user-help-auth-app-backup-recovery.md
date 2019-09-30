---
title: Microsoft Authenticator アプリを使用したバックアップと復旧 - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する方法を説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4e45fbe6533832765e90e5cf244f0dcbcf0b72
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035130"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する

**適用対象:**

- バージョン 5.7.0 以降を実行している iOS デバイス

- バージョン 6.6.0 以降を実行している Android デバイス

Microsoft Authenticator アプリは、アカウント資格情報と、アカウントの順序などの関連アプリ設定をクラウドにバックアップします。 バックアップ後に、アプリを使用して情報を新しいデバイスに復旧することもできます。これにより、ロックアウトやアカウントの再作成の必要性を回避できる可能性があります。

バックアップの保存場所ごとに個人用の Microsoft アカウントが 1 つ必要になりますが、iOS では iCloud アカウントも必要になります。 複数のアカウントをその 1 つの場所に保存しておくことができます。 たとえば、個人用アカウント、職場または学校アカウントのほか、Facebook や Google などの Microsoft 以外の個人用アカウントがある場合があります。

> [!IMPORTANT]
> 保存されるのは、個人およびサードパーティのアカウントの資格情報のみです。これには、本人確認に必要なユーザー名とアカウント確認コードが含まれます。 電子メールやファイルなど、アカウントに関連付けられているその他の情報は格納されません。 アカウントをなんらかの方法で他の製品やサービスに関連付けたり共有したりすることはありません。 最後に、IT 管理者がこれらのアカウントに関する情報を取得することはありません。

## <a name="back-up-your-account-credentials"></a>アカウントの資格情報のバックアップ

資格情報をバックアップする前に、次のものが必要です。

- 復旧アカウントとして機能する個人用 [Microsoft アカウント](https://account.microsoft.com/account)。

- **iOS の場合のみ**、実際の保存場所用の [iCloud アカウント](https://www.icloud.com/)が必要です。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>iOS デバイスのクラウド バックアップを有効にするには

- iOS デバイスで、 **[設定]** 、 **[バックアップ]** の順に選択し、 **[iCloud のバックアップ]** を有効にします。

    アカウントの資格情報は、自分の iCloud アカウントにバックアップされます。

    ![iCloud のバックアップ設定の場所を表示している iOS の設定画面](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Android デバイスのクラウド バックアップを有効にするには

- Android デバイスで、 **[設定]** 、 **[バックアップ]** の順に選択し、 **[Cloud backup]\(クラウド バックアップ\)** を有効にします。

    アカウントの資格情報が自分のクラウド アカウントにバックアップされます。

    ![バックアップ設定の場所を表示している、Android の設定画面](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>アカウント資格情報を新しいデバイスに復旧する

お使いのアカウントの資格情報は自分のクラウド アカウントから復旧できますが、最初に、復旧するアカウントが Microsoft Authenticator アプリに存在しないことを確認する必要があります。 たとえば、個人用の Microsoft アカウントを復旧する場合は、Authenticator アプリでまだ個人用の Microsoft アカウントが設定されていないことを確認する必要があります。 既存のアカウントを誤って上書きまたは消去しないようにするために、この確認は重要です。

### <a name="to-recover-your-information"></a>情報を復旧するには

1. お使いのモバイル デバイスで、Microsoft Authenticator アプリを開き、画面の下部から **[回復の開始]** を選択します。

    ![[回復の開始] をクリックする場所を示している Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. バックアップ プロセス中に使用したものと同じ個人用 Microsoft アカウントを使用して、復旧アカウントにサインインします。

    アカウント資格情報が新しいデバイスに復旧されます。

復旧が完了すると、Microsoft Authenticator アプリで個人用 Microsoft アカウントの検証コードが新旧の携帯電話間で異なることに気付く可能性があります。 各デバイスに独自の一意の資格情報があるためコードが異なりますが、どちらも有効で、関連付けられている携帯電話を使用してサインインするときに機能します。

## <a name="recover-additional-accounts-requiring-more-verification"></a>さらに検証が必要な追加のアカウントを復旧する

個人用アカウントや職場または学校アカウントでプッシュ通知を使用する場合、情報を復旧する前に追加認証を提供する必要があることを示すアラートが画面に表示されます。 プッシュ通知では、ネットワーク経由で送信されることのない、特定のデバイスに関連付けられた資格情報を使用する必要があるので、デバイスで資格情報を作成する前に本人確認が必要です。

個人用 Microsoft アカウントの場合、代替電子メールや電話番号と共にパスワードを入力して本人確認することができます。 職場または学校アカウントの場合は、アカウント プロバイダーから提供された QR コードをスキャンする必要があります。

### <a name="to-provide-additional-verification-for-personal-accounts"></a>個人用アカウントに対して追加の検証を提供するには

1. Microsoft Authenticator アプリの **[アカウント]** 画面で、復旧するアカウントの横にあるドロップダウン矢印を選択します。

    ![使用可能なアカウントとそれに関連付けられたドロップダウン矢印を表示している Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. **[Sign in to recover]\(サインインして復旧する\)** を選択し、追加の検証として電子メール アドレスまたは電話番号を確認します。

    ![サインイン情報を入力できる Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>職場または学校アカウントに対して追加の検証を提供するには

1. Microsoft Authenticator アプリの **[アカウント]** 画面で、復旧するアカウントの横にあるドロップダウン矢印を選択します。

    ![使用可能なアカウントとそれに関連付けられたドロップダウン矢印を表示している Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. **[Scan QR code to recover] (QR コードをスキャンして復旧)** を選択してから、QR コードをスキャンします。

    ![QR コードをスキャンできる Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >QR コードとその取得方法の詳細については、管理者がセキュリティ情報を有効にしたかどうかに基づいて、[Microsoft Authenticator アプリの概要](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)に関するページまたは [Authenticator アプリを使用するためのセキュリティ情報の設定](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)に関するページを参照してください。
    >
    >初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

## <a name="troubleshoot-backup-and-recovery-problems"></a>バックアップと復旧に関する問題のトラブルシューティング

バックアップを使用できない理由はいくつかあります。

- **オペレーティング システムの変更。** バックアップは、iOS の場合は iCloud、Android の場合は Microsoft のクラウド ストレージ プロバイダーに保存されます。 つまり、Android デバイスと iOS デバイスを切り替えるとバックアップは利用できなくなります。 切り替える場合は、Microsoft Authenticator アプリ内で手動でアカウントを作成し直す必要があります。

- **ネットワークの問題。** ネットワーク関連の問題が発生している場合は、ネットワークに接続していることと自分のアカウントに正しくサインインしていることを確認してください。

- **アカウントの問題。** アカウント関連の問題が発生している場合は、自分のアカウントに正しくサインインしていることを確認してください。 iOS の場合、これは、お使いの iPhone と同じ AppleID アカウントを使用して iCloud にサインインする必要があることを意味します。

- **不注意による削除。** 以前のデバイスから、またはクラウド ストレージ アカウントの管理中にバックアップ アカウントを削除した可能性があります。 このような状況では、アプリ内にアカウントを手動で再作成する必要があります。

- **既存の Microsoft Authenticator アカウント。** Microsoft Authenticator アプリ内にアカウントを既に設定してある場合、アプリではバックアップしたアカウントを復旧できません。 復旧防止により、アカウントの詳細情報が古い情報で上書きされないことが保証されます。 このような状況では、バックアップを復旧する前に Authenticator アプリで設定した既存のアカウントから既存のアカウント情報を削除する必要があります。

- **バックアップが最新ではない。** バックアップ情報が古い場合は、Microsoft の回復アカウントに再度サインインすることで、その情報を更新するように求められることがあります。 回復アカウントとは、バックアップを保存するために最初に使用した個人用の Microsoft アカウントです。 サインインが必要な場合は、メニューまたは操作バーに赤いドットが表示されます。 この赤いドットを選択すると、もう一度サインインして情報を更新するように求められます。

## <a name="next-steps"></a>次の手順

アカウントの資格情報をバックアップし、新しいデバイスに復旧したので、Microsoft Authenticator アプリを使用した本人確認に進むことができます。 詳細については、[Microsoft Authenticator アプリを使用してアカウントにサインインする方法](user-help-sign-in.md)に関するページを参照してください。

## <a name="related-articles"></a>関連記事

- [Microsoft Authenticator アプリとは](user-help-auth-app-overview.md)

- [Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
