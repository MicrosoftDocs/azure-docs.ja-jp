---
title: Microsoft Authenticator アプリを使用してアカウントをバックアップおよび回復する - Azure AD
description: Microsoft Authenticator アプリを使用して、バックアップされたアカウント資格情報をバックアップおよび復旧する方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530903"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび回復する

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

## <a name="recover-accounts-requiring-more-verification"></a>さらに検証が必要なアカウントを復旧する

個人用アカウントや職場または学校アカウントでプッシュ通知を使用する場合、情報を復旧する前に追加認証を提供する必要があることを示すアラートが画面に表示されます。 プッシュ通知では、ネットワーク経由で送信されることのない、特定のデバイスに関連付けられた資格情報を使用する必要があるので、デバイスで資格情報を作成する前に本人確認が必要です。

個人用 Microsoft アカウントの場合、代替電子メールや電話番号と共にパスワードを入力して本人確認することができます。 職場または学校アカウントの場合は、アカウント プロバイダーから提供された QR コードをスキャンする必要があります。

### <a name="to-provide-more-verification-for-personal-accounts"></a>個人用アカウントに対してさらに検証を提供するには

1. Microsoft Authenticator アプリの **[アカウント]** 画面で、復旧するアカウントをタップして、アカウントの全画面ビューを開きます。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="使用可能なアカウント タイルを含む Microsoft Authenticator アプリを示すスクリーンショット。" border="true":::

1. 復旧するアカウントのタイルをタップし、サインインするオプションをタップして復旧します。 パスワードを入力し、追加認証としてメール アドレスまたは電話番号を確認します。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Microsoft Authenticator ワンタイム パスワード コードを示すスクリーンショット。" border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>職場または学校アカウントに対してさらに検証を提供するには

1. Microsoft Authenticator アプリの **[アカウント]** 画面で、復旧するアカウントをタップして、アカウントの全画面ビューを開きます。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="使用可能なアカウント タイルを表示した Microsoft Authenticator アプリ" border="true":::

1. 全画面表示ビューで、QR コードをスキャンするオプションをタップして完全に復旧します。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Authenticator がワンタイム パスワードを確認コードとして提示する" border="true":::

>[!NOTE]
>QR コードとその取得方法の詳細については、管理者がセキュリティ情報を有効にしたかどうかに基づいて、[Microsoft Authenticator アプリの概要](./user-help-auth-app-download-install.md)に関するページまたは [Authenticator アプリを使用するためのセキュリティ情報の設定](./security-info-setup-auth-app.md)に関するページを参照してください。
>
>初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

## <a name="troubleshoot-backup-and-recovery-problems"></a>バックアップと復旧に関する問題のトラブルシューティング

バックアップを使用できない理由はいくつかあります。

- **オペレーティング システムの変更**: バックアップは、iOS の場合は iCloud、Android の場合は Microsoft のクラウド ストレージ プロバイダーに保存されます。 つまり、Android デバイスと iOS デバイスを切り替えるとバックアップは利用できなくなります。 切り替える場合は、Microsoft Authenticator アプリ内で手動でアカウントを作成し直す必要があります。

- **ネットワークの問題**: ネットワーク関連の問題が発生している場合は、ネットワークに接続していることと自分のアカウントに正しくサインインしていることを確認してください。

- **アカウントの問題**: アカウント関連の問題が発生している場合は、自分のアカウントに正しくサインインしていることを確認してください。 iOS の場合、これは、お使いの iPhone と同じ AppleID アカウントを使用して iCloud にサインインする必要があることを意味します。

- **不注意による削除**: 以前のデバイスから、またはオンライン ストレージ アカウントの管理中にバックアップ アカウントを削除した可能性があります。 このような状況では、アプリ内にアカウントを手動で再作成する必要があります。

- **既存の Microsoft Authenticator アカウント**: Microsoft Authenticator アプリ内にアカウントを既に設定してある場合、アプリではバックアップしたアカウントを復旧できません。 復旧防止により、アカウントの詳細情報が古い情報で上書きされないことが保証されます。 このような状況では、バックアップを復旧する前に Authenticator アプリで設定した既存のアカウントから既存のアカウント情報を削除する必要があります。

- **バックアップが最新ではない**: バックアップ情報が古い場合は、Microsoft の回復アカウントに再度サインインすることで、その情報を更新するように求められることがあります。 回復アカウントとは、バックアップを保存するために最初に使用した個人用の Microsoft アカウントです。 サインインが必要な場合は、メニューまたは操作バーに赤いドットが表示されるか、バックアップからの回復を完了するためにサインインを求める感嘆符のアイコンが表示されます。 適切なアイコンを選択すると、もう一度サインインして情報を更新するように求められます。

## <a name="next-steps"></a>次のステップ

アカウントの資格情報をバックアップし、新しいデバイスに復旧したので、Microsoft Authenticator アプリを使用した本人確認に進むことができます。 詳細については、[Microsoft Authenticator アプリを使用してアカウントにサインインする方法](user-help-sign-in.md)に関するページを参照してください。

## <a name="related-articles"></a>関連記事

- [Microsoft Authenticator アプリとは](user-help-auth-app-overview.md)

- [Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md)

- [Multi-factor Authentication](/azure/multi-factor-authentication/)