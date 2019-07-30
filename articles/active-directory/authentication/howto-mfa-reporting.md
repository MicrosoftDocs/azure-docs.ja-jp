---
title: Azure MFA のアクセスおよび使用状況レポート - Azure Active Directory
description: ここでは、Azure Multi-Factor Authentication 機能のレポートを使用する方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd4c6952aab1581a968fc671141792c0e16a7d46
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536962"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のレポート

Azure Multi-Factor Authentication は、Azure Portal からアクセスでき、個人や組織が使用できるいくつかのレポートを提供します。 次の表は使用できるレポートの一覧です。

| レポート | Location | 説明 |
|:--- |:--- |:--- |
| ユーザーのブロックの履歴 | [Azure AD] > [MFA サーバー] > [ユーザーのブロック/ブロック解除] | ユーザーのブロックまたはブロック解除の要求履歴を表示します。 |
| 利用状況と不正アクセス アラート | [Azure AD] > [サインイン数] | 全体的な利用状況、ユーザーの概要、およびユーザーの詳細に関する情報を提供します。また、指定した日付範囲の間に送信された不正アクセス アラートの履歴も提供します。 |
| オンプレミス コンポーネントの利用状況 | [Azure AD] > [MFA サーバー] > [アクティビティ レポート] | NPS の拡張機能、AD FS、MFA サーバーを介した MFA の全体的な利用状況に関する情報を提供します。 |
| ユーザーの認証バイパスの履歴 | [Azure AD] > [MFA サーバー] > [ワンタイム バイパス] | ユーザーの Multi-Factor Authentication バイパスの要求履歴を提供します。 |
| サーバーの状態 | [Azure AD] > [MFA サーバー] > [サーバーの状態] | アカウントに関連付けられている Multi-Factor Authentication Server の状態を示します。 |

