---
title: ローカライズ文字列 ID - Azure Active Directory B2C |Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーに api.signuporsignin の ID を持つコンテンツ定義の ID を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 611d676f5f588ff32f981692456160e269642a43
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428128"
---
# <a name="localization-string-ids"></a>ローカライズ文字列 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ローカリゼーション**要素を使用すると、ユーザー体験に関するポリシーで複数のロケールまたは言語をサポートできます。 この記事では、ポリシーで使用できるローカリゼーション ID の一覧を示します。 UI のローカリゼーションに精通するには、[ローカリゼーション](localization.md)を参照してください。

## <a name="sign-up-or-sign-in-page-elements"></a>サインアップ ページまたはサインイン ページの要素

次の ID は、`api.signuporsignin` の ID を持つコンテンツ定義に使用されます。

| id | 既定値 |
| -- | ------------- |
| **local_intro_email** | 自分の既存のアカウントでサインイン |
| **logonIdentifier_email** | 電子メール アドレス |
| **requiredField_email** | メール アドレスを入力してください |
| **invalid_email** | 有効な電子メール アドレスを入力してください |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&'' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | 自分のユーザー名でサインイン |
| **logonIdentifier_username** | ユーザー名 |
| **requiredField_username** | ユーザー名を入力してください |
| **password** | Password |
| **requiredField_password** | パスワードを入力してください |
| **invalid_password** | 入力したパスワードは、予期された形式ではありません。 |
| **forgotpassword_link** | パスワードを忘れた場合 |
| **createaccount_intro** | アカウントをお持ちでない場合は、 |
| **createaccount_link** | 今すぐサインアップ |
| **divider_title** | OR |
| **cancel_message** | ユーザーがパスワードを忘れました |
| **button_signin** | サインイン |
| **social_intro** | 自分のソーシャル アカウントでサインイン |
  **remember_me** |サインインしたままにする|
| **unknown_error** | サインインで問題が発生しました。 後でもう一度やり直してください。 |

次の例は、サインアップまたはサインインページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![サインアップまたはサインイン ページの UX 要素](./media/localization-string-ids/localization-susi.png)

ID プロバイダーの ID は、ユーザー体験  **ClaimsExchange** 要素で構成されます。 ID プロバイダーのタイトルをローカライズするには、 **ElementType** は`ClaimsProvider`に設定され、**StringId** は`ClaimsExchange`の ID に設定されます。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

次の例では、Facebook の ID プロバイダーをアラビア語にローカライズしています。

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>サインアップまたはサインインのエラー メッセージ

| id | 既定値 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | パスワードが正しくありません。 |
| **UserMessageIfClaimsPrincipalDoesNotExist** | ご利用のアカウントが見つからないようです。 |
| **UserMessageIfOldPasswordUsed** | 古いパスワードが使用された可能性があります。 |
| **DefaultMessage** | ユーザー名またはパスワードが無効です。 |
| **UserMessageIfUserAccountDisabled** | ご使用のアカウントはロックされています。 サポート担当者に連絡してロックを解除してから、もう一度お試しください。 |
| **UserMessageIfUserAccountLocked** | ご使用のアカウントは、不正使用を防ぐために一時的にロックされています。 後で再試行してください。 |
| **AADRequestsThrottled** | この時点でリクエストが多すぎます。 しばらく待って、もう一度やり直してください。 |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>サインアップとセルフアサート ページのユーザー インターフェイス要素

以下は、`api.localaccountsignup`の ID を持つコンテンツ定義、または`api.selfasserted.profileupdate`や`api.localaccountpasswordreset`など、`api.selfasserted`で始まるコンテンツ定義の ID です。

| id | 既定値 |
| -- | ------------- |
| **ver_sent** | 次の場所に確認コードが送信されました: |
| **ver_but_default** | Default |
| **cancel_message** | セルフ アサート情報の入力がユーザーによって取り消されました |
| **preloader_alt** | お待ちください |
| **ver_but_send** | 確認コードを送信する |
| **alert_yes** | はい |
| **error_fieldIncorrect** | 1 つ以上のフィールドが正しく入力されていません。 入力を確認してから、やり直してください。 |
| **year** | 年 |
| **verifying_blurb** | 情報の処理が終わるまでお待ちください。 |
| **button_cancel** | Cancel |
| **ver_fail_no_retry** | 多くの正しくない試行を行いました。 後でもう一度やり直してください。 |
| **month** | Month |
| **ver_success_msg** | 電子メールアドレスが確認されました。 続行することができるようになりました。 |
| **months** | 1 月、2 月、3 月、4 月、5 月、6 月、7 月、8 月、9 月、10 月、11 月、12 月 |
| **ver_fail_server** | ご使用のメールアドレスの確認に問題があります。 有効なメール アドレスを入力して、もう一度やり直してください。 |
| **error_requiredFieldMissing** | 必要なフィールドがありません。 すべての必須フィールドに入力してから、やり直してください。 |
| **initial_intro** | 以下の詳細を指定してください。 |
| **ver_but_resend** | 新しいコードを送信します |
| **button_continue** | 作成 |
| **error_passwordEntryMismatch** | パスワードの入力フィールドが一致しません。 両方のフィールドに同じパスワードを入力して、もう一度やり直してください。 |
| **ver_incorrect_format** | 形式が正しくありません |
| **ver_but_edit** | 電子メールを変更します |
| **ver_but_verify** | コードの確認 |
| **alert_no** | いいえ |
| **ver_info_msg** | 確認コードが受信トレイに送信されました。 次の入力ボックスにコピーしてください。 |
| **day** | 日 |
| **ver_fail_throttled** | このメール アドレスを確認するリクエストが多すぎます。 しばらく待って、再試行してください。 |
| **helplink_text** | これは何ですか。 |
| **ver_fail_retry** | そのコードが正しくありません。 再試行してください。 |
| **alert_title** | 詳細情報の入力をキャンセルします。 |
| **required_field** | この情報は必須です。 |
| **alert_message** | 詳細情報の入力をキャンセルしますか。 |
| **ver_intro_msg** | 確認が必要です。 [送信] ボタンをクリックしてください。 |
| **ver_input** | 確認コード |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>サインアップとセルフアサート ページ エラー メッセージ

