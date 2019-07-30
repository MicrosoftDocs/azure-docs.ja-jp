---
title: Microsoft Authenticator アプリを使用してアカウントにサインインする - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator アプリを使用して、職場または学校アカウント、または個人用の Microsoft アカウントと Microsoft 以外のアカウントにサインインします。このとき、2 要素認証または電話によるサインインが使用されます。
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
ms.openlocfilehash: bc136cdc88c67e3b26aefc4982f49a2547e456db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60474013"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを使用してアカウントにサインインする
Microsoft Authenticator アプリは、2 要素認証を使用する場合に、アカウントにサインインするために役立ちます。 2 要素認証は、特に機密情報を閲覧する際に、アカウントにより安全にアクセスするために役立ちます。 パスワードは忘れたり、盗まれたり、侵害されたりすることがあるため、2 要素認証は、他のユーザーの侵入を困難にすることでお客様のアカウントの保護に役立つ追加のセキュリティ手順です。

Microsoft Authenticator アプリは次のような複数の方法で使用できます。

- お客様が自分のユーザー名とパスワードを使ってサインインした後に、2 つ目の認証方法が求められるようにする。

- ユーザー名と、指紋、顔、または PIN によるモバイル デバイスを利用して、パスワードを必要としないサインインを提供する。

  >[!Important]
  >この電話によるサインイン方法は、職場または学校および個人用の Microsoft アカウントにのみ利用できます。 Microsoft 以外のアカウントでは、標準の 2 要素認証プロセスを使用する必要があります。

## <a name="prerequisites"></a>前提条件
Microsoft Authenticator アプリを使用するには、次の条件を満たしている必要があります。

 1. Microsoft Authenticator アプリをダウンロードしてインストールします。 まだ完了していない場合は、[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)に関する記事を参照してください。

 2. 職場または学校、個人用、およびサードパーティのアカウントを Microsoft Authenticator アプリに追加します。 詳細な手順については、[職場または学校アカウントの追加](user-help-auth-app-add-work-school-account.md)、[個人用アカウントの追加](user-help-auth-app-add-personal-ms-account.md)、および[個人用の Microsoft 以外のアカウントの追加](user-help-auth-app-add-non-ms-account.md)に関する記事を参照してください。

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>職場または学校アカウントで電話によるサインインを有効にして使用する
電話によるサインインは、2 段階認証プロセスの一種です。 ユーザーは、自分が知っていることと持っているものを提示することで本人確認を行う必要がありますが、電話によるサインインを使用すると、アカウント パスワードの入力をスキップし、携帯電話上ですべての本人確認を実行できます。

電話によるサインインを有効にする前に、2 要素認証を有効にする必要があります。 アカウントの 2 要素認証を有効にする方法の詳細については、[職場または学校アカウントの追加](user-help-auth-app-add-work-school-account.md)と[個人用アカウントの追加](user-help-auth-app-add-personal-ms-account.md)に関する記事を参照してください。

電話によるサインインは、iOS デバイスと Android 6.0 以降を実行している Android デバイスでのみ使用できます。

### <a name="turn-on-phone-sign-in"></a>電話によるサインインを有効にする 

- Microsoft Authenticator アプリを開き、職場または学校アカウントにアクセスして電話によるサインインを有効にします。

    - **このアイコン ![設定されていることを示すアイコン](media/user-help-auth-app-sign-in/icon.png) が表示される場合。** このアイコンが職場または学校アカウント名の横に表示される場合は、そのアカウントに電話によるサインインが既に設定されていることを意味します。 アプリ外で認証要求に関する通知を受け取るには、アカウントのプッシュ通知を追加するように求められる場合があります。

    - **2 要素認証にアプリを使用している場合。** アプリと 2 要素認証を既に使用している場合は、アカウント名の横にあるドロップダウン矢印を選択して、 **[電話によるサインインを有効にする]** を選択できます。
    
    - **職場または学校アカウントが見つからない場合。** アプリの **[アカウント]** 画面に職場または学校アカウントが表示されない場合は、まだアプリに追加していないことを意味します。 [職場または学校アカウントの追加](user-help-auth-app-add-work-school-account.md)に関する記事の手順に従って、職場または学校アカウントを追加します。

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>電話によるサインインを使用してアカウントにサインインします。
電話によるサインインを有効にした後は、Microsoft Authenticator アプリのみを使用してサインインできます。

