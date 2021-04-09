---
title: Azure AD 認証と承認のエラー コード
description: Azure AD セキュリティ トークン サービス (STS) から返される AADSTS エラー コードについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 03/17/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 56ffa8c33c409e274318407a70f33edaabb5073d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578500"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD 認証と承認のエラー コード

Azure Active Directory (Azure AD) セキュリティ トークン サービス (STS) から返される AADSTS エラー コードに関する情報をお探しですか。 AADSTS エラーの説明、修正、およびいくつかの推奨される回避策を見つけるには、このドキュメントをお読みください。

> [!NOTE]
> この情報は暫定的なもので、変更されることがあります。 ご質問がありますか。またはお探しの情報が見つかりませんでしたか。 GitHub のイシューを作成するか、「[開発者向けのサポート オプションとヘルプ オプション](./developer-support-help-options.md)」で、ヘルプやサポートを受けるためのその他の方法を参照してください。
>
> このドキュメントは、開発者と管理者向けのガイダンスとして提供されています。クライアント自体では決して使用しないでください。 エラー コードは予告なく変更される可能性があります。これは、より詳しいエラー メッセージを提供してアプリケーションを構築中の開発者に役立てていただくためです。 テキストやエラー コード番号に依存するアプリケーションは、時間の経過に伴い正常に機能しなくなります。

## <a name="handling-error-codes-in-your-application"></a>アプリケーションでのエラー コードの処理