## <a name="view-mfa-reports"></a>MFA レポートを表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で **[Azure Active Directory]**  >  **[MFA サーバー]** を選択します。
3. 表示するレポートを選択します。

   ![Azure portal での MFA Server のサーバーの状態レポート](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD サインイン レポート

環境の動作状況を判断するために必要な情報は、[Azure Portal](https://portal.azure.com) の **サインイン アクティビティ レポート**から取得できます。

サインイン レポートを見ることにより、多要素認証 (MFA) の使用状況に関する情報を含め、マネージド アプリケーションの使用状況とユーザーのサインイン アクティビティに関する情報を把握できます。 また、MFA データから、組織内の MFA の動作状況を洞察することができます。 たとえば、次のような疑問の答えがわかります。

- MFA を使用したサインイン チャレンジが実行されたかどうか。
- ユーザーが MFA を完了した方法。
- ユーザーが MFA を完了できなかった理由。
- MFA チャレンジを受けているユーザー数。
- MFA チャレンジを完了できないユーザー数。
- エンド ユーザーが経験している一般的な MFA の問題。

このデータは、[Azure Portal](https://portal.azure.com) と[レポート API](../reports-monitoring/concept-reporting-api.md) から取得できます。

![Azure portal の Azure AD サインイン レポート](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>サインイン レポートの構造

MFA のサインイン アクティビティ レポートから、次の情報にアクセスできます。

**MFA の必要性:** サインインに MFA が必要かどうか。 ユーザーごとの MFA、条件付きアクセス、または他の理由で、MFA が必要になる場合があります。 指定できる値は、**はい**または**いいえ**です。

**MFA の結果:** MFA が満たされたか拒否されたかに関する詳細情報。

- MFA が満たされた場合は、MFA がどのように満たされたかに関する情報がこの列に表示されます。
   - Azure Multi-Factor Authentication
      - completed in the cloud (クラウドで完了しました)
      - has expired due to the policies configured on tenant (テナントに構成されているポリシーのため期限が切れました)
      - registration prompted (登録がプロンプトされました)
      - satisfied by claim in the token (トークンの要求によって満たされました)
      - satisfied by claim provided by external provider (外部プロバイダーから送信された要求によって満たされました)
      - satisfied by strong authentication (強力な認証によって満たされました)
      - skipped as flow exercised was Windows broker logon flow (実行されたフローが Windows ブローカー ログオン フローのためスキップされました)
      - skipped due to app password (アプリ パスワードによりスキップされました)
      - skipped due to location (場所によりスキップされました)
      - skipped due to registered device (登録済みデバイスによりスキップされました)
      - skipped due to remembered device (記憶済みデバイスによりスキップされました)
      - successfully completed (正常に完了しました)
   - Redirected to external provider for multi-factor authentication (多要素認証のために外部プロバイダーにリダイレクトされました)

- MFA が拒否された場合は、この列に拒否の理由が表示されます。
   - Azure Multi-Factor Authentication denied; (Azure Multi-Factor Authentication は拒否されました)
      - authentication in-progress (認証が進行中)
      - duplicate authentication attempt (重複する認証試行)
      - entered incorrect code too many times (不適切なコードが何度も入力されました)
      - invalid authentication (無効な認証)
      - invalid mobile app verification code (無効なモバイル アプリ確認コード)
      - misconfiguration (構成の誤り)
      - phone call went to voicemail (ボイスメールに対する電話の呼び出し)
      - phone number has an invalid format (電話番号の形式が正しくありません)
      - service error (サービス エラー)
      - unable to reach the user’s phone (ユーザーの電話にアクセスできません)
      - unable to send the mobile app notification to the device (モバイル アプリ通知をデバイスに送信できません)
      - unable to send the mobile app notification (モバイル アプリ通知を送信できません)
      - user declined the authentication (ユーザーが認証を拒否しました)
      - user did not respond to mobile app notification (ユーザーがモバイル アプリ通知に応答しませんでした)
      - user does not have any verification methods registered (ユーザーが確認方法を登録していません)
      - user entered incorrect code (ユーザーが不適切なコードを入力しました)
      - user entered incorrect PIN (ユーザーが不適切な PIN を入力しました)
      - user hung up the phone call without succeeding the authentication (ユーザーが認証に成功しないまま電話を切りました)
      - user is blocked (ユーザーはブロックされています)
      - user never entered the verification code (ユーザーは確認コードを入力しませんでした)
      - user not found (ユーザーが見つかりません)
      - verification code already used once (確認コードは既に使用されています)

**MFA の認証方法:** ユーザーが MFA の完了に使用した認証方法。 指定できる値は、次のとおりです。

- テキスト メッセージ
- モバイル アプリの通知
- 電話の呼び出し (認証用電話)
- モバイル アプリの確認コード
- 電話の呼び出し (会社の電話)
- 電話の呼び出し (代替の認証用電話)

**MFA 認証の詳細:** スクラブ バージョンの電話番号 (例: +X XXXXXXXX64)。

**条件付きアクセス** 以下を含む、サインイン試行に影響を与えた条件付きアクセス ポリシーに関する情報を見つけます。

- ポリシー名
- 許可コントロール
- セッション コントロール
- 結果

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>MFA に登録されているユーザーを PowerShell がレポート

最初に、[MSOnline V1 PowerShell モジュール](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)がインストールされていることを確認します。

以下の PowerShell を使用して、MFA に登録しているユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

以下の PowerShell を使用して、MFA に登録されていないユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>アクティビティ レポートで考えられる結果

次の表を利用すると、ダウンロードしたバージョンの多要素認証アクティビティ レポートを使用して多要素認証のトラブルシューティングを行うことができます。 これらは Azure portal には直接表示されません。

| 呼び出しの結果 | 説明 | 概略的な説明 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN Entered (PIN を入力しました) | ユーザーが PIN を入力しました。  認証に成功した場合は、正しい PIN が入力されたためです。  認証が拒否された場合は、間違った PIN が入力されたか、そのユーザーが標準モードに設定されています。 |
| SUCCESS_NO_PIN | Only # Entered (# のみを入力しました) | ユーザーが PIN モードに設定され、認証が拒否された場合、これはユーザーが自分の PIN を入力せずに # のみを入力したことを意味します。  ユーザーが標準モードに設定されていて認証が成功した場合、これはユーザーが # のみを入力したことを意味します。これは標準モードでは正しい操作です。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Not Pressed After Entry (入力後に # が押されていません) | # が入力されていないため、ユーザーは DTMF の数字を送信しませんでした。  入力が完了したことを示す # が入力されない限り、入力された他の数字は送信されません。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | No Phone Input - Timed Out (電話番号の入力なし - タイムアウトになりました) | 電話には出ましたが、応答がありませんでした。  通常、これは通話がボイスメールで取得されたことを示します。 |
| SUCCESS_PIN_EXPIRED | PIN Expired and Not Changed (PIN が期限切れになり、変更されていません) | ユーザーの PIN は期限切れであり、変更を求められましたが、PIN の変更が正常に完了しませんでした。 |
| SUCCESS_USED_CACHE | Used Cache (キャッシュが使用されました) | 構成されたキャッシュのタイムフレーム内に同じユーザー名に対する前回の認証が成功したため、Multi-Factor Authentication の呼び出しなしで認証が成功しました。 |
| SUCCESS_BYPASSED_AUTH | Bypassed Auth (認証がバイパスされました) | ユーザーに対して開始されたワンタイム バイパスを使用して認証が成功しました。  バイパスの詳細については、バイパスされたユーザー履歴レポートに関する記事を参照してください。 |
| SUCCESS_USED_IP_BASED_CACHE | Used IP-based Cache (IP ベースのキャッシュが使用されました) | 構成されたキャッシュのタイムフレーム内に同じユーザー名、アプリケーション名、IP に対する前回の認証が成功したため、Multi-Factor Authentication の呼び出しなしで認証が成功しました。 |
| SUCCESS_USED_APP_BASED_CACHE | Used App-based Cache (アプリベースのキャッシュが使用されました) | 構成されたキャッシュのタイムフレーム内に同じユーザー名、認証の種類、アプリケーション名に対する前回の認証が成功したため、Multi-Factor Authentication の呼び出しなしで認証が成功しました。 |
| SUCCESS_INVALID_INPUT | Invalid Phone Input (無効な電話番号の入力) | 電話から送信された応答が無効です。  これは、FAX 機またはモデムからの可能性があります。または、ユーザーが自分の PIN の一部として「*」を入力した可能性があります。 |
| SUCCESS_USER_BLOCKED | User is Blocked (ユーザーはブロックされています) | ユーザーの電話番号がブロックされています。  ブロックされた番号は、認証の呼び出し中にユーザーにより、または Azure portal を使用している管理者により開始されます。 <br> 注:  ブロックされた番号も不正行為アラートの副産物です。 |
| SUCCESS_SMS_AUTHENTICATED | Text Message Authenticated (テキスト メッセージが認証されました) | 双方向テスト メッセージの場合、ユーザーはワンタイム パスコード (OTP) または OTP + PIN で正しく応答しました。 |
| SUCCESS_SMS_SENT | Text Message Sent (テキスト メッセージが送信されました) | テキスト メッセージの場合、ワンタイム パスコード (OTP) を含むテキスト メッセージは正常に送信されました。  ユーザーは、認証を完了するためにアプリケーションに OTP または OTP + PIN を入力します。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobile App Authenticated (モバイル アプリの認証済み) | ユーザーはモバイル アプリを介して認証に成功しました。 |
| SUCCESS_OATH_CODE_PENDING | OATH Code Pending (OATH コードが保留中です) | ユーザーは OATH コードの入力を求められましたが、応答しませんでした。 |
| SUCCESS_OATH_CODE_VERIFIED | OATH Code Verified (OATH コードの検証済み) | ユーザーはプロンプトが表示されたときに有効な OATH コードを入力しました。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Fallback OATH Code Verified (フォールバック OATH コードの検証済み) | ユーザーは主要な多要素認証方法を使用して認証を拒否されてから、フォールバックの有効な OATH コードを提供されました。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Fallback Security Questions Answered (フォールバック セキュリティの質問が回答されました) | ユーザーは主要な多要素認証方法を使用して認証を拒否されてから、フォールバックのセキュリティの質問に正しく答えました。 |
| FAILED_PHONE_BUSY | Auth Already In Progress (認証が既に進行中です) | Multi-Factor Authentication で既にこのユーザーの認証が処理されています。  多くの場合、これは同じサインオン中に複数の認証要求を送信する RADIUS クライアントによって引き起こされます。 |
| CONFIG_ISSUE | Phone Unreachable (電話に到達できません) | 呼び出しが試行されましたが、発信できなかったか、応答がありませんでした。  これには、ビジー信号、ファスト ビジー信号 (切断)、トライトーン (サービスが停止された番号)、呼び出し中のタイムアウトなどが含まれます。 |
| FAILED_INVALID_PHONENUMBER | Invalid Phone Number Format (無効な電話番号形式です) | 電話番号の形式が正しくありません。  電話番号は数字である必要があります。また、国番号 +1 (米国およびカナダ) では 10 桁である必要があります。 |
| FAILED_USER_HUNGUP_ON_US | User Hung Up the Phone (ユーザーが電話を切りました) | ユーザーは電話に出ましたが、ボタンを押さずに電話を切りました。 |
| FAILED_INVALID_EXTENSION | Invalid Extension (無効な内線です) | 内線に無効な文字が含まれています。  数字、コンマ、*、# のみを使用できます。  @ プレフィックスも使用できます。 |
| FAILED_FRAUD_CODE_ENTERED | Fraud Code Entered (不正なコードが入力されました) | ユーザーが通話中に不正を報告することを選択したため、認証が拒否され電話番号がブロックされました。| 
| FAILED_SERVER_ERROR | Unable to Place Call (電話をかけられません) | Multi-Factor Authentication サービスで電話をかけることができませんでした。 |
| FAILED_SMS_NOT_SENT | Text Message Could Not Be Sent (テキスト メッセージを送信できませんでした) | テキスト メッセージを送信できませんでした。  認証は拒否されます。 |
| FAILED_SMS_OTP_INCORRECT | Text Message OTP Incorrect (テキスト メッセージの OTP が正しくありません) | ユーザーは、受信したテキスト メッセージから正しくないワンタイム パスコード (OTP) を入力しました。  認証は拒否されます。 |
| FAILED_SMS_OTP_PIN_INCORRECT | Text Message OTP + PIN Incorrect (テキスト メッセージの OTP と PIN が正しくありません) | ユーザーは、正しくないワンタイム パスコード (OTP)、正しくないユーザー PIN、またはその両方を入力しました。  認証は拒否されます。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Exceeded Maximum Text Message OTP Attempts (テキスト メッセージ OTP の最大試行回数を超えました) | ユーザーは、ワンタイム パスコード (OTP) の最大試行回数を超えました。 |
| FAILED_PHONE_APP_DENIED | Mobile App Denied (モバイル アプリが拒否されました) | ユーザーがモバイル アプリで [拒否] ボタンを押して認証を拒否しました。 |
| FAILED_PHONE_APP_INVALID_PIN | Mobile App Invalid PIN (モバイル アプリの無効な PIN) | モバイル アプリで認証するときに、ユーザーが無効な PIN を入力しました。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobile App PIN Not Changed (モバイル アプリの PIN は変更されませんでした) | ユーザーがモバイル アプリで必要な PIN の変更を正常に完了しませんでした。 |
| FAILED_FRAUD_REPORTED | Fraud Reported (不正が報告されました) | ユーザーがモバイル アプリで不正を報告しました。 |
| FAILED_PHONE_APP_NO_RESPONSE | Mobile App No Response (モバイル アプリに応答がありません) | ユーザーはモバイル アプリの認証要求に応答しませんでした。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobile App All Devices Blocked (モバイル アプリですべてのデバイスがブロックされました) | このユーザーのモバイル アプリ デバイスは通知に応答しなくなり、ブロックされました。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Mobile App Notification Failed (モバイル アプリの通知に失敗しました) | ユーザーのデバイス上のモバイル アプリに通知を送信しようとしたときにエラーが発生しました。 |
| FAILED_PHONE_APP_INVALID_RESULT | Mobile App Invalid Result (モバイル アプリの無効な結果) | モバイル アプリから無効な結果が返されました。 |
| FAILED_OATH_CODE_INCORRECT | OATH Code Incorrect (OATH コードが正しくありません) | ユーザーが正しくない OATH コードを入力しました。  認証は拒否されます。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH Code + PIN Incorrect (OATH コードと PIN が正しくありません) | ユーザーは、正しくない OATH コード、正しくないユーザー PIN、またはその両方を入力しました。  認証は拒否されます。 |
| FAILED_OATH_CODE_DUPLICATE | Duplicate OATH Code (重複する OATH コード) | ユーザーは、前に使用されていた OATH コードを入力しました。  認証は拒否されます。 |
| FAILED_OATH_CODE_OLD | OATH Code Out of Date (OATH コードが最新ではありません) | ユーザーは、前に使用されていた OATH コードの前の OATH コードを入力しました。  認証は拒否されます。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH Code Result Timeout (OATH コード結果のタイムアウト) | ユーザーの OATH コードの入力に時間がかかりすぎており、Multi-Factor Authentication の試行は既にタイムアウトしました。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Security Questions Result Timeout (セキュリティの質問結果のタイムアウト) | ユーザーのセキュリティの質問に対する回答の入力に時間がかかりすぎており、Multi-Factor Authentication の試行が既にタイムアウトしました。 |
| FAILED_AUTH_RESULT_TIMEOUT | Auth Result Timeout (認証結果のタイムアウト) | ユーザーは、Multi-Factor Authentication の試行に時間がかかりすぎて完了できませんでした。 |
| FAILED_AUTHENTICATION_THROTTLED | Authentication Throttled (認証が調整されています) | 多要素認証の試行がサービスによって調整されました。 |

## <a name="next-steps"></a>次の手順

* [SSPR と MFA の使用状況と分析情報のレポート](howto-authentication-methods-usage-insights.md)
* [ユーザー向け](../user-help/multi-factor-authentication-end-user.md)
* [デプロイする場所](concept-mfa-whichversion.md)