| id | 既定値 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 指定した ID を持つユーザーは既に存在します。 別の ID を選択してください。 |
| **UserMessageIfClaimNotVerified** | 要求が確認されていません: {0} |
| **UserMessageIfIncorrectPattern** | {0} のパターンが正しくありません: |
| **UserMessageIfMissingRequiredElement** | 必要な要素が見つかりません: {0} |
| **UserMessageIfValidationError** | {0} による検証中にエラーが発生しました |
| **UserMessageIfInvalidInput** | {0}に無効な入力があります。 |
| **ServiceThrottled** | この時点でリクエストが多すぎます。 しばらく待って、もう一度やり直してください。 |

次の例では、サインアップページにユーザーインターフェイス要素の一部を使用しています。

![UI 要素名がラベル付けされたサインアップ ページ](./media/localization-string-ids/localization-sign-up.png)

次の例は、ユーザーが [認証コードを送信する] ボタンをクリックした後に、サインアップ ページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>電話ファクター認証ページのユーザー インタフェース要素

以下は、`api.phonefactor`の ID を持つコンテンツ定義用の ID です。

| id | 既定値 |
| -- | ------------- |
| **button_verify** | 電話する |
| **country_code_label** | 国番号 |
| **cancel_message** | ユーザーが多要素認証を取り消しました |
| **text_button_send_second_code** | 新しいコードを送信する |
| **code_pattern** | \\d{6} |
| **intro_mixed** | レコードに次の番号があります。 ユーザーを認証する電話または SMS を使用してコードを送信できます。 |
| **intro_mixed_p** | レコードに、次の番号があります。 ユーザーに電話できる番号を選択するか、認証するために SMS 経由でコードを送信します。 |
| **button_verify_code** | コードの確認 |
| **requiredField_code** | 受信した確認コードを入力してください |
| **invalid_code** | 受信した 6 桁のコードを入力してください |
| **button_cancel** | Cancel |
| **local_number_input_placeholder_text** | 電話番号 |
| **button_retry** | [再試行] |
| **alternative_text** | 自分の電話を持っていません |
| **intro_phone_p** | レコードに、次の番号があります。 電話で認証できる番号を選択してください。 |
| **intro_phone** | レコードに次の番号があります。 認証するためにお電話します。 |
| **enter_code_text_intro** | 以下に確認コードを入力するか、  |
| **intro_entry_phone** | 認証用に電話を受けることを希望する番号を以下に入力してください。 |
| **intro_entry_sms** | 認証用に SMS でコードを受け取ることを希望する番号を以下に入力してください。 |
| **button_send_code** | コードの送信 |
| **invalid_number** | 有効な電話番号を入力してください |
| **intro_sms** | レコードに次の番号があります。 認証するために SMS を介してコードを送信します。 |
| **intro_entry_mixed** | 認証用に SMS でコードを受け取る、または電話を受けることを希望する番号を以下に入力してください。 |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |レコードに、次の番号があります。 認証用に SMS でコードを受け取ることを希望する番号を以下に入力してください。 |
| **requiredField_countryCode** | 国コードを選択してください |
| **requiredField_number** | 電話番号を入力してください |
| **country_code_input_placeholder_text** |国または地域 |
| **number_label** | 電話番号 |
| **error_tryagain** | 指定した電話番号がビジー状態であるか、利用不可です。 番号を確認して、もう一度やり直してください。 |
| **error_incorrect_code** | 入力した確認コードは、弊社の記録と一致しません。 もう一度やり直すか、新しいコードを要求してください。 |
| **countryList** | 「[国の一覧](#countries-list)」を参照してください。 |
| **error_448** | 指定された電話番号につながりません。 |
| **error_449** | ユーザーが再試行回数を超えました。 |
| **verification_code_input_placeholder_text** | 確認コード |

次の例は、MFA 登録ページの一部のユーザー インターフェイス要素の使用方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-mfa1.png)

