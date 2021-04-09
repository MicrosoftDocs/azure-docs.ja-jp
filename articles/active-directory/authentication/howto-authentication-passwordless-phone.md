---
title: Microsoft Authenticator アプリを使用したパスワードなしのサインイン - Azure Active Directory
description: Microsoft Authenticator アプリを使用した Azure AD へのパスワードなしのサインインを有効にする
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e6cd7efcd0e851c15975aba5ff9b99c615eb7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653475"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリでパスワードなしのサインインを有効にする 

Microsoft Authenticator アプリを使用すると、パスワードを使用せずに Azure AD アカウントにサインインできます。 Microsoft Authenticator では、キーベースの認証を使用して、デバイスに関連付けられているユーザー資格情報を有効にします。なお、このデバイスでは PIN または生体認証が使用されます。 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) でも、同様のテクノロジが使用されます。

この認証テクノロジは、モバイルを含む任意のデバイス プラットフォームで使用できます。 このテクノロジはまた、Microsoft 認証ライブラリに統合されている任意のアプリまたは Web サイトで使用することもできます。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="サインインを承認するようユーザーに求めている、ブラウザーでのサインインの例":::

Microsoft Authenticator アプリから電話によるサインインを有効にしたユーザーには、自分のアプリで番号をタップするように求めるメッセージが表示されます。 ユーザー名またはパスワードの入力は求められません。 アプリでサインイン プロセスを完了するには、ユーザーはさらに次の操作を行う必要があります。

1. 番号を一致させます。
2. **[承認]** を選択します。
3. 自分の PIN または生体認証を指定します。

## <a name="prerequisites"></a>前提条件

Microsoft Authenticator アプリでパスワードなしの電話によるサインインを使用するには、次の前提条件を満たしている必要があります。

- 検証方法としてプッシュ通知が許可されている Azure AD Multi-Factor Authentication。
- iOS 8.0 以降、または Android 6.0 以降を実行しているデバイスにインストールされている最新バージョンの Microsoft Authenticator。

> [!NOTE]
> Azure AD PowerShell を使用して Microsoft Authenticator のパスワードなしのサインインを有効にした場合は、それがディレクトリ全体に対して有効になっています。 この新しい方法の使用を有効にすると、それが PowerShell ポリシーよりも優先されます。 新しい "*認証方法*" メニューを使用して、テナント内のすべてのユーザーに対して有効にすることをお勧めします。そうしないと、新しいポリシーに含まれていないユーザーは、パスワードなしでサインインできなくなります。

## <a name="enable-passwordless-authentication-methods"></a>パスワードなしの認証方法を有効にする

Azure AD でパスワードレス認証を使用するには、まず、統合された登録エクスペリエンスを有効にしてから、パスワードなしの方法に対してユーザーを有効にします。

### <a name="enable-the-combined-registration-experience"></a>統合された登録エクスペリエンスを有効にする

パスワードなしの認証方法の登録機能は、統合された登録機能に依存しています。 ユーザー自身が統合された登録を完了できるようにするには、[統合されたセキュリティ情報の登録を有効にする](howto-registration-mfa-sspr-combined.md)手順に従ってください。

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>パスワードなしの電話によるサインインの認証方法を有効にする

Azure AD では、サインイン プロセス中に使用できる認証方法を選択できます。 その後、ユーザーは使用する方法を登録します。

パスワードなしの電話によるサインインの認証方法を有効にするには、次の手順を実行します。

1. "*全体管理者*" アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. *Azure Active Directory* を検索して選択し、 **[セキュリティ]**  >  **[認証方法]**  >  **[ポリシー]** の順に移動します。
1. **[Microsoft Authenticator]** で、次のオプションを選択します。
   1. **有効にする** - [はい] または [いいえ]
   1. **ターゲット** - [すべてのユーザー] または [ユーザーの選択]
1. 追加された各グループまたはユーザーは既定で、パスワードレスとプッシュ通知の両方のモード ("任意" モード) で Microsoft Authenticator が使用できるようになっています。 これを変更するには、各行に対して次のことを行います。
   1. **[...]**  >  **[構成]** の順に移動します。
   1. **認証モード** - [任意]、[パスワードレス]、または [プッシュ]
1. 新しいポリシーを適用するには、 **[保存]** を選択します。

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Microsoft Authenticator のユーザー登録と管理

Azure AD のパスワードなし認証方法にユーザーが自分自身を登録するには、次の手順を使用します。

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) にアクセスします。
1. サインインし、次に **[方法の追加] > [認証アプリ]** を選択してから、 **[追加]** を選択し、Authenticator アプリを追加します。
1. 指示に従って、お使いのデバイスに Microsoft Authenticator アプリをインストールして構成します。
1. **[完了]** を選択して、Authenticator の構成を完了します。
1. **Microsoft Authenticator** で、登録したアカウントのドロップダウン メニューから **[電話によるサインインを有効にする]** を選択します。
1. アプリの指示に従って、パスワードなしの電話によるサインインに対するアカウントの登録を完了します。

