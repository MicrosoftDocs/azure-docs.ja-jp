---
title: Microsoft Authenticator アプリを使用したパスワードなしのサインイン - Azure Active Directory
description: Microsoft Authenticator アプリを使用した Azure AD へのパスワードなしのサインインを有効にする (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a7bf9690b4f50c771afc4745bbc2e5377adbba
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848478"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Microsoft Authenticator アプリを使用したパスワードなしのサインインを有効にする (プレビュー)

Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) のテクノロジと同様、Microsoft Authenticator は、キーベースの認証を使用して、デバイスに関連付けられていて生体認証または PIN を使用するユーザー資格情報を有効にします。 この認証方法は、モバイルを含む任意のデバイス プラットフォームと、Microsoft 認証ライブラリと統合されているすべてのアプリまたは Web サイトで使用できます。 

![サインインを承認するようユーザーに求めている、ブラウザーでのサインインの例](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

ユーザー名の入力後にパスワードの入力を求めるプロンプトを表示するのではなく、Microsoft Authenticator アプリから電話によるサインインを有効にしているユーザーには、アプリで番号をタップするよう伝えるメッセージが表示されます。 このアプリでは、ユーザーは、番号を照合し、[Approve]\(承認) を選択してから、PIN を入力するか生体認証を行う必要があります。その後、認証が完了します。

> [!NOTE]
> この機能は 2017 年 3 月から Microsoft Authenticator アプリに含まれていたため、ディレクトリに対してこのポリシーを有効にすると、ユーザーはすぐにこのフローを経験し、ポリシーでユーザーが有効にされていない場合はエラー メッセージが表示される可能性があります。 この変更を認識して、ユーザーに用意をさせてください。

## <a name="prerequisites"></a>前提条件

- 認証方法としてプッシュ通知が許可されている Azure Multi-factor Authentication 
- iOS 8.0 以降、または Android 6.0 以降を実行しているデバイスにインストールされている最新バージョンの Microsoft Authenticator。

> [!NOTE]
> Azure AD PowerShell を使用して以前の Microsoft Authenticator アプリのパスワードなしサインイン プレビューを有効にした場合は、ディレクトリ全体に対して有効になります。 この新しい方法の使用を有効にすると、PowerShell ポリシーが上書きされます。 新しい認証方法を使用して、テナント内のすべてのユーザーに対してを有効にすることをお勧めします。そうしないと、新しいポリシーに含まれていないユーザーは、パスワードなしでログインできなくなります。 

## <a name="enable-passwordless-authentication-methods"></a>パスワードなしの認証方法を有効にする

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

パスワードなしの認証方法の登録機能は、統合された登録のプレビューに依存しています。 「[統合されたセキュリティ情報の登録の有効化 (プレビュー)](howto-registration-mfa-sspr-combined.md)」の記事に記載されている手順に従って、統合された登録のプレビューを有効にします。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>パスワードなしの電話によるサインインの認証方法を有効にする

1. [Azure ポータル](https://portal.azure.com)
1. *Azure Active Directory* を検索して選択します。 **[セキュリティ]**  >  **[認証方法]**  >  **[認証方法ポリシー (プレビュー)]** の順に選択します。
1. **[Passwordless phone sign-in]\(パスワードなしの電話によるサインイン\)** で、次のオプションを選択します。
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. **[保存]** を選択して新しいポリシーを設定します

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator アプリのユーザー登録と管理

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) に移動します。
1. サインインします (まだサインインしていない場合)
1. **[方法の追加]** をクリックし、 **[認証アプリ]** を選択し、 **[追加]** をクリックして、認証アプリを追加します。
1. 指示に従って、デバイスに Microsoft Authenticator アプリをインストールして構成します。
1. **[完了]** をクリックし、Authenticator MFA アプリの設定フローを完了します。 
1. **Microsoft Authenticator** で、アカウントのドロップダウン メニューから **[電話によるサインインを有効にする]** を選択します。
1. アプリの指示に従って、パスワードなしの電話によるサインインの登録を完了します。 

組織は、Microsoft Authenticator アプリでの設定と電話によるサインインの有効化をさらに支援するために、[パスワードではなく電話でサインインする方法](../user-help/microsoft-authenticator-app-phone-signin-faq.md)に関する記事をユーザーに紹介することができます。

## <a name="sign-in-with-passwordless-credential"></a>パスワードなしの資格情報でサインインする

パブリック プレビューでは、ユーザーにこの新しい資格情報の作成または使用を適用する方法はありません。 ユーザーには、管理者がテナントを有効にしていて、**さらに**ユーザーが電話によるサインインを有効にするように Microsoft Authenticator アプリを更新した後でのみ、パスワードなしのサインインが発生します。

Web にユーザー名を入力し、 **[次へ]** を選択すると、ユーザーには数字が表示され、Microsoft Authenticator アプリでパスワードを使用する代わりに認証できる適切な番号を選択するように求められます。 

![Microsoft Authenticator アプリを使用したブラウザーのサインインの例](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>既知の問題

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>ユーザーはポリシーで有効にされていないが、Microsoft Authenticator でのパスワードなしの電話によるサインイン方法がある

ユーザーは、ある時点で、現在の Microsoft Authenticator アプリまたはそれより前のデバイスでパスワードなしの電話によるサインイン資格情報を作成した可能性があります。 パスワードなしの電話によるサインインの認証方法ポリシーを管理者が有効にすると、資格情報が登録されているすべてのユーザーは、ポリシーの使用が有効かどうかに関係なく、新しいサインイン プロンプトの表示が開始されます。 ポリシーによってユーザーが資格情報の使用を許可されていない場合は、認証フローの完了後にエラーが表示されます。 

管理者は、ユーザーがパスワードなしの電話によるサインインを使用することを許可することができます。または、ユーザーがこの方法を削除する必要があります。 ユーザーが登録済みのデバイスを持っていない場合は、[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) にアクセスし、削除することができます。 まだ Authenticator for MFA を使用している場合は、Microsoft Authenticator 内から **[電話によるサインインを無効にする]** を選択できます。  

### <a name="ad-fs-integration"></a>AD FS の統合

ユーザーが Microsoft Authenticator のパスワードなしの資格情報を有効にすると、そのユーザーの認証は常に、既定で承認のための通知を送信します。 このロジックは、ハイブリッド テナントのユーザーが、[Use your password instead] (代わりにパスワードを使用する) をクリックする追加の手順を取ることなく、サインイン確認のために ADFS にリダイレクトされることを防ぎます。 このプロセスは、オンプレミスの条件付きアクセス ポリシーとパススルー認証フローもバイパスします。 

ユーザーがパスワードなしの電話によるサインインの確認に回答せずに保留していて、もう一度サインインを試行した場合は、代わりにパスワードを入力するようにユーザーに ADFS を表示することができます。  

### <a name="azure-mfa-server"></a>Azure MFA サーバー

組織のオンプレミスの Azure MFA サーバーを介して MFA に対して有効にされたエンド ユーザーは、単一のパスワードなしの電話によるサインイン資格情報を引き続き作成し、使用することができます。 ユーザーが資格情報で Microsoft Authenticator の複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。  

### <a name="device-registration"></a>デバイス登録

この新しい、強力な資格情報を作成するための前提条件の 1 つは、Microsoft Authenticator アプリのインストール先となるデバイスが、個々のユーザーに対し、Azure AD テナント内にも登録されていることです。 現在のデバイス登録の制限により、デバイスは、1 つのテナントにしか登録できません。 この制限は、電話によるサインインを有効にできるのは、Microsoft Authenticator アプリの職場または学校の 1 つのアカウントのみであることを意味します。

> [!NOTE]
> デバイスの登録は、デバイスの管理、つまり "MDM" と同じではありません。 この場合、Azure AD ディレクトリでデバイス ID とユーザー ID の両方が関連付けられるだけです。  

## <a name="next-steps"></a>次の手順

[パスワードなしとは](concept-authentication-passwordless.md)

[デバイス登録の詳細](../devices/overview.md#getting-devices-in-azure-ad)

[Azure Multi-Factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