1. 職場または学校のアカウントにサインインします。

    ユーザー名を入力すると、 **[サインインの承認]** 画面が開き、2 桁の数字が表示され、Microsoft Authenticator アプリでサインインするように求められます。 このサインイン方法を使用しない場合は、 **[Use your password instead]\(代わりにパスワードを使用する\)** を選択し、パスワードを使用してサインインします。

    ![コンピューター上の [サインインの承認] ボックス](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. デバイス上で通知または Microsoft Authenticator アプリを開き、コンピューターの **[サインインの承認]** 画面に表示される数字と一致する数字をタップします。

    ![デバイス上の [サインインの承認] ボックス](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. サインインを承認する場合は **[承認]** を選択します。 承認しない場合は、 **[拒否]** を選択します。

4. 携帯電話の暗証番号または生体認証キーを使用して、認証を完了します。

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>個人用 Microsoft アカウントで電話によるサインインを有効にして使用する
Outlook.com、Xbox、または Skype などにサインインするために使用するアカウントなど、個人の Microsoft アカウントで電話によるサインインを有効にできます。

>[!NOTE]
>Microsoft Authenticator アプリでアカウントを保護するには、暗証番号または生体認証ロックがデバイスに必要です。 携帯電話がロック解除状態のままにされている場合、アプリは、電話によるサインインを有効にする前にロックを設定するよう求めるメッセージを表示します。

### <a name="turn-on-phone-sign-in"></a>電話によるサインインを有効にする 

- Microsoft Authenticator アプリを開き、個人用 Microsoft アカウントにアクセスして電話によるサインインを有効にします。

    - **このアイコン ![設定されていることを示すアイコン](media/user-help-auth-app-sign-in/icon.png) が表示される場合。** このアイコンがアカウント名の横に表示される場合は、そのアカウントに電話によるサインインが既に設定されていることを意味します。 アプリ外で認証要求に関する通知を受け取るには、アカウントのプッシュ通知を追加するように求められる場合があります。

    - **2 要素認証にアプリを使用している場合。** アプリと 2 要素認証を既に使用している場合は、アカウント名の横にあるドロップダウン矢印を選択して、 **[電話によるサインインを有効にする]** を選択できます。
    
    - **アカウントが見つからない場合。** アプリの **[アカウント]** 画面にアカウントが表示されない場合は、まだアプリに追加していないことを意味します。 [個人用 Microsoft アカウントの追加](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account)に関する記事の手順に従って、個人用 Microsoft アカウントを追加します。

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>電話によるサインインを使用してアカウントにサインインする

1. 個人用の Microsoft アカウントでサインイン ページに移動し、パスワードを入力する代わりに **[代わりに Microsoft Authenticator アプリを使用する]** リンクを選択します。 

    Microsoft から携帯電話に通知が送信されます。 

2. 通知を承認します。 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>2 要素認証を使用してアカウントにサインインする
標準の 2 要素認証方法では、ログインするデバイスにご自分のユーザー名とパスワードを入力し、Microsoft Authenticator アプリが通知を送信するかどうか、または Microsoft Authenticator アプリの **[アカウント]** 画面から関連付けられている確認コードをコピーするかを選択します。 Microsoft Authenticator アプリにアカウントを追加するプロセスの一環として、アカウントの 2 要素認証を有効にします。

>[!Note]
>職場または学校アカウント、または個人用アカウントが Microsoft Authenticator アプリの **[アカウント]** 画面に表示されない場合は、そのアカウントが Microsoft Authenticator アプリに追加されていないことを意味します。 アカウントを追加するには、[職場または学校アカウントの追加](user-help-auth-app-add-work-school-account.md)または[個人用アカウントの追加](user-help-auth-app-add-personal-ms-account.md)に関する記事を参照してください。

2 要素認証のさまざまな方法を使用して職場または学校アカウント、または個人用アカウントにサインインするために必要な手順については、「[2 段階認証またはセキュリティ情報を使ったサインイン方法](user-help-sign-in.md)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
|**質問**|**ソリューション**|
|--------------|-------------|
|**電話によるサインインは、パスワードの入力よりどの程度安全ですか。**|現在、ほとんどの人は、ユーザー名とパスワードを使って Web サイトやアプリにサインインします。 残念なことに、パスワードは、なくしたり、盗まれたり、ハッカーに推測されたりすることがよくあります。<br><br>Microsoft Authenticator アプリを設定すると、携帯電話の暗証番号または生体認証ロックによって保護されているアカウントのロックを解除するためのキーが電話上に作成されます。 このキーは、サインイン時に本人確認のために使用されます。<br><br>**重要**<br>お客様のデータは、キーを保護する目的にのみ、ローカルで使用されます。 クラウドに送信されたり格納されることはありません。|
|**電話によるサインインは 2 段階認証プロセスに代わるものですか。2 段階認証を無効にする必要がありますか。**|電話によるサインインは 2 段階認証の一種であり、2 つの手順の両方がモバイル デバイス上で行われます。 アカウントのセキュリティを強化するために、2 段階認証プロセスを有効にしておく必要があります。|
|**アカウントの 2 段階認証を有効にしたままにする場合、2 つの通知を承認する必要がありますか。**| いいえ。 電話を使用して Microsoft アカウントにサインインすると 2 段階認証とみなされ、2 回目の承認は必要ありません。|
|**電話を紛失した場合、または電話が手元にない場合は、どのようにしてアカウントにアクセスできますか。アカウントへのアクセス方法を教えてください。**| サインイン ページで [Use a password instead]\(代わりにパスワードを使用する)\ リンクを選択すれば、いつでもパスワードの使用に戻すことができます。 ただし、2 段階認証を使用している場合は、第 2 の方法で身元を証明する必要があることに注意してください。<br><br>**重要**<br>アカウントに、最新の認証方法を複数関連付けてあることを確認することを強くお勧めします。<br><br>個人アカウントの認証方法は [[セキュリティ設定]](https://account.live.com/proofs/manage) ページから管理することができます。 職場または学校アカウントの場合、管理者がセキュリティ情報をオンに設定している場合は、組織の [[追加のセキュリティ確認]](https://aka.ms/MFASetup) ページまたは **[Keep your account secure]\(アカウントのセキュリティ保護を維持\)** ページに移動します。 セキュリティ情報の詳細については、「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」を参照してください。<br><br>認証方法を管理できない場合は、管理者に連絡する必要があります。|
|**この機能の使用を停止してパスワードの入力に戻すにはどうすればよいですか。**|個人アカウントの場合は、サインイン時に **[Use a password instead]\(代わりにパスワードを使用する\)** リンクを選択します。 前回の選択内容が記憶され、次にサインインするときは既定で表示されます。 電話によるサインインに戻る場合は、サインイン時に **[Use an app instead]\(代わりにアプリを使用する)** リンクを選択します。<br><br>職場または学校アカウントの場合、Microsoft Authenticator アプリの **[設定]** ページからデバイスの登録を解除するか、プロファイルの **[デバイスとアクティビティ]** 領域でデバイスを無効にする必要があります。 プロファイルからデバイスを無効にする方法の詳細については、「[マイ アプリ ポータルからプロファイルとアカウント情報を更新する](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information)」を参照してください。|
|**電話によるサインインに複数の職場または学校アカウントを使用できないのはなぜですか。**| 携帯電話は、1 つの職場または学校アカウントに登録する必要があります。 電話によるサインインを別の職場または学校アカウントで有効にするには、 **[設定]** ページから古いデバイスの登録を解除する必要があります。|
|**携帯電話を使用して自分のコンピューターにサインインできますか。**| コンピューターには、Windows 10 で Windows Hello を使用してサインインすることをお勧めします。 Windows Hello を使用すると、顔、指紋、または暗証番号を使用してサインインできます。|

## <a name="next-steps"></a>次の手順

- 個人用 Microsoft アカウントの確認コードの取得に問題がある場合は、「[Microsoft アカウントのセキュリティ情報と確認コード](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes)」の記事の「**確認コードの問題のトラブルシューティング**」セクションを参照してください。

- アプリに関する一般的な質問については、「[Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md)」をご覧ください

- 2 段階認証について詳しくは、「[アカウントへの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)」をご覧ください

- セキュリティ情報の詳細については、「[セキュリティ情報 (プレビュー) の概要](user-help-security-info-overview.md)」を参照してください