次の例は、MFA 検証ページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>検証表示コントロールのユーザー インターフェイス要素

[検証表示コントロール](display-control-verification.md)の ID を次に示します。

| id | 既定値 |
| -- | ------------- |
|verification_control_but_change_claims |Change |
|verification_control_fail_send_code |コードを送信できませんでした。しばらくしてから、もう一度お試しください。 |
|verification_control_fail_verify_code |コードを検証できませんでした。しばらくしてから、もう一度お試しください。 |
|verification_control_but_send_code |コードの送信 |
|verification_control_but_send_new_code |新しいコードを送信します |
|verification_control_but_verify_code |コードの確認 |
|verification_control_code_sent| 確認コードが送信されました。 次の入力ボックスにコピーしてください。 |

### <a name="example"></a>例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>RESTful サービスのエラー メッセージ

[RESTful サービスの技術プロファイル](restful-technical-profile.md)のエラー メッセージ用 ID を次に示します。

| id | 既定値 |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | RESTful サービス エンド ポイントへの接続を確立できませんでした。 RESTful サービスの URL: {0} |
|UserMessageIfCircuitOpen | {0}RESTful サービスの URL: {1} |
|UserMessageIfDnsResolutionFailed | RESTful サービス エンドポイントのホスト名を解決できませんでした。 RESTful サービスの URL: {0} |
|UserMessageIfRequestTimeout | タイムアウト制限の {0} 秒内に、RESTful サービス エンド ポイントへの接続を確立できませんでした。 RESTful サービスの URL: {1} |


### <a name="example"></a>例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Azure MFA エラー メッセージ

[Azure MFA の技術プロファイル](multi-factor-auth-technical-profile.md)のエラー メッセージ用 ID を次に示します。

| id | 既定値 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 電話に SMS を送信できません。別の電話番号をお試しください。 |
|UserMessageIfInvalidFormat | 電話番号の形式が正しくありません。修正してから、もう一度お試しください。|
|UserMessageIfMaxAllowedCodeRetryReached | 間違ったコードの入力回数が多くなりすぎました。後でもう一度お試しください。|
|UserMessageIfServerError | MFA サービスを使用できませんでした。後でもう一度お試しください。|
|UserMessageIfThrottled | 要求が調整されています。後でもう一度お試しください。|
|UserMessageIfWrongCodeEntered|間違ったコードが入力されました。後でもう一度お試しください。|

### <a name="example"></a>例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

[Azure AD SSPR の技術プロファイル](aad-sspr-technical-profile.md)のエラー メッセージ用 ID を次に示します。

| id | 既定値 |
| -- | ------------- |
|UserMessageIfChallengeExpired | コードの有効期限が切れています。|
|UserMessageIfInternalError | 電子メール サービスで内部エラーが発生しました。後でもう一度お試しください。|
|UserMessageIfThrottled | 送信された要求が多すぎます。後でもう一度お試しください。|
|UserMessageIfVerificationFailedNoRetry | 検証の試行の最大回数を超えました。|
|UserMessageIfVerificationFailedRetryAllowed | 検証に失敗しました。もう一度お試しください。|


### <a name="example"></a>例

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>ワンタイム パスワードのエラー メッセージ

[ワンタイム パスワードの技術プロファイル](one-time-password-technical-profile.md)のエラー メッセージ用 ID を次に示します

| id | 既定値 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |指定されたワンタイム パスワードの検証が最大試行回数を超えました |
|UserMessageIfSessionDoesNotExist |ワンタイム パスワードの検証セッションが期限切れになりました |
|UserMessageIfSessionConflict |ワンタイム パスワードの検証セッションが競合しています |
|UserMessageIfInvalidCode |検証用に指定されたワンタイム パスワードが正しくありません |
|UserMessageIfVerificationFailedRetryAllowed |そのコードが正しくありません。 再試行してください。 | 

### <a name="example"></a>例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>要求の変換のエラー メッセージ

要求の変換のエラー メッセージの ID を次に示します。

| id | 要求の変換 | 既定値 |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 要求の種類 "inputClaim" のブール値の要求値の比較に失敗しました。| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 要求値の比較に失敗しました。指定された左オペランドが右オペランドを超えています。|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | StringComparison "OrdinalIgnoreCase" を使用した要求値の比較に失敗しました。|

### <a name="example"></a>例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>国の一覧

Multi-Factor Authentication で使用される countryList の値を次に示します。

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>次のステップ

ローカライズの例については、次の記事を参照してください。

- [Azure Active Directory B2C でのカスタム ポリシーによる言語のカスタマイズ](custom-policy-localization.md)
- [Azure Active Directory B2C でのユーザー フローによる言語のカスタマイズ](user-flow-language-customization.md)