[OAuth2.0 仕様](https://tools.ietf.org/html/rfc6749#section-5.2)では、エラー応答の `error` の部分を使用して、認証中のエラーを処理する方法に関するガイダンスが提供されています。 

サンプルのエラー応答を次に示します。

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| パラメーター         | 説明    |
|-------------------|----------------|
| `error`       | 発生したエラーの種類を分類するために使用でき、またエラーに対処するために使用する必要のあるエラー コード文字列。 |
| `error_description` | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 このフィールドをコードでエラーに対処するために使用しないでください。 |
| `error_codes` | 診断に役立つ STS 固有のエラー コードの一覧。  |
| `timestamp`   | エラーが発生した時刻。 |
| `trace_id`    | 診断に役立つ、要求の一意の識別子。 |
| `correlation_id` | コンポーネント間での診断に役立つ、要求の一意の識別子。 |
| `error_uri` |  エラーに関する追加情報が含まれているエラー参照ページへのリンク。  これは、開発者による使用のみを目的にしています。ユーザーには提供しないでください。  エラー参照システムに、エラーに関する追加情報がある場合にのみ存在します。すべてのエラーで追加情報が提供されるわけではありません。|

`error` フィールドには、いくつかの指定できる値があります。特定のエラー ([デバイス コード フロー](v2-oauth2-device-code.md)の `authorization_pending` など) とその対処方法の詳細については、プロトコル ドキュメントのリンクおよび OAuth 2.0 仕様を確認してください。  いくつかの一般的なものを次に示します。

| エラー コード         | 説明        | クライアント側の処理    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 必要なパラメーターが不足しているなどのプロトコル エラーです。 | 要求を修正し再送信します。|
| `invalid_grant`    | 一部の認証情報 (承認コード、更新トークン、アクセス トークン、PKCE チャレンジ) が無効か、解析不能か、見つからないか、またはそれ以外の状態で確認できません | 新しい承認コードを取得するには、`/authorize` エンドポイントへの新しい要求を試してください。  そのアプリのプロトコルの使用を確認および検証することを検討してください。 |
| `unauthorized_client` | 認証されたクライアントは、この承認付与の種類を使用する権限がありません。 | これは、通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| `invalid_client` | クライアント認証に失敗しました。  | クライアント資格情報が有効ではありません。 修正するには、アプリケーション管理者が資格情報を更新します。   |
| `unsupported_grant_type` | 承認サーバーが承認付与の種類をサポートしていません。 | 要求の付与の種類を変更します。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| `invalid_resource` | 対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 | これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。  開発中の場合、これは通常、誤って設定されたテスト テナント、または要求されているスコープの名前の入力ミスを示します。 |
| `interaction_required` | 要求にユーザーの介入が必要です。 たとえば、追加の認証手順が必要です。 | ユーザーが必要なチャレンジをすべて完了できるように、この要求を対話的に、同じリソースで再試行してください。  |
| `temporarily_unavailable` | サーバーが一時的にビジー状態であるため、要求を処理できません。 | 要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |

## <a name="lookup-current-error-code-information"></a>現在のエラー コード情報の参照
エラー コードとメッセージは変更される可能性があります。  最新の情報については、[https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) ページを参照して、AADSTS のエラーの説明、修正、およびいくつかの推奨される回避策を確認してください。  

たとえば、"AADSTS50058" というエラー コードを受け取った場合は、[https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) で "50058" を検索します。  次のように URL にエラー コード番号を追加して、特定のエラーに直接リンクすることもできます。[https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058)

## <a name="aadsts-error-codes"></a>AADSTS エラー コード

| エラー | 説明 |
|---|---|
| AADSTS16000 | SelectUserAccount - これは Azure AD によってスローされる割り込みで、ユーザーが複数の有効な SSO セッションの中から選択できるようにする UI が表示されます。 このエラーはかなり一般的で、`prompt=none` が指定された場合に、アプリケーションに返される可能性があります。 |
| AADSTS16001 | UserAccountSelectionInvalid - セッションの選択ロジックが拒否されているタイルをユーザーがクリックした場合に、このエラーが表示されます。 このエラーがトリガーされた場合、ユーザーはタイル/セッションの最新の一覧から選択するか、別のアカウントを選択することで、回復することができます。 このエラーは、コードの欠陥や競合状態が原因で発生することがあります。 |
| AADSTS16002 | AppSessionSelectionInvalid - アプリで指定されている SID 要件が満たされていません。  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - ユーザーがテナントに明示的に追加されていないことを示します。 |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD はユーザー キーをプロビジョニングできません。 |
| AADSTS20001 | WsFedSignInResponseError - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS20012 | WsFedMessageInvalid - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS20033 | FedMetadataInvalidTenantName - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS40008 | OAuth2IdPUnretryableServerError - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS40010 | OAuth2IdPRetryableServerError - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - フェデレーション ID プロバイダーに問題があります。 この問題を解決するには、IDP に問い合わせてください。 |
| AADSTS50000 | TokenIssuanceError - サインイン サービスに問題があります。 この問題を解決するには、[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md)。 |
| AADSTS50001 | InvalidResource - リソースが無効になっているか、存在しません。 アプリのコードをチェックして、アクセスしようとしているリソースの正確なリソース URL を指定していることを確認します。  |
| AADSTS50002 | NotAllowedTenant - テナントでプロキシ アクセスが制限されているため、サインインが失敗しました。 自分が所有するテナント ポリシーの場合は、制限されたテナント設定を変更して、この問題を解決できます。 |
| AADSTS500021 | '{tenant}' テナントへのアクセスが拒否されました。 AADSTS500021 は、テナント制限機能が構成されており、ユーザーが、ヘッダー `Restrict-Access-To-Tenant` で指定されている許可されたテナントの一覧にないテナントにアクセスしようとしていることを示します。 詳細については、「[テナント制限を使用して SaaS クラウド アプリケーションへのアクセスを管理する](../manage-apps/tenant-restrictions.md)」を参照してください。|
| AADSTS50003 | MissingSigningKey - 署名キーまたは証明書がないために、サインインが失敗しました。 アプリで署名キーが構成されていない可能性があります。 詳細については、エラー [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured) のトラブルシューティングに関する記事を参照してください。 問題が引き続き発生する場合は、アプリの所有者またはアプリ管理者に問い合わせてください。 |
| AADSTS50005 | DevicePolicyError - ユーザーが、条件付きアクセス ポリシーで現在サポートされていないプラットフォームからデバイスにログインしようとしました。 |
| AADSTS50006 | InvalidSignature - 無効な署名のため、署名の検証が失敗しました。 |
| AADSTS50007 | PartnerEncryptionCertificateMissing - このアプリのパートナー暗号化証明書が見つかりませんでした。 この問題を解決するには、Microsoft への[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md)。 |
| AADSTS50008 | InvalidSamlToken - トークンに SAML アサーションがないか、正しく構成されていません。 フェデレーション プロバイダーに問い合わせてください。 |
| AADSTS50010 | AudienceUriValidationFailed - トークン オーディエンスが構成されていないため、アプリのオーディエンス URI の検証が失敗しました。 |
| AADSTS50011 | InvalidReplyTo - 返信アドレスがないか、正しく構成されていません。または、アプリに対して構成されている返信アドレスと一致しません。  解決方法として、この存在していない返信アドレスを Azure Active Directory アプリケーションに必ず追加するか、またはアクセス許可を持つ他のユーザーに Active Directory でアプリケーションを管理してもらいます。 詳細については、エラー [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch) のトラブルシューティングに関する記事を参照してください。|
| AADSTS50012 | AuthenticationFailed - 次のいずれかの理由で認証に失敗しました。<ul><li>署名証明書のサブジェクト名が承認されていない</li><li>承認されたサブジェクト名に一致する信頼された証明機関ポリシーが見つからなかった</li><li>証明書チェーンが無効</li><li>署名証明書が無効</li><li>テナントにポリシーが構成されていない</li><li>署名証明書の拇印が承認されていない</li><li>クライアント アサーションに無効な署名が含まれている</li></ul> |
| AADSTS50013 | InvalidAssertion - さまざまな理由によりアサーションが無効です。たとえば、トークンの発行者が有効期間内の API バージョンと一致しない、期限が切れている、形式が正しくない、アサーションの更新トークンがプライマリ更新トークンではない、などです。 |
| AADSTS50014 | GuestUserInPendingState - ユーザーの受諾が保留中の状態です。 ゲスト ユーザー アカウントがまだ完全には作成されていません。 |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - ユーザーには法的年齢グループの同意が必要です。 |
| AADSTS50017 | CertificateValidationFailed - 次の理由から、証明書の検証が失敗しました。<ul><li>信頼できる証明書に発行側の証明書が見つかりません</li><li>予期される CrlSegment が見つかりきません</li><li>信頼できる証明書に発行側の証明書が見つかりません</li><li>構成されている Delta CRL 配布点に、対応する CRL 配布点がありません</li><li>タイムアウトの問題のため、有効な CRL セグメントを取得できません</li><li>CRL をダウンロードできません</li></ul>テナント管理者に問い合わせてください。 |
| AADSTS50020 | UserUnauthorized - ユーザーがこのエンドポイントの呼び出しを承認されていません。 |
| AADSTS50027 | InvalidJwtToken - 次の理由により JWT トークンが無効です。<ul><li>nonce 要求、サブ要求が含まれていない</li><li>サブジェクト識別子の不一致</li><li>idToken 要求内の要求の重複</li><li>予期しない発行者</li><li>予期しない対象ユーザー</li><li>有効な時間範囲内でない </li><li>トークンの形式が適切ではない</li><li>発行元からの外部 ID トークンが、署名の検証に失敗しました。</li></ul> |
| AADSTS50029 | 無効な URI - ドメイン名に無効な文字が含まれています。 テナント管理者に問い合わせてください。 |
| AADSTS50032 | WeakRsaKey - 脆弱な RSA キーを使用しようとする誤ったユーザー試行を示します。 |
| AADSTS50033 | RetryableError - データベース操作に関連しない一時的なエラーを示します。 |
| AADSTS50034 | UserAccountNotFound - このアプリケーションにサインインするには、アカウントがディレクトリに追加されている必要があります。 |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - ペアワイズ識別子の生成に必要なソルトが、プリンシパル内にありません。 テナント管理者に問い合わせてください。 |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - サブジェクトが、クライアント アサーション内の発行者クレームと一致しません。 テナント管理者に問い合わせてください。 |
| AADSTS50049 | NoSuchInstanceForDiscovery - 不明または無効なインスタンス。 |
| AADSTS50050 | MalformedDiscoveryRequest - 要求が正しい形式ではありません。 |
| AADSTS50053 | IdsLocked - ユーザーが間違ったユーザー ID またはパスワードで何度もサインインを試みたために、アカウントがロックされています。 |
| AADSTS50055 | InvalidPasswordExpiredPassword - パスワードの期限が切れています。 |
| AADSTS50056 | パスワードが無効または null です。パスワードがこのユーザーのストアに存在しません。 |
| AADSTS50057 | UserDisabled - ユーザー アカウントが無効にされています。 アカウントは、管理者によって無効にされています。 |
| AADSTS50058 | UserInformationNotProvided - ユーザーがサインインしていないことを意味します。 これは、ユーザーが認証されておらず、まだサインインしていないときに想定される一般的なエラーです。</br>ユーザーがその前にサインインした SSO のコンテキストでこのエラーが推奨された場合は、SSO セッションが見つからないか無効であることを意味します。</br>prompt=none が指定されている場合、このエラーがアプリケーションに返される可能性があります。 |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - テナントを識別する情報が要求内に見つからず、指定されたどの資格情報でも暗黙的に示されませんでした。 ユーザーはテナント管理者に連絡して問題解決に協力してもらうことができます。 |
| AADSTS50061 | SignoutInvalidRequest - サインアウト要求が無効です。 |
| AADSTS50064 | CredentialAuthenticationError - ユーザー名またはパスワードの資格情報の検証に失敗しました。 |
| AADSTS50068 | SignoutInitiatorNotParticipant - サインアウトに失敗しました。 サインアウトを開始したアプリケーションは現在のセッションの参加者ではありません。 |
| AADSTS50070 | SignoutUnknownSessionIdentifier - サインアウトに失敗しました。 サインアウト要求で、既存のセッションと一致していない名前識別子が指定されました。 |
| AADSTS50071 | SignoutMessageExpired - ログアウト要求の有効期限が切れています。 |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - ユーザーは第 2 要素認証 (対話型) に登録する必要があります。 |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - 強力な認証が必要です。ユーザーが MFA チャレンジに合格しませんでした。 |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - 管理者による構成変更により、またはユーザーが新しい場所に移動したため、ユーザーはリソースにアクセスする際に多要素認証を使用する必要があります。 リソースの新しい承認要求で再試行してください。 |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - 管理者による構成変更により、またはユーザーが新しい場所に移動したため、ユーザーは多要素認証を使用する必要があります。 |
| AADSTS50085 | 更新トークンにソーシャル IDP ログインが必要です。 ユーザーに、ユーザー名とパスワードで再度サインインを試行させます |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - サービスは一時的に利用できません。 やり直してください。 |
| AADSTS50089 | フロー トークンの有効期限が切れています。認証に失敗しました。 ユーザーに、ユーザー名とパスワードを使用してサインインを再試行させてください。 |
| AADSTS50097 | DeviceAuthenticationRequired - デバイス認証が必要です。 |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT 署名が無効です。 |
| AADSTS50105 | EntitlementGrantsNotFound - サインインしているユーザーが、サインインしているアプリのロールに割り当てられていません。 アプリにユーザーを割り当ててください。 詳細については、エラー [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS50107 | InvalidRealmUri - 要求されたフェデレーション領域オブジェクトが存在しません。 テナント管理者に問い合わせてください。 |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - JWT ヘッダーの問題。 テナント管理者に問い合わせてください。 |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - 要求の変換に、無効な入力パラメーターが含まれています。 テナント管理者に問い合わせて、ポリシーを更新してください。 |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - パスワードのリセットまたはパスワード登録エントリのため、サインインが中断されました。 |
| AADSTS50126 | InvalidUserNameOrPassword - 無効なユーザー名またはパスワードにより、資格情報の検証でエラーが発生しました。 |
| AADSTS50127 | BrokerAppNotInstalled - ユーザーは、このコンテンツにアクセスするためにブローカー アプリをインストールする必要があります。 |
| AADSTS50128 | ドメイン名が無効です。テナントを識別する情報が要求内に見つからず、指定されたどの資格情報でも暗黙的に示されませんでした。 |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - デバイスを登録するには、ワークプレースの参加が必要です。 |
| AADSTS50131 | ConditionalAccessFailed - Windows デバイスの状態が無効である、疑わしいアクティビティ、アクセス ポリシー、またはセキュリティ ポリシーの判断によって要求がブロックされたなど、さまざまな条件付きアクセス エラーを示します。 |
| AADSTS50132 | SsoArtifactInvalidOrExpired - パスワードが期限切れまたは最近のパスワード変更により、セッションは無効です。 |
| AADSTS50133 | SsoArtifactRevoked - パスワードが期限切れまたは最近のパスワード変更により、セッションは無効です。 |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - 不適切なデータ センターです。 OAuth 2.0 デバイス フローでアプリケーションによって開始された要求を承認するには、承認者が元の要求が存在する場所と同じデータ センターにいる必要があります。 |
| AADSTS50135 | PasswordChangeCompromisedPassword - アカウントのリスクのため、パスワードの変更が必要です。 |
| AADSTS50136 | RedirectMsaSessionToApp - 単一 MSA セッションが検出されました。 |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - 外部更新トークンがないためセッションが無効です。 |
| AADSTS50140 | KmsiInterrupt - ユーザーがサインインしたときの "サインインしたままにする" 割り込みによりエラーが発生しました。 詳細を調べるための相関 ID、要求 ID、エラー コードを添えて、[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md)。 |
| AADSTS50143 | セッションが一致しません。異なるリソースにより、ユーザーのテナントがドメインのヒントと一致しないため、セッションが無効です。 詳細を調べるための相関 ID、要求 ID、エラー コードを添えて、[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md)。 |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - ユーザーの Active Directory パスワードの有効期限が切れました。 ユーザーに新しいパスワードを生成するか、またはユーザーにセルフサービスのリセット ツールを使用させて、パスワードをリセットしてください。 |
| AADSTS50146 | MissingCustomSigningKey - このアプリは、アプリ固有の署名キーを使って構成する必要があります。 そのように構成されていません。または、キーが有効期限切れか、またはまだ有効になっていません。 |
| AADSTS50147 | MissingCodeChallenge - コードのチャレンジ パラメーターのサイズが無効です。 |
| AADSTS50155 | DeviceAuthenticationFailed - このユーザーのデバイス認証が失敗しました。 |
| AADSTS50158 | ExternalSecurityChallenge - 外部セキュリティ チャレンジが満たされませんでした。 |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - 外部プロバイダーによって送信された要求が十分ではないか、または外部プロバイダーに対して要求された要求がありません。 |
| AADSTS50166 | ExternalClaimsProviderThrottled - クレーム プロバイダーに要求を送信できませんでした。 |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - クライアントは、Windows 10 のアカウントの拡張機能を通じて SSO トークンを取得できますが、要求にトークンがないか、指定されたトークンの有効期限が切れています。 |
| AADSTS50169 | InvalidRequestBadRealm - 領域が、現在のサービス名前空間の構成された領域ではありません。 |
| AADSTS50170 | MissingExternalClaimsProviderMapping - 外部コントロールのマッピングがありません。 |
| AADSTS50173 | FreshTokenNeeded - 許可が取り消されたため有効期限切れです。新しい認証トークンが必要です。 管理者またはユーザーがこのユーザーのトークンを取り消したため、後続のトークン更新が失敗し、再認証が必要です。 ユーザーにサインインの再試行を促してください。 |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - 外部のチャレンジは、パススルー ユーザーに対してサポートされていません。 |
| AADSTS50178 | ExternalChallengeNotSupportedForPassthroughUsers - セッション制御は、パススルー ユーザーに対してサポートされていません。 |
| AADSTS50180 | WindowsIntegratedAuthMissing - 統合 Windows 認証が必要です。 シームレス SSO に対してテナントを有効にしてください。 |
| AADSTS50187 | DeviceInformationNotProvided - サービスはデバイス認証を実行できませんでした。 |
| AADSTS50196 | LoopDetected - クライアント ループが検出されました。 アプリのロジックを調べて、確実にトークンのキャッシュが実装されていて、エラー状態が正しく処理されるようにします。  アプリが非常に短期間にあまりにも多くの同じ要求を行いました。これは、障害がある状態にあるか、またはトークンを不正に要求していることを示しています。 |
| AADSTS50197 | ConflictingIdentities - ユーザーが見つかりませんでした。 もう一度サインインしてみてください。 |
| AADSTS50199 | CmsiInterrupt - セキュリティ上の理由から、この要求にはユーザー確認が必要です。  これは "interaction_required" エラーであるため、クライアントでは対話型認証を行う必要があります。これが発生した理由は、システム Web ビューを使用してネイティブ アプリケーションのトークンが要求されたことにあります。これが実際にサインインしようとしたアプリであったかどうかをたずねるプロンプトをユーザーに表示する必要があります。 このメッセージを表示しないようにするには、リダイレクト URI が次のセーフ リストに含まれている必要があります。 <br />http://<br />https://<br />msauth://(iOS のみ)<br />msauthv2://(iOS のみ)<br />chrome-extension://(デスクトップ Chrome ブラウザーのみ) |
| AADSTS51000 | RequiredFeatureNotEnabled - 機能が無効になっています。 |
| AADSTS51001 | DomainHintMustbePresent - ドメイン ヒントは、オンプレミスのセキュリティ識別子またはオンプレミスの UPN とともに存在している必要があります。 |
| AADSTS51004 | UserAccountNotInDirectory - ディレクトリにユーザー アカウントが存在しません。 |
| AADSTS51005 | TemporaryRedirect - 要求された情報が Location ヘッダーで指定された URI にあることを示す、HTTP ステータス 307 と同等です。 この状態が表示されたら、応答に関連付けられた Location ヘッダーに従います。 元の要求メソッドが POST の場合、リダイレクトされた要求も POST メソッドを使用します。 |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - 統合 Windows 認証が必要です。 ユーザーは、統合 Windows 認証の要求がないセッション トークンを使用してログインしました。 もう一度ログインするように、ユーザーに要求します。 |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - ユーザーが、LinkedIn リソースへのアクセスに同意していません。 |
| AADSTS53000 | DeviceNotCompliant - 条件付きアクセス ポリシーでは準拠デバイスを要求していますが、デバイスが準拠していません。 ユーザーは、Intune などの承認済み MDM プロバイダーにデバイスを登録する必要があります。 |
| AADSTS53001 | DeviceNotDomainJoined - 条件付きアクセス ポリシーではドメイン参加デバイスを要求していますが、デバイスがドメインに参加していません。 ユーザーにドメイン参加デバイスを使用させます。 |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - 使用されているアプリが、条件付きアクセスのために承認されたアプリではありません。 アクセスするには、ユーザーは承認されたアプリの一覧からアプリを 1 つ選んで使用する必要があります。 |
| AADSTS53003 | BlockedByConditionalAccess - 条件付きアクセス ポリシーにより、アクセスがブロックされました。 アクセス ポリシーでは、トークンの発行が許可されていません。 |
| AADSTS53004 | ProofUpBlockedDueToRisk - ユーザーは、このコンテンツにアクセスする前に、多要素認証登録プロセスを完了する必要があります。 ユーザーは多要素認証に登録する必要があります。 |
| AADSTS53011 | ホーム テナントのリスクによりユーザーがブロックされました。 |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - X という ID でアプリケーションを使用することにユーザーまたは管理者が同意していません。このユーザーとリソースのインタラクティブな承認要求を送信してください。 |
| AADSTS65004 | UserDeclinedConsent - ユーザーはアプリへのアクセスの同意を拒否しました。 ユーザーに、再度サインインしてアプリに同意させてください|
| AADSTS65005 | MisconfiguredApplication - アプリの必須リソース アクセス リストに、リソースによって検出可能なアプリが含まれていません。または、必須リソース アクセス リストで指定されていないリソースへのアクセスをクライアント アプリが要求したか、Graph サービスから無効な要求が返されたか、リソースが見つかりません。 アプリが SAML をサポートしている場合、間違った識別子 (エンティティ) でアプリを構成している可能性があります。 詳細については、エラー [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS650052 | このアプリには、組織 `\"{organization}\"` がサブスクライブしていないか有効にしていない `(\"{name}\")` サービスへのアクセスが必要です。 サービス サブスクリプションの構成を確認するには、IT 管理者にお問い合わせください。 |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - 認証に失敗しました。 更新トークンが無効です。 次のいずれかの理由がエラーの原因の可能性があります。<ul><li>トークンのバインド ヘッダーが空</li><li>トークンのバインド ハッシュが一致しない</li></ul> |
| AADSTS70001 | UnauthorizedClient - アプリケーションが無効です。 詳細については、エラー [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS70002 | InvalidClient - 資格情報の検証中にエラーが発生しました。 指定された client_secret が、このクライアントに予期される値と一致しません。 client_secret を修正してから、やり直してください。 詳細については、「[承認コードを使用してアクセス トークンを要求する](v2-oauth2-auth-code-flow.md#request-an-access-token)」を参照してください。 |
| AADSTS70003 | UnsupportedGrantType - アプリがサポートされていない付与タイプを返しました。 |
| AADSTS70004 | InvalidRedirectUri - アプリが無効なリダイレクト URI を返しました。 クライアントによって指定されているリダイレクト アドレスが、構成されているどのアドレス、または OIDC 承認リストのどのアドレスとも、一致しません。 |
| AADSTS70005 | UnsupportedResponseType - 次の理由により、アプリがサポートされていない応答の種類を返しました。<ul><li>応答の種類 "token" がアプリに対して有効になっていません</li><li>応答の種類 "id_token" には "OpenID" スコープが必要です。エンコードされた wctx にサポートされていない OAuth パラメーター値が含まれます</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - アプリが、トークンを要求するときに、`response_mode` のサポートされていない値を返しました。  |
| AADSTS70008 | ExpiredOrRevokedGrant - 非アクティブのため、更新トークンの有効期限が切れました。 トークンは XXX に発行され、一定期間、非アクティブでした。 |
| AADSTS70011 | InvalidScope - アプリによって要求されたスコープが無効です。 |
| AADSTS70012 | MsaServerError - MSA (コンシューマー) ユーザーの認証中にサーバー エラーが発生しました。 やり直してください。 まだ失敗する場合は、[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 デバイス フロー エラー。 承認は保留中です。 デバイスは要求のポーリングを再試行します。 |
| AADSTS70018 | BadVerificationCode - ユーザーがデバイス コード フローに誤ったユーザー コードを入力したため、確認コードが無効です。 認証は承認されていません。 |
| AADSTS70019 | CodeExpired - 確認コードの有効期限が切れました。 ユーザーにサインインを再試行させてください。 |
| AADSTS75001 | BindingSerializationError - SAML メッセージ バインド中にエラーが発生しました。 |
| AADSTS75003 | UnsupportedBindingError - アプリが、サポートされていないバインドに関連するエラーを返しました (SAML プロトコルの応答は、HTTP POST 以外のバインド経由では送信できません)。 |
| AADSTS75005 | Saml2MessageInvalid - Azure AD は、SSO 用のアプリによって送信された SAML 要求をサポートしていません。 詳細については、エラー [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS7500514 | サポートされている SAML 応答の種類が見つかりませんでした。 サポートされている応答の種類は "Response" (XML 名前空間 "urn:oasis:names:tc:SAML:2.0:protocol") または "Assertion" (XML 名前空間 "urn:oasis:names:tc:SAML:2.0:assertion") です。 アプリケーション エラー - 開発者がこのエラーを処理します。|
| AADSTS750054 | SAML リダイレクト バインディング用の HTTP 要求に、SAMLRequest または SAMLResponse がクエリ文字列のパラメーターとしてある必要があります。 詳細については、エラー [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS75008 | RequestDeniedError - SAML 要求に予期しない宛先が設定されているため、アプリからの要求は拒否されました。 |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - サービスでのユーザーの認証に使用された認証方法が、要求された認証方法と一致しません。 詳細については、エラー [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch) のトラブルシューティングに関する記事を参照してください。 |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 認証要求の NameIdPolicy が無効です。 |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - 認証エージェントが Active Directory に接続できません。 エージェント サーバーが、パスワードを検証する必要のあるユーザーと同じ AD フォレストのメンバーであり、Active Directory に接続できることを確認します。 |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - パスワード検証要求がタイムアウトしました。Active Directory が使用可能で、エージェントからの要求に応答していることを確認します。 |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - 認証エージェントからの応答の処理中に不明なエラーが発生しました。 要求をやり直してください。 まだ失敗する場合は、[サポート チケットを開いて](../fundamentals/active-directory-troubleshooting-support-howto.md)、エラーの詳細を取得します。 |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - 認証エージェントはユーザーのパスワードを検証できません。 エージェント ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください。 |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - 認証エージェントはパスワードを復号化できません。 |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - ユーザーは許可されている時間範囲外にログオンしようとしました (時間は AD で指定されています)。 |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - 認証エージェントを実行しているコンピューターと AD の間に時間のずれがあるため、認証の試行を完了できませんでした。 時刻同期の問題を解決してください。 |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos 認証を試みましたが失敗しました。 |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - 認証パッケージがサポートされていません。 |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Authorization ヘッダーが見つかりませんでした。 |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - テナントがシームレス SSO 用に有効化されていません。 |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - ユーザーの Kerberos チケットを検証できません。 |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - ユーザーの Kerberos チケットが期限切れか無効のため、シームレス SSO に失敗しました。 |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - Azure AD にサインインしようとしているユーザーは、デバイスにサインインしているユーザーと異なります。 |
| AADSTS90002 | InvalidTenantName - データ ストアでテナント名が見つかりませんでした。 正しいテナント ID があることを確認します。 |
| AADSTS90004 | InvalidRequestFormat - 要求の形式が正しくありません。 |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - 要求を完了できません。 識別子とログイン ヒントを一緒に使用することはできないため、要求は無効です。  |
| AADSTS90006 | ExternalServerRetryableError - サービスは一時的に利用できません。|
| AADSTS90007 | InvalidSessionId - 要求が正しくありません。 渡されたセッション ID を解析できません。 |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - ユーザーまたは管理者がアプリケーションを使用することに同意していません。 少なくとも、アプリケーションではサインインおよびユーザー プロファイルの読み取りのアクセス許可を指定して Azure AD にアクセスする必要があります。 |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - アプリケーションは、自身のトークンを要求されています。 このシナリオは、指定されたリソースが GUID ベースのアプリケーション ID を使用する場合にのみサポートされます。 |
| AADSTS90010 | NotSupported - アルゴリズムを作成できません。 |
| AADSTS90012 | RequestTimeout - 要求がタイムアウトしました。 |
| AADSTS90013 | InvalidUserInput - ユーザーからの入力が無効です。 |
| AADSTS90014 | MissingRequiredField - このエラー コードは、さまざまなケースで、資格情報に想定されているフィールドが存在しないときに表示される場合があります。 |
| AADSTS90015 | QueryStringTooLong - クエリ文字列が長すぎます。 |
| AADSTS90016 | MissingRequiredClaim - アクセス トークンが無効です。 必要な要求がありません。 |
| AADSTS90019 | MissingTenantRealm - Azure AD で要求からテナント識別子を特定できませんでした。 |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - プリンシパル名の形式が無効、または予期される形式 `name[/host][@realm]` を満たしていません。 プリンシパル名は必須です。ホストと領域は省略可能で、null に設定できます。 |
| AADSTS90023 | InvalidRequest - 認証サービス要求が有効ではありません。 |
| AADSTS9002313 | InvalidRequest - 要求の形式が正しくないか、無効です。 - この問題の原因は、特定のエンドポイントへの要求に何か問題があったことです。 この問題を解決するには、発生したエラーの fiddler トレースを取得し、要求が実際に適切に書式設定されているかどうかを確認します。 |
| AADSTS90024 | RequestBudgetExceededError - 一時的なエラーが発生しました。 やり直してください。 |
| AADSTS90033 | MsodsServiceUnavailable - Microsoft Online Directory Service (MSODS) が使用できません。 |
| AADSTS90036 | MsodsServiceUnretryableFailure - MSODS によってホストされる WCF サービスから再試行できない予期しないエラーが発生しました。 エラーの詳細を取得するには、[サポート チケットを開いてください](../fundamentals/active-directory-troubleshooting-support-howto.md)。 |
| AADSTS90038 | NationalCloudTenantRedirection - 指定したテナント 'Y' は、国内クラウド 'X' に属しています。 現在のクラウド インスタンス 'Z' が X とフェデレーションしていません。クラウドのリダイレクト エラーが返されます。 |
| AADSTS90043 | NationalCloudAuthCodeRedirection - 機能が無効になっています。 |
| AADSTS90051 | InvalidNationalCloudId - 国内クラウド識別子に無効なクラウド識別子が含まれています。 |
| AADSTS90055 | TenantThrottlingError - 着信要求が多すぎます。 この例外は、ブロックされているテナントに対してスローされます。 |
| AADSTS90056 | BadResourceRequest - コードをアクセス トークンと引き換えるには、アプリで `/token` エンドポイントに POST 要求を送信する必要があります。 また、その前に認証コードを提供し、それを POST 要求で `/token` エンドポイントに送信する必要があります。 OAuth 2.0 承認コード フローの概要については、この記事 ([../azuread-dev/v1-protocols-oauth-code.md](../azuread-dev/v1-protocols-oauth-code.md)) を参照してください。 ユーザーを authorization_code を返す `/authorize` エンドポイントにリダイレクトしてください。 `/token` エンドポイントに要求をポストすることで、ユーザーはアクセス トークンを取得します。 Azure portal にログインし、 **[アプリの登録] > [エンドポイント]** の順にチェックして、2 つのエンドポイントが正しく構成されていることを確認します。 |
| AADSTS90072 | PassThroughUserMfaError - ユーザーがサインインに使用した外部アカウントが、ユーザーがサインインしているテナントに存在しません。そのため、ユーザーはテナントの MFA 要件を満たすことができません。 このエラーは、ユーザーが同期されていても、Active Directory と Azure AD 間で ImmutableID (sourceAnchor) 属性に不一致がある場合にも発生する可能性があります。 アカウントをまずテナントに外部ユーザーとして追加する必要があります。 サインアウトして別の Azure AD ユーザー アカウントでサインインしてください。 |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - サービスが WS-Federation メッセージを処理しようとしたときにエラーが発生しました。 メッセージが無効です。 |
| AADSTS90082 | OrgIdWsFederationNotSupported - 要求に対して選択された認証ポリシーは現在サポートされていません。 |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - ゲスト アカウントは、このサイトでは許可されていません。 |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - 会社のオブジェクトがまだプロビジョニングされていないため、サービスはトークンを発行することができません。 |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - ユーザーの DA トークンの有効期限が切れています。 |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - URI から WS-Federation メッセージを作成中にエラーが発生しました。 |
| AADSTS90090 | GraphRetryableError - サービスは一時的に利用できません。 |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - 要求に対して Forbidden エラー コードが Graph から返されました。 |
| AADSTS90094 | AdminConsentRequired - 管理者の同意が必要です。 |
| AADSTS900382 | Confidential Client はクロスクラウド要求でサポートされていません。 |
| AADSTS90099 | アプリケーション '{appId}' ({appName}) は、テナント '{tenant}' で承認されていません。 アプリケーションは、パートナー代理管理者が使用できるようになる前に、顧客テナントへのアクセスが承認されている必要があります。 事前同意を提供するか、適切な Partner Center API を実行してアプリケーションを承認します。 |
| AADSTS900971| 応答アドレスが指定されていません。|
| AADSTS90100 | InvalidRequestParameter - パラメーターが空であるか無効です。 |
| AADSTS901002 | AADSTS901002:"resource" 要求パラメーターはサポートされていません。 |
| AADSTS90101 | InvalidEmailAddress - 指定されたデータは有効な電子メール アドレスはありません。 電子メール アドレスは `someone@example.com` の形式である必要があります。 |
| AADSTS90102 | InvalidUriParameter - 値は有効な絶対 URI である必要があります。 |
| AADSTS90107 | InvalidXml - 要求は無効です。 データは無効な文字がないことを確認してください。|
| AADSTS90114 | InvalidExpiryDate - 一括トークンの有効期限のタイムスタンプが原因で期限切れのトークンが発行されます。 |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - ユーザー コードが null 値または空です。|
| AADSTS90120 | InvalidDeviceFlowRequest - 要求は既に承認されているか拒否されています。 |
| AADSTS90121 | InvalidEmptyRequest - 無効な空の要求です。|
| AADSTS90123 | IdentityProviderAccessDenied - ID または要求の発行プロバイダーが要求を拒否したため、トークンを発行できません。 |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - リソースは、`/common` または `/consumers` エンドポイント経由でサポートされていません。 `/organizations` またはテナント固有のエンドポイントを使用してください。 |
| AADSTS90125 | DebugModeEnrollTenantNotFound - ユーザーはシステムに存在しません。 ユーザー名を正しく入力したか確認してください。 |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - このエンドポイントではこのユーザーの種類はサポートされていません。 システムは、ユーザー名からユーザーのテナントを推論できません。 |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - アプリケーションは、`/common` または `/consumers` エンドポイント経由でサポートされていません。 `/organizations` またはテナント固有のエンドポイントを使用してください。 |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - 再試行できないエラーが発生しました。|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - ユーザー プリンシパルには、構成された NGC ID キーがありません。 |
| AADSTS130005 | NgcInvalidSignature - NGC キー署名の検証に失敗しました。|
| AADSTS130006 | NgcTransportKeyNotFound - NGC トランスポート キーがデバイスに構成されていません。 |
| AADSTS130007 | NgcDeviceIsDisabled - デバイスは無効になっています。 |
| AADSTS130008 | NgcDeviceIsNotFound - NGC キーが参照しているデバイスが見つかりませんでした。 |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - 要求の nonce が指定されていません。 |
| AADSTS140001 | InvalidSessionKey - セッション キーが無効です。|
| AADSTS165900 | InvalidApiRequest - 無効な要求です。 |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - Chrome WebView バージョンがサポートされていません。 |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - リソースは、デバイス専用のトークンを受け入れるように構成されていません。 |
| AADSTS240001 | BulkAADJTokenUnauthorized - ユーザーは、Azure AD にデバイスを登録する権限がありません。 |
| AADSTS240002 | RequiredClaimIsMissing - id_token を `urn:ietf:params:oauth:grant-type:jwt-bearer` 許可として使用できません。|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - テナント管理者によって、この要求をブロックするセキュリティ ポリシーが構成されています。 テナント レベルで定義されているセキュリティ ポリシーを確認し、ご自身の要求がポリシーの要件を満たしているかどうか判断してください。 |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - ディレクトリまたはテナントにアプリケーションが見つかりませんでした。 このエラーは、アプリケーションがテナントの管理者によってインストールされていない場合や、アプリケーションがテナント内のいずれのユーザーによっても同意されていない場合に発生することがあります。 アプリケーションの識別子の値を正しく構成していないか、または間違ったテナントに認証要求を送信した可能性があります。 |
| AADSTS700020 | InteractionRequired - アクセス許可には操作が必要です。 |
| AADSTS700022 | InvalidMultipleResourcesScope - 入力パラメーターのスコープに指定された値に複数のリソースが含まれているため無効です。 |
| AADSTS700023 | InvalidResourcelessScope - アクセス トークンを要求するときに、入力パラメーターのスコープに指定された値が無効です。 |
| AADSTS7000215 | 無効なクライアント シークレットが指定されています。 開発者エラー - アプリは、必要な認証パラメーターまたは正しい認証パラメーターを使用せずにサインインしようとしています。|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided - 指定されたクライアント秘密鍵の有効期限が切れています。 Azure portal にアクセスしてアプリの新しいキーを作成するか、またはセキュリティを強化するために証明書資格情報を使用することを検討してください ([https://aka.ms/certCreds](./active-directory-certificate-credentials.md))。 |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant - 指定された承認コードは、他のテナントに対して使用することを目的にしているため、拒否されました。 OAuth2 承認コードは、それが取得されたときの同じテナント (必要に応じて /common または /{tenant-ID}) に対して引き換える必要があります。 |
| AADSTS1000000 | UserNotBoundError - Bind API では Azure AD ユーザーも外部 IDP による認証が必要ですが、まだ行われていません。 |
| AADSTS1000002 | BindCompleteInterruptError - バインドは正常に完了しましたが、ユーザーに通知する必要があります。 |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled - アプリケーションが無効です。 |
| AADSTS7000114| アプリケーション 'appIdentifier' には、アプリケーションの代理呼び出しを行うことが許可されていません。|
| AADSTS7500529 | 値 ‘SAMLId-Guid’ は有効な SAML ID ではありません- Azure AD ではこの属性を使用して、返される応答の InResponseTo 属性が設定されます。 ID の 1 文字目に数字を使用することはできないので、一般的な方法としては、GUID の文字列表現の前に "id" のような文字列を付加します。 たとえば、id6c1c178c166d486687be4aaf5e482730 は有効な ID です。 |

## <a name="next-steps"></a>次のステップ

* ご質問がありますか。またはお探しの情報が見つかりませんでしたか。 GitHub のイシューを作成するか、「[開発者向けのサポート オプションとヘルプ オプション](./developer-support-help-options.md)」で、ヘルプやサポートを受けるためのその他の方法を参照してください。