組織はユーザーに対して、パスワードを使用しないで、自分の電話でサインインするように指示することができます。 Microsoft Authenticator アプリの構成と電話によるサインインの有効化の詳細については、「[Microsoft Authenticator アプリを使用してアカウントにサインインする](../user-help/user-help-auth-app-sign-in.md)」を参照してください。

> [!NOTE]
> ポリシーによって電話によるサインインを使用することが許可されていないユーザーは、Microsoft Authenticator アプリ内でそれを有効にすることができなくなります。

## <a name="sign-in-with-passwordless-credential"></a>パスワードなしの資格情報でサインインする

ユーザーは、以下の操作がすべて完了したら、パスワードなしサインインの利用を開始できます。

- 管理者がユーザーのテナントを有効にしました。
- 電話によるサインインを有効にするように、ユーザーが自分の Microsoft Authenticator アプリを更新しました。

ユーザーが電話によるサインイン プロセスを初めて開始するときには、ユーザーは次の手順を行います。

1. サインイン ページに自分の名前を入力します。
2. **[次へ]** を選択します。
3. 必要に応じて、 **[その他のサインイン方法]** を選択します。
4. **[Microsoft Authenticator アプリで要求を承認する]** を選択します。

すると、ユーザーに番号が表示されます。 パスワードを入力するのではなく、適切な番号を選択することで認証を受けるようにユーザーはアプリから求められます。

ユーザーがパスワードなしの電話によるサインインを利用すると、アプリではその後も引き続きユーザーにこの方法が案内されます。 ただし、ユーザーには別の方法を選択するためのオプションが表示されます。

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Microsoft Authenticator アプリを使用したブラウザーのサインインの例":::

## <a name="known-issues"></a>既知の問題

次の既知の問題点があります。

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>パスワードなしの電話によるサインインのオプションが表示されない

あるシナリオでは、ユーザーのパスワードなしのサインインの確認が未回答で、保留中である可能性があります。 しかし、ユーザーはもう一度サインインを試みることができます。 この場合、ユーザーにはパスワードを入力するオプションのみが表示されると考えられます。

このシナリオを解決するには、次の手順を使用します。

1. Microsoft Authenticator アプリを開きます。
2. 通知プロンプトに応答します。

これで、ユーザーはパスワードなしの電話によるサインインを引き続き利用できるようになります。

### <a name="federated-accounts"></a>フェデレーション アカウント

ユーザーがパスワードなしの資格情報を有効にした場合、Azure AD ログイン プロセスで login\_hint の使用が停止されます。 そのため、このプロセスによって、ユーザーがフェデレーション ログインの場所に向かって加速されることはなくなります。

このロジックにより、通常は、ハイブリッド テナント内のユーザーが、サインインの確認のための Active Directory フェデレーション サービス (AD FS) にリダイレクトされることはありません。 ただし、ユーザーには **[代わりにパスワードを使用する]** をクリックするという選択肢が残されています。

### <a name="azure-mfa-server"></a>Azure MFA サーバー

オンプレミスの Azure MFA server を介して、エンド ユーザーが Multi-Factor Authentication (MFA) を利用できるようにすることができます。 そのユーザーは引き続き、パスワードなしの電話によるサインイン資格情報を作成して利用することができます。

ユーザーが、パスワードなしの電話によるサインイン資格情報で Microsoft Authenticator アプリの複数のインストール (5 つ以上) をアップグレードしようとすると、この変更はエラーになることがあります。

### <a name="device-registration"></a>デバイス登録

この新しい強力な資格情報を作成するには、前提条件を満たす必要があります。 前提条件の 1 つとして、Microsoft Authenticator アプリがインストールされているデバイスを、Azure AD テナント内で個々のユーザーに登録する必要があります。

現在、デバイスは 1 つのテナントにのみ登録できます。 この制限は、電話によるサインインを有効にできるのは、Microsoft Authenticator アプリの職場または学校の 1 つのアカウントのみであることを意味します。

> [!NOTE]
> デバイスの登録は、デバイスの管理またはモバイル デバイス管理 (MDM) と同じではありません。 デバイスの登録の場合、Azure AD ディレクトリでデバイス ID とユーザー ID の両方が関連付けられるだけです。

## <a name="next-steps"></a>次のステップ

Azure AD 認証とパスワードレスの方法については、次の記事を参照してください。

- [パスワードレス認証のしくみを学習する](concept-authentication-passwordless.md)
- [デバイス登録の詳細](../devices/overview.md#getting-devices-in-azure-ad)
- [Azure AD Multi-Factor Authentication の詳細](../authentication/howto-mfa-getstarted.md)
