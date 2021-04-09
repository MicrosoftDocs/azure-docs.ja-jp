---
title: Azure AD B2C ローカル アカウント ID プロバイダーを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C テナントでサインアップまたはサインインに使用できる ID の種類 (メール アドレス、ユーザー名、電話番号) を定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555250"
---
# <a name="set-up-the-local-account-identity-provider"></a>ローカル アカウント ID プロバイダーを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C には、ユーザーの認証に使用できるさまざまな方法が用意されています。 ユーザーは、ユーザー名とパスワード、電話による確認 (別名: パスワードレス認証)、またはソーシャル ID プロバイダーを使用して、ローカル アカウントにサインインできます。 ローカル アカウント ID プロバイダーの設定では、メール アドレスでのサインアップが既定で有効になっています。 

この記事では、ユーザーがこの Azure AD B2C テナントのローカルのアカウントを作成する方法について説明します。 ユーザーの ID が Facebook、Google などのフェデレーション ID プロバイダーによって管理されるソーシャル ID またはエンタープライズ ID の場合は、「[ID プロバイダーの追加](add-identity-provider.md)」を参照してください。

## <a name="email-sign-in"></a>メール アドレスでのサインイン

メール アドレスオプションを使用すると、ユーザーはメール アドレスとパスワードを使用してサインインまたはサインアップできます。

- **サインイン**: ユーザーはメール アドレスとパスワードを入力するように求められます。
- **サインアップ**: ユーザーはメール アドレスを入力するように求められます。このアドレスがサインアップ時に検証され (省略可能)、ログイン ID になります。 次にユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 次に、[続行] を選択してアカウントを作成します。
- **パスワードのリセット**: ユーザーは自身のメール アドレスを入力し、確認する必要があります。その後、パスワードをリセットできます。

![メール アドレスでのサインアップまたはサインインの操作](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>ユーザー名でのサインイン

ユーザー オプションを使用すると、ユーザーはユーザー名とパスワードを使用してサインインまたはサインアップできます。

- **サインイン**: ユーザーは、ユーザー名とパスワードを入力するように求められます。
- **サインアップ**: ユーザーはユーザー名を入力するように求められます。これがログイン ID になります。 ユーザーはメール アドレスの入力も求められます。このアドレスはサインアップ時に検証されます。 このメール アドレスは、パスワード リセット フローで使用されます。 ユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 次にユーザーは、[続行] を選択してアカウントを作成します。
- **パスワードのリセット**: ユーザーは、ユーザー名と、関連付けられたメール アドレスを入力する必要があります。 このメール アドレスを確認する必要があります。その後、ユーザーはパスワードをリセットできます。

![ユーザー名でのサインアップまたはサインインの操作](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>電話でのサインイン (プレビュー)

パスワードレス認証は、認証の一種であり、ユーザーはパスワードを使用してサインインする必要がありません。 電話でのサインアップとサインインを使用すると、ユーザーはプライマリ ログイン ID として電話番号を使用してアプリにサインアップできます。 ユーザーは、サインアップとサインインの際に次の操作を行います。

- **サインイン**: 自身の電話番号を ID として使用する既存のアカウントを持っている場合、ユーザーはその電話番号を入力し、 *[サインイン]* を選択します。 *[続行]* を選択して国と電話番号を確定すると、1 回限りの認証コードがそのユーザーの電話に送信されます。 ユーザーが確認コードを入力し、 *[続行]* を選択してサインインします。
- **サインアップ**: アプリケーションのアカウントをまだ持っていない場合、ユーザーは *[今すぐサインアップ]* リンクをクリックしてアカウントを作成できます。 
    1. サインアップ ページが表示されます。ここでユーザーは *[国]* を選択し、電話番号を入力して、 *[コードの送信]* を選択します。 
    1. 1 回限りの確認コードがユーザーの電話番号に送信されます。 ユーザーは、サインアップ ページで *確認コード* を入力し、 *[コードの確認]* を選択します (ユーザーがコードを取得できない場合、 *[新しいコードを送信します]* を選択できます)。 
    1. ユーザーは、サインアップ ページで要求されたその他の情報 (表示名、名、姓など) を入力します。 その後 [続行] を選択します。
    1. 次に、ユーザーは **回復用メール アドレス** を入力するように求められます。 ユーザーはメール アドレスを入力し、 *[確認コードを送信する]* を選択します。 ユーザーはメールの受信トレイに送信されたコードを取得して、 [確認コード] ボックスに入力できます。 次に、ユーザーは [コードの確認] を選択します。
    1. コードが確認されたら、ユーザーは *[作成]* を選択してアカウントを作成します。 

![電話でのサインアップまたはサインインの操作](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>価格

ワンタイム パスワードが SMS テキスト メッセージを使用してユーザーに送信されます。 携帯電話会社によっては、送信されるメッセージごとに課金される場合があります。 価格情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」の「**別料金**」セクションを参照してください。

> [!NOTE]
> 電話でのサインアップを使用したユーザー フローを構成するときは、多要素認証 (MFA) が既定で無効化されます。 電話でのサインアップを使用したユーザー フローで MFA を有効にすることはできますが、電話番号がプライマリ ID として使用されるため、2 番目の認証要素に使用できる唯一のオプションは電子メールによるワンタイム パスコードです。

### <a name="phone-recovery"></a>電話での回復

ユーザー フローで電話でのサインアップとサインインを有効にする場合、回復用メール機能を有効にすることもお勧めします。 この機能を使用すると、ユーザーが自分の電話を持っていないときにアカウントを回復するために使用できるメール アドレスを指定できます。 このメール アドレスは、アカウントの回復のみに使用されます。 サインインに使用することはできません。

- 回復用メール アドレスのプロンプトが **オン** の場合、ユーザーが初めてサインアップするときに、バックアップのメール アドレスを確認するように求められます。 前に回復用メールを提供していないユーザーは、次回のサインイン時にバックアップのメール アドレスの確認を求められます。

- 回復用メールが **オフ** の場合、ユーザーがサインアップまたはサインインしても、回復用メール プロンプトは表示されません。
 
次のスクリーンショットは、電話での回復フローを示しています。

![電話での回復のユーザー フロー](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>電話またはメール アドレスでのサインイン (プレビュー)

[電話でのサインイン](#phone-sign-in-preview)と[メール アドレスでのサインイン](#email-sign-in)を組み合わせることを選択できます。 サインアップまたはサインインのページで、ユーザーは電話番号またはメール アドレスを入力できます。 ユーザー入力に基づいて、対応するフローにユーザーが移動されます。 

![電話またはメール アドレスでのサインアップまたはサインインの操作](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>ローカル アカウント ID プロバイダーの設定を構成する

プロバイダー (メール アドレス、ユーザー名、または電話番号) を有効または無効にすることで、ユーザー フロー内で使用できるローカル ID プロバイダーを構成できます。  テナント レベルでは、複数のローカル ID プロバイダーを有効にできます。

1 つのユーザー フローでは、一度に 1 つのローカル アカウント ID プロバイダーの使用のみを構成できます。 テナント レベルで複数のユーザー フローが有効になっている場合は、各ユーザー フローで異なるローカル アカウント ID プロバイダーを設定できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** で、 **[ID プロバイダー]** を選択します。
1. ID プロバイダーの一覧で、 **[ローカル アカウント]** を選択します。
1. **[Configure local IDP]\(ローカル IDP を構成します\)** ページで、コンシューマーが Azure AD B2C テナントにローカル アカウントを作成するために使用できる、許可される ID の種類を少なくとも 1 つ選択します。
1. **[保存]** を選択します。

## <a name="configure-your-user-flow"></a>ユーザー フローを構成する

1. Azure portal の左側のメニューで、 **[Azure AD B2C]** を選択します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します。
1. サインアップとサインインの操作を構成するユーザー フローを選択します。
1. **[ID プロバイダー]** を選択します。
1. **[ローカル アカウント]** で、次のいずれかを選択します。 **[電子メールのサインアップ]** 、 **[User ID signup]\(ユーザー ID でのサインアップ\)** 、 **[Phone signup]\(電話でのサインアップ\)** 、 **[Phone/Email signup]\(電話/電子メールでのサインアップ\)** 、または **[なし]** 。

### <a name="enable-the-recovery-email-prompt"></a>回復用メール アドレスのプロンプトを有効にする

**[Phone signup]\(電話でのサインアップ\)** 、 **[Phone/Email signup]\(電話/電子メールでのサインアップ\)** オプションを選択した場合、回復用メール アドレスのプロンプトを有効にします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. Azure AD B2C の **[ポリシー]** で、 **[ユーザー フロー]** を選択します。
1. 一覧からユーザー フローを選択します。
1. **[設定]** で **[プロパティ]** を選択します。
1. **[Enable recovery email prompt for phone number signup and sign in (preview)]\(電話番号でのサインアップとサインインの回復用メール プロンプトを有効にする (プレビュー)\)** の横で、次のように選択します。
   - **[オン]** : サインアップとサインインの両方で回復用メール プロンプトを表示する。
   - **[オフ]** : 回復用メール プロンプトを非表示にする。
1. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>スターター パックを取得する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 事前に作成されたポリシーがいくつか含まれるスターター パックが用意されています。 関連するスターター パックをダウンロードします。 

- [メール アドレスでのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [ユーザー名でのサインイン](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [電話でのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 証明書利用者ポリシーを選択します。 
- [電話またはメール アドレスでのサインイン](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 証明書利用者ポリシーを選択します。

スターター パックをダウンロードした後:

1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。

1. 「[Azure Active Directory B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」の「[カスタム ポリシーにアプリケーション ID を追加します](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)」の手順を完了します。 たとえば、前提条件を満たす際に登録した 2 つのアプリケーション (*IdentityExperienceFramework* と *ProxyIdentityExperienceFramework*) の **アプリケーション (クライアント) ID** を使用して、`/phone-number-passwordless/`**`Phone_Email_Base.xml`** を更新します。
1. ポリシー ファイルをアップロードする

::: zone-end

## <a name="next-steps"></a>次のステップ

- [外部 ID プロバイダーを追加する](add-identity-provider.md)
- [ユーザー フローを作成する](tutorial-create-user-flows.md)
