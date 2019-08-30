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
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba99001159277d9d221910cafa4f5165ae2bd812
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880768"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する

**適用対象:**

- iOS デバイス

Microsoft Authenticator アプリは、アカウント資格情報と、アカウントの順序などの関連アプリ設定をクラウドにバックアップします。 バックアップ後に、アプリを使用して情報を新しいデバイスに復旧することもできます。これにより、ロックアウトやアカウントの再作成の必要性を回避できる可能性があります。

> [!IMPORTANT]
> バックアップの保存場所ごとに 1 つの個人用 Microsoft アカウントと 1 つの iCloud アカウントが必要です。 ただし、その保存場所内で複数のアカウントをバックアップできます。 たとえば、個人用アカウント、学校のアカウントと、Facebook や Google などのサードパーティのアカウントがある場合があります。
>
> 個人およびサードパーティのアカウントの資格情報のみが格納されます。これには、本人確認を行うために必要なユーザー名とアカウント確認コードが含まれます。 電子メールやファイルなど、アカウントに関連付けられているその他の情報は格納されません。 アカウントをなんらかの方法で他の製品やサービスに関連付けたり共有したりすることはありません。 最後に、IT 管理者がこれらのアカウントに関する情報を取得することはありません。

## <a name="back-up-your-account-credentials"></a>アカウントの資格情報のバックアップ

資格情報をバックアップする前に、次の両方が必要です。

- 復旧アカウントとして機能する個人用 [Microsoft アカウント](https://account.microsoft.com/account)。

- 実際の保存場所となる [iCloud アカウント](https://www.icloud.com/)。

両方のアカウントへのサインインを要求すると、バックアップ情報のセキュリティが向上します。

### <a name="to-turn-on-cloud-backup"></a>クラウド バックアップを有効にするには

- iOS デバイスで、 **[設定]** 、 **[バックアップ]** の順に選択し、 **[iCloud のバックアップ]** を有効にします。

    アカウントの資格情報は、自分の iCloud アカウントにバックアップされます。

    ![iCloud のバックアップ設定の場所を表示している iOS の設定画面](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>アカウント資格情報を新しいデバイスに復旧する

情報をバックアップしたときに設定したのと同じ Microsoft 復旧アカウントを使用して、アカウント資格情報を iCloud アカウントから復旧できます。

### <a name="to-recover-your-information"></a>情報を復旧するには

1. iOS デバイスで、Microsoft Authenticator アプリを開き、画面の下部から **[回復の開始]** を選択します。

    ![[回復の開始] をクリックする場所を示している Microsoft Authenticator アプリ](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. バックアップ プロセス中に使用したものと同じ個人用 Microsoft アカウントを使用して、復旧アカウントにサインインします。

    アカウント資格情報が新しいデバイスに復旧されます。

復旧が完了すると、Microsoft Authenticator アプリで個人用 Microsoft アカウントの検証コードが新旧の携帯電話間で異なることに気付く可能性があります。 各デバイスに独自の一意の資格情報があるためコードが異なりますが、どちらも有効で、関連付けられている携帯電話を使用してサインインするときに機能します。

## <a name="recover-additional-accounts-requiring-more-verification"></a>さらに検証が必要な追加のアカウントを復旧する

個人用アカウント、職場アカウント、または学校アカウントでプッシュ通知を使用する場合、情報を復旧する前に追加の検証を提供する必要があることを示すアラートが画面に表示されます。 プッシュ通知では、ネットワーク経由で送信されることのない、特定のデバイスに関連付けられた資格情報を使用する必要があるので、デバイスで資格情報を作成する前に本人確認が必要です。

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
    >初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。
    >
    >QR コードを取得する方法の詳細については、管理者がセキュリティ情報を有効にしたかどうかに基づいて、「[Microsoft Authenticator アプリの概要](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)」または「[Authenticator アプリを使用するようにセキュリティ情報を設定する](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)」を参照してください。

## <a name="troubleshooting-backup-and-recovery-problems"></a>バックアップと復旧に関する問題のトラブルシューティング

バックアップを使用できない理由はいくつかあります。

- **オペレーティング システムの変更。** バックアップは、携帯電話のオペレーティング システムによって提供されるクラウド ストレージ オプションに保存されるので、Android と iOS の間を切り替えるとバックアップを使用できません。 このような状況では、アプリ内にアカウントを手動で再作成する必要があります。

- **ネットワークまたはパスワードの問題。** ネットワークに接続し、前回 iPhone で使用したのと同じ AppleId を使用して iCloud アカウントにサインインしていることを確認します。

- **不注意による削除。** 以前のデバイスから、またはクラウド ストレージ アカウントの管理中にバックアップ アカウントを削除した可能性があります。 このような状況では、アプリ内にアカウントを手動で再作成する必要があります。

- **既存の Microsoft Authenticator アカウント。** Microsoft Authenticator アプリ内にアカウントを既に設定してある場合、アプリではバックアップしたアカウントを復旧できません。 復旧防止により、アカウントの詳細情報が古い情報で上書きされないことが保証されます。 このような状況では、バックアップを復旧する前に Authenticator アプリで設定した既存のアカウントから既存のアカウント情報を削除する必要があります。

## <a name="next-steps"></a>次の手順

アカウントの資格情報をバックアップし、新しいデバイスに復旧したので、Microsoft Authenticator アプリを使用した本人確認に進むことができます。 詳細については、[Microsoft Authenticator アプリを使用してアカウントにサインインする方法](user-help-sign-in.md)に関するページを参照してください。

## <a name="related-topics"></a>関連トピック

- [Microsoft Authenticator アプリとは](user-help-auth-app-overview.md)

- [Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md)

- [Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
