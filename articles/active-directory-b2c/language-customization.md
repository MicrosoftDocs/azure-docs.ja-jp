---
title: Azure Active Directory B2C での言語のカスタマイズ
description: Azure Active Directory B2C でのユーザー フローの言語のエクスペリエンスのカスタマイズについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686412"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C での言語のカスタマイズ

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) の言語のカスタマイズを使うと、顧客のニーズに応じて、ユーザー フローをさまざまな言語に対応させることができます。 Microsoft では [36 の言語](#supported-languages)の翻訳が提供されていますが、どの言語についてもユーザーが自分で翻訳を提供できます。 1 つの言語でのみページが提供されている場合でも、ページ上の任意のテキストをカスタマイズできます。

## <a name="how-language-customization-works"></a>言語のカスタマイズのしくみ

ユーザー フローで利用できる言語を選ぶには、言語のカスタマイズを使います。 この機能を有効にした後、アプリケーションからクエリ文字列パラメーター `ui_locales` を指定できます。 Azure AD B2C を呼び出すと、ページが指定したロケールに翻訳されます。 この種類の構成では、ユーザー フローの言語を完全に制御し、顧客のブラウザーの言語設定を無視できます。

顧客に表示される言語をそこまで制御する必要がない場合もあります。 `ui_locales` パラメーターを指定しない場合、顧客のエクスペリエンスはブラウザーの設定によって決まります。 その場合でも、ユーザー フローの翻訳先言語を、サポートされる言語として追加することで制御できます。 サポートしない言語を表示するように顧客のブラウザーが設定されている場合は、サポートされているカルチャで既定として選択した言語が代わりに表示されます。

* **ui-locales で指定した言語**:言語のカスタマイズを有効にすると、ユーザー フローはここで指定した言語に翻訳されます。
* **ブラウザーで要求されている言語**:`ui_locales` パラメーターを指定しないと、ユーザー フローはブラウザーで要求されている言語に翻訳されます ("*言語がサポートされている場合*")。
* **ポリシーの既定の言語**:ブラウザーで言語が指定されていない場合、またはサポートされていない言語が指定されている場合は、ユーザー フローはユーザー フローの既定の言語に翻訳されます。

> [!NOTE]
> カスタム ユーザー属性を使っている場合は、独自の翻訳を提供する必要があります。 詳しくは、「[文字列のカスタマイズ](#customize-your-strings)」をご覧ください。

::: zone pivot="b2c-custom-policy"

ローカライズには次の 3 つの手順が必要です。 

1. サポートされている言語の明示的な一覧を設定する
1. 言語固有の文字列とコレクションを指定する
1. ページの[コンテンツ定義](contentdefinitions.md)を編集する。 

::: zone-end 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>ui_locales で要求された言語のサポート

言語のカスタマイズの一般公開前に作成されたポリシーについては、最初に、この機能を有効にする必要があります。 一般公開後に作成されたポリシーとユーザー フローでは、言語のカスタマイズは既定で有効になっています。

ユーザー フローで言語のカスタマイズを有効にした後は、`ui_locales` パラメーターを追加することにより、ユーザー フローの言語を制御できます。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. 翻訳を有効にするユーザー フローをクリックします。
1. **[言語]** を選択します。
1. **[言語のカスタマイズを有効化]** を選びます。

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>ユーザー フローで有効にする言語を選択する

`ui_locales` パラメーターが指定されていないブラウザーで要求された場合に、ユーザー フローで言語セットが翻訳されるように、言語セットを有効にします。

1. 前の手順で、ユーザー フローの言語のカスタマイズを有効にしていることを確認します。
1. ユーザー フローの **[言語]** ページで、サポートする言語を選択します。
1. プロパティ ウィンドウで、 **[有効]** を **[はい]** に変更します。
1. プロパティ ウィンドウの上部にある **[保存]** を選びます。

>[!NOTE]
>`ui_locales` パラメーターが指定されていない場合、ページは顧客のブラウザーの言語に翻訳されます (有効になっている場合のみ)。
>

## <a name="customize-your-strings"></a>文字列のカスタマイズ

言語のカスタマイズを使うと、ユーザー フローの文字列をカスタマイズできます。

1. 前の手順で、ユーザー フローの言語のカスタマイズを有効にしていることを確認します。
1. ユーザー フローの **[言語]** ページで、カスタマイズする言語を選択します。
1. **[Page-level-resources files]\(ページ レベル リソース ファイル\)** で、編集するページを選択します。
1. **[既定値のダウンロード]** (この言語を前に編集した場合は **[オーバーライドのダウンロード]** ) を選びます。

上記の手順により、文字列の編集を開始する際に使用できる JSON ファイルを入手できます。

### <a name="change-any-string-on-the-page"></a>ページ上の文字列を変更する

1. 前の手順でダウンロードした JSON ファイルを JSON エディターで開きます。
1. 変更する要素を探します。 探している文字列の `StringId` を見つけるか、変更する `Value` 属性を見つけます。
1. `Value` 属性を表示する文字列に更新します。
1. 変更するすべての文字列について、`Override` を `true` に変更します。
1. ファイルを保存し、変更内容をアップロードします。 (アップロード コントロールは、JSON ファイルをダウンロードした場所にあります)

> [!IMPORTANT]
> 文字列をオーバーライドする必要がある場合は、必ず `Override` の値を `true` に設定してください。 値が変更されていない場合、そのエントリは無視されます。

### <a name="change-extension-attributes"></a>拡張属性を変更する

カスタム ユーザー属性の文字列を変更する場合や、JSON に追加する場合は、次の形式を使います。

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

`<ExtensionAttribute>` をカスタム ユーザー属性の名前に置き換えます。

`<ExtensionAttributeValue>` を表示する新しい文字列に置き換えます。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections を使って値の一覧を提供する

応答の値セットの一覧を指定する場合は、`LocalizedCollections` 属性を作成する必要があります。 `LocalizedCollections` は、`Name` と `Value` のペアの配列です。 項目の順序は、表示される順序になります。 `LocalizedCollections` を追加するには、次の形式を使います。

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` は、この `LocalizedCollections` 属性が応答になるユーザー属性です。
* `Name` は、ユーザーに表示される値です。
* `Value` は、このオプションが選択された場合に要求で返される値です。

### <a name="upload-your-changes"></a>変更のアップロード

1. JSON ファイルの変更が完了したら、B2C テナントに戻ります。
1. **[ユーザー フロー]** を選択し、翻訳を有効にするユーザー フローをクリックします。
1. **[言語]** を選択します。
1. 翻訳する言語を選びます。
1. 翻訳を提供するページを選びます。
1. フォルダー アイコンを選び、アップロードする JSON ファイルを選びます。

変更がユーザー フローに自動的に保存されます。

## <a name="customize-the-page-ui-by-using-language-customization"></a>言語のカスタマイズを使用してページの UI をカスタマイズする

[HTML コンテンツ](customize-ui-with-html.md)をローカライズする方法は 2 つあります。 方法の 1 つは、[言語のカスタマイズ](language-customization.md)を有効にすることです。 この機能を有効にすると、Azure AD B2C で OpenID Connect パラメーター `ui-locales` をエンドポイントに転送できるようになります。 コンテンツ サーバーは、このパラメーターを使用して、言語固有のカスタマイズされた HTML ページを提供できます。

使われているロケールに基づいて、さまざまな場所からコンテンツをプルすることもできます。 CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようにフォルダー構造を設定できます。 ワイルドカード値 `{Culture:RFC5646}` を使うと、適切な言語が呼び出されます。 たとえば、次のようなカスタム ページの URI があるものとします。

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

このページを `fr` で読み込むことができます。 ページは、HTML および CSS のコンテンツを取得とき、次の場所から取得します。

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>カスタム言語の追加

現在 Microsoft が翻訳を提供していない言語を追加することもできます。 ユーザー フロー内のすべての文字列に対して翻訳を提供する必要があります。 言語およびロケールのコードは、ISO 639-1 標準にものに限られます。 ロケール コードの形式は、"ISO_639-1_code"-"CountryCode" (例: `en-GB`) である必要があります。 ロケール ID の形式の詳細については、 https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a を参照してください。

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
2. カスタム言語を追加するユーザー フローをクリックし、 **[言語]** をクリックします。
3. ページの上部から **[カスタム言語の追加]** を選択します。
4. 表示されたコンテキスト ウィンドウで、有効なロケール コードを入力して、翻訳を提供する言語を指定します。
5. ページごとに英語の一連のオーバーライドをダウンロードし、翻訳作業を行うことができます。
6. 完了した JSON ファイルは、ページごとにアップロードできます。
7. **[有効]** を選択すると、ユーザー フローでユーザーに対してこの言語を表示できるようになります。
8. 言語を保存します。

>[!IMPORTANT]
>カスタム言語を有効にするか、保存する前のオーバーライドをアップロードする必要があります。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>サポートされる言語の一覧を設定する

お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. `Localization` 要素を、サポートされている言語である英語 (既定) とスペイン語を指定して追加します。  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>言語固有のラベルを指定する

`Localization` 要素の [LocalizedResources](localization.md#localizedresources) には、ローカライズされた文字列の一覧が含まれています。 ローカライズされたリソース要素には、ローカライズされたリソースを一意に識別するために使用される識別子があります。 この識別子は後で[コンテンツ定義](contentdefinitions.md)要素で使用されます。

ローカライズされたリソース要素は、コンテンツ定義およびサポートする任意の言語に対して設定します。 英語とスペイン語の統合されたサインアップまたはサインイン ページをカスタマイズするには、`</SupportedLanguages>` 要素の後に次の `LocalizedResources` 要素を追加します。

> [!NOTE]
> 次の例では、各行の先頭にシャープ記号 (`#`) を追加したため、ローカライズされたラベルを画面上で簡単に見つけることができます。

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>ローカライズを使用してコンテンツ定義を編集する

コピーした ContentDefinitions 要素の内容全体を BuildingBlocks 要素の子として貼り付けます。

次の例では、英語 (en) とスペイン語 (es) のカスタム文字列がサインアップまたはサインイン ページと、ローカル アカウント サインアップ ページに追加されています。 各 **LocalizedResourcesReference** の **LocalizedResourcesReferenceId** は、それらのロケールと同じですが、識別子として任意の文字列を使用できます。 言語とページの組み合わせごとに、以前に作成した対応する **LocalizedResources** をポイントします。

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>更新されたカスタム ポリシーをアップロードしてテストする

### <a name="upload-the-custom-policy"></a>カスタム ポリシーをアップロードする

1. 拡張ファイルを保存します。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. **Azure AD B2C** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。
1. 前に変更した拡張ファイルをアップロードします。

### <a name="test-the-custom-policy-by-using-run-now"></a>**[今すぐ実行]** を使用したカスタム ポリシーのテスト

1. アップロードしたポリシーを選択し、 **[今すぐ実行]** を選択します。
1. ローカライズされたサインアップまたはサインイン ページが表示されます。
1. サインアップ リンクをクリックすると、ローカライズされたサインアップ ページが表示されます。
1. ブラウザーの既定の言語をスペイン語に切り替えます。 または、クエリ文字列パラメーターの `ui_locales` を承認要求に追加することもできます。 次に例を示します。 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>関連情報

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>オーバーライドとしてのページ UI カスタマイズ ラベル

言語のカスタマイズを有効にすると、ページ UI のカスタマイズを使った、ラベルの以前の編集内容は、英語 (en) の JSON ファイルに保存されます。 引き続き、言語のカスタマイズで言語リソースをアップロードして、ラベルなどの文字列を変更できます。

::: zone-end

### <a name="up-to-date-translations"></a>翻訳を更新する

Microsoft は、最新の翻訳を提供するよう努めています。 継続的に翻訳を改善し、コンプライアンスを維持します。 バグやグローバルな用語の変更を特定し、ユーザー フローにシームレスに適用される更新を行います。

### <a name="support-for-right-to-left-languages"></a>右から左へ記述する言語のサポート

現在、右から左に書く言語はサポートしていません。 これには、カスタム ロケールと CSS を使用して、文字列の表示方法を変更することで対応できます。 この機能が必要な場合は、[Azure フィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)で投票してください。

### <a name="social-identity-provider-translations"></a>ソーシャル ID プロバイダーの翻訳

Microsoft は、`ui_locales` OIDC パラメーターをソーシャル ログインに提供しています。 ただし、Facebook や Google などの一部のソーシャル ID プロバイダーは、それを利用していません。

### <a name="browser-behavior"></a>ブラウザーの動作

Chrome と Firefox はどちらも、それぞれに設定されている言語を要求します。 サポートされている言語の場合は、既定値の前に表示されます。 Microsoft Edge は現在、言語を要求せず、既定の言語が表示されます。

## <a name="supported-languages"></a>サポートされている言語

Azure AD B2C では、以下の言語がサポートされています。 ユーザー フローの言語は、Azure AD B2C によって提供されます。 Multi-Factor Authentication (MFA) 通知の言語は、[Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md) によって提供されます。

| Language              | 言語コード | ユーザー フロー         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| アラビア語                | ar            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ブルガリア語             | bg            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ベンガル語                | bn            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| カタロニア語               | ca            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| チェコ語                 | cs            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| デンマーク語                | da            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ドイツ語                | de            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ギリシャ語                 | el            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| 英語               | en            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| スペイン語               | es            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| エストニア語              | et            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| バスク語                | eu            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| フィンランド語               | fi            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| フランス語                | fr            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ガリシア語              | gl            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| グジャラート語              | gu            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| ヘブライ語                | he            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ヒンディー語                 | hi            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| クロアチア語              | hr            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ハンガリー語             | hu            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| インドネシア語            | id            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| イタリア語               | it            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| 日本語              | ja            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| カザフ語                | kk            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| カンナダ語               | kn            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| 韓国語                | ko            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| リトアニア語            | lt            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ラトビア語               | lv            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| マラヤーラム語             | ml            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| マラーティー語               | mr            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| マレー語                 | ms            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ノルウェー語 - ブークモール      | nb            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| オランダ語                 | nl            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ノルウェー語             | no            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| パンジャーブ語               | pa            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| ポーランド語                | pl            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ポルトガル語 - ブラジル   | pt-br         | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ポルトガル語 - ポルトガル | pt-pt         | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ルーマニア語              | ro            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ロシア語               | ru            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| スロバキア語                | sk            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| スロベニア語             | sl            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| セルビア語 - キリル    | sr-cryl-cs    | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| セルビア語 - ラテン       | sr-latn-cs    | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| スウェーデン語               | sv            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| タミル語                 | ta            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| テルグ語                | te            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) |
| タイ語                  | th            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| トルコ語               | tr            | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ウクライナ語             | uk            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| ベトナム語            | vi            | ![X はサポートされないことを示します。](./media/user-flow-language-customization/no.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| 中国語 - 簡体字  | zh-hans       | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |
| 中国語 - 繁体字 | zh-hant       | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) | ![緑色のチェック マーク。](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>次のステップ

::: zone pivot="b2c-user-flow"

[Azure Active Directory B2C でのアプリケーションのユーザー インターフェイスのカスタマイズ](customize-ui-with-html.md)に関する記事を参照して、アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細を確認します。

::: zone-end 

::: zone pivot="b2c-custom-policy"

- IEF リファレンスで[ローカリゼーション](localization.md)要素についてさらに詳しく学習する。
- Azure AD B2C で使用できる[ローカライズ文字列 ID](localization-string-ids.md) の一覧を参照する。

::: zone-end 
