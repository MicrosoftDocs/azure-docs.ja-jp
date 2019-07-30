---
title: Azure MFA NPS 拡張機能のエラー コードのトラブルシューティング | Azure Active Directory
description: Azure Multi-Factor Authentication の NPS 拡張機能の問題を解決するためのヘルプを得る
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d04db6e9ccedc1e67ed0cdfd914ab42ebea0b1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536943"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する

Azure Multi-Factor Authentication の NPS 拡張機能でエラーが発生した場合、この記事を使ってすばやく解決してください。 NPS 拡張機能のログは、NPS 拡張機能がインストールされているサーバーで **[カスタム ビュー]**  >  **[サーバー ロール]**  >  **[Network Policy and Access Services]\(ネットワーク ポリシーと Access Services\)** を選択すると表示されるイベント ビューアーで確認できます。

## <a name="troubleshooting-steps-for-common-errors"></a>一般的なエラーのトラブルシューティング手順

| エラー コード | トラブルシューティングの手順 |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [サポートに連絡](#contact-microsoft-support)し、ログを収集した手順について説明してください。 テナント ID、ユーザー プリンシパル名 (UPN) など、エラーが発生する前の状況について、できるだけ多くの情報をお知らせください。 |
| **CLIENT_CERT_INSTALL_ERROR** | テナントへのクライアント証明書のインストールまたは関連付けに問題がある可能性があります。 [MFA NPS 拡張機能のトラブルシューティング](howto-mfa-nps-extension.md#troubleshooting)の手順に従って、クライアント証明書に関する問題を調査します。 |
| **ESTS_TOKEN_ERROR** | [MFA NPS 拡張機能のトラブルシューティング](howto-mfa-nps-extension.md#troubleshooting)の手順に従って、クライアント証明書および ADAL トークンに関する問題を調査します。 |
| **HTTPS_COMMUNICATION_ERROR** | NPS サーバーが、Azure MFA から応答を受信できません。 ファイアウォールが https://adnotifications.windowsazure.com との間で送受信されるトラフィックに対して双方向に開いていることを確認します。 |
| **HTTP_CONNECT_ERROR** | NPS 拡張機能を実行しているサーバーで、 https://adnotifications.windowsazure.com と https://login.microsoftonline.com/ に到達できることを確認します。 このサイトが読み込まれない場合は、そのサーバーで接続のトラブルシューティングを行います。 |
| **Azure MFA の NPS 拡張機能:** <br> Azure MFA の NPS 拡張機能は、AccessAccept 状態での Radius 要求のセカンダリ認証のみ実行します。 ユーザー username から受け取った要求に対する応答の状態は AccessReject で、要求を無視します。 | このエラーは通常、AD の認証の失敗または NPS サーバーが Azure AD から応答を受信できないことを反映します。 ファイアウォールがポート 80 と 443 を使用して、 https://adnotifications.windowsazure.com および https://login.microsoftonline.com の間で送受信されるトラフィックに対して双方向に開いていることを確認します。 また、[Network Access Permissions]\(ネットワーク アクセス許可) の [DIAL-IN]\(ダイヤル IN) タブで、[control access through NPS Network Policy]\(NPS ネットワーク ポリシーでアクセスを制御する) に設定されていることを確認することも重要です。 また、ユーザーにライセンスが割り当てられていない場合にも、このエラーがトリガーされる可能性があります。 |
| **REGISTRY_CONFIG_ERROR** | レジストリにアプリケーションに対するキーが見つかりません。原因としては、[PowerShell スクリプト](howto-mfa-nps-extension.md#install-the-nps-extension)がインストール後に実行されていないことが考えられます。 見つからないキーは、エラー メッセージに示されています。 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa に、そのキーがあることを確認してください。 |
| **REQUEST_FORMAT_ERROR** <br> Radius 要求に、必須の Radius userName\Identifier 属性がありません。NPS が RADIUS 要求を受信していることを確認します | このエラーは、通常、インストールに問題があることを示します。 NPS 拡張機能は、RADIUS 要求を受信できる NPS サーバーにインストールする必要があります。 RDG、RRAS などのサービスの依存関係としてインストールされた NPS サーバーは、RADIUS 要求を受信しません。 このようにインストールされ、エラーが発生した場合、NPS 拡張機能は、認証要求から詳細情報を読み取ることができないため動作しません。 |
| **REQUEST_MISSING_CODE** | NPS サーバーと NAS サーバーの間のパスワード暗号化プロトコルが 2 つ目として使用している認証方法に対応していることを確認します。 **PAP** は、クラウドでの Azure MFA のすべての認証方法 (電話、一方向テキスト メッセージ、モバイル アプリの通知、およびモバイル アプリの確認コード) をサポートします。 **CHAPV2** と **EAP** は、電話とモバイル アプリの通知をサポートします。 |
| **USERNAME_CANONICALIZATION_ERROR** | ユーザーがオンプレミスの Active Directory インスタンスに存在すること、また、ディレクトリへのアクセス許可が NPS サービスに付与されていることを確認します。 フォレスト間の信頼を使用している場合、詳細については、[サポートにお問い合わせください](#contact-microsoft-support)。 |

### <a name="alternate-login-id-errors"></a>代替ログイン ID エラー

| エラー コード | エラー メッセージ | トラブルシューティングの手順 |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | エラー: userObjectSid の検索に失敗しました | ユーザーがオンプレミスの Active Directory インスタンスに存在することを確認します。 フォレスト間の信頼を使用している場合、詳細については、[サポートにお問い合わせください](#contact-microsoft-support)。 |
| **ALTERNATE_LOGIN_ID_ERROR** | エラー:代替 LoginId の検索に失敗しました | LDAP_ALTERNATE_LOGINID_ATTRIBUTE が[有効な Active Directory 属性](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx)に設定されていることを確認します。 <br><br> LDAP_FORCE_GLOBAL_CATALOG が True に設定されているか、LDAP_LOOKUP_FORESTS が空でない値で構成されている場合は、グローバル カタログが構成され、それに AlternateLoginId 属性が追加されていることを確認します。 <br><br> LDAP_LOOKUP_FORESTS が空でない値で構成されている場合は、値が正しいことを確認します。 複数のフォレスト名がある場合、名前はスペースではなくセミコロンで区切る必要があります。 <br><br> これらの手順で問題が解決されない場合は、[サポートにお問い合わせください](#contact-microsoft-support)。 |
| **ALTERNATE_LOGIN_ID_ERROR** | エラー:代替 LoginId 値が空です | ユーザーの AlternateLoginId 属性が構成されていることを確認します。 |

## <a name="errors-your-users-may-encounter"></a>ユーザー側で発生する可能性があるエラー

| エラー コード | エラー メッセージ | トラブルシューティングの手順 |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | 呼び出し元のテナントに、ユーザーの認証を行うためのアクセス許可がありません | テナント ドメインとユーザー プリンシパル名 (UPN) のドメインが同じかどうかを確認します。 たとえば、user@contoso.com が、Contoso テナントに対して認証しようとしていることを確認します。 UPN は、Azure のテナントの有効なユーザーを表します。 |
| **AuthenticationMethodNotConfigured** | 指定した認証方法が、ユーザーに対して構成されていません | 「[2 段階認証設定の管理](../user-help/multi-factor-authentication-end-user-manage-settings.md)」の説明に従って、ユーザーに認証方法を追加または確認してもらいます。 |
| **AuthenticationMethodNotSupported** | 指定した認証方法がサポートされていません。 | このエラーを含むすべてのログを収集し、[サポートに連絡](#contact-microsoft-support)します。 サポートに連絡するとき、ユーザー名と、エラーをトリガーしたセカンダリ検証方法をお伝えください。 |
| **BecAccessDenied** | MSODS Bec 呼び出しによってアクセス拒否が返されました。テナントでユーザー名が定義されていない可能性があります | ユーザーは、オンプレミスの Active Directory に存在しますが、AD Connect で Azure AD に同期されていません。 または、テナント用のユーザーがありません。 「[2 段階認証設定の管理](../user-help/multi-factor-authentication-end-user-manage-settings.md)」の説明に従って、ユーザーを Azure AD に追加し、そのユーザーに認証方法を追加してもらいます。 |
| **InvalidFormat** または**StrongAuthenticationServiceInvalidParameter** | 電話番号の形式を認識できません | ユーザーに確認の電話番号を修正してもらいます。 |
| **InvalidSession** | 指定したセッションが無効か、有効期限が切れている可能性があります | セッション完了までの時間が 3 分を超えました。 認証要求を開始してから 3 分以内にユーザーが確認コードを入力していること、またはアプリの通知に応答していることを確認します。 問題が解決しない場合は、クライアント、NAS サーバー、NPS サーバー、および Azure MFA のエンドポイント間でネットワーク待ち時間が発生していないことを確認します。  |
| **NoDefaultAuthenticationMethodIsConfigured** | 既定の認証方法が、ユーザーに対して構成されていません | 「[2 段階認証設定の管理](../user-help/multi-factor-authentication-end-user-manage-settings.md)」の説明に従って、ユーザーに認証方法を追加または確認してもらいます。 ユーザーが既定の認証方法を選択し、その方法を自身のアカウントに対して構成していることを確認します。 |
| **OathCodePinIncorrect** | 入力したコードと PIN が誤っています。 | このエラーは、NPS 拡張機能では発生しません。 このエラーが発生した場合、トラブルシューティング方法については、[サポートにお問い合わせください](#contact-microsoft-support)。 |
| **ProofDataNotFound** | プルーフ データが、指定した認証方法に対して構成されていません。 | 「[2 段階認証設定の管理](../user-help/multi-factor-authentication-end-user-manage-settings.md)」の説明に従って、ユーザーに別の認証方法を試してもらうか、新しい認証方法を追加してもらいます。 認証方法が適切に設定されていることを確認した後も、このエラーが引き続き発生する場合は、[サポートにお問い合わせください](#contact-microsoft-support)。 |
| **SMSAuthFailedWrongCodePinEntered** | 入力したコードと PIN が誤っています。 (OneWaySMS) | このエラーは、NPS 拡張機能では発生しません。 このエラーが発生した場合、トラブルシューティング方法については、[サポートにお問い合わせください](#contact-microsoft-support)。 |
| **TenantIsBlocked** | テナントがブロックされています | [サポートにお問い合わせください](#contact-microsoft-support)。お問い合わせの際は、Azure Portal の Azure AD プロパティ ページのディレクトリ ID をご用意ください。 |
| **UserNotFound** | 指定したユーザーが見つかりませんでした | テナントが Azure AD でアクティブとして表示されなくなりました。 サブスクリプションがアクティブで、必要なファースト パーティー アプリがあることを確認します。 また、証明書サブジェクトが意図したとおりであること、およびその証明書が引き続き有効で、サービス プリンシパルで登録されていることを確認します。 |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>ユーザーに表示されるエラー以外のメッセージ

認証要求の失敗が原因で、Multi-Factor Authentication からメッセージが表示されることもあります。 これは構成の結果としてエラーが発生したことを示すものではありません。認証要求が拒否された理由を説明する意図的な警告です。

| エラー コード | エラー メッセージ | 推奨される手順 | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | 誤ったコードが入力されました\OATH コードが正しくありません | ユーザーが誤ったコードを入力しました。 新しいコードを要求するか、サインインし直したうえで、もう一度試すようユーザーに指示します。 | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | 許可されている最大コード再試行回数に達しました | ユーザーが何回も検証チャレンジに失敗しました。 設定によっては、管理者によるブロック解除が必要になることがあります。  |
| **SMSAuthFailedWrongCodeEntered** | 誤ったコードが入力されました/テキスト メッセージ OTP が正しくありません | ユーザーが誤ったコードを入力しました。 新しいコードを要求するか、サインインし直したうえで、もう一度試すようユーザーに指示します。 |

## <a name="errors-that-require-support"></a>サポートを必要とするエラー

次のいずれかのエラーが発生した場合は、診断のために[サポートに連絡](#contact-microsoft-support)することをお勧めします。 こうしたエラーに対処する手順に標準はありません。 サポートに問い合わせるときは、エラーを引き起こした状況や手順に関するできるだけ多くの情報、およびテナント情報を必ずお知らせください。

| エラー コード | エラー メッセージ |
| ---------- | ------------- |
| **InvalidParameter** | 要求は null にできません |
| **InvalidParameter** | ObjectId は ReplicationScope {0} に対して null または空にできません |
| **InvalidParameter** | CompanyName \{0}\ は、長さの制限 {1} を超えています |
| **InvalidParameter** | UserPrincipalName は null または空にできません |
| **InvalidParameter** | 指定した TenantId の形式が正しくありません |
| **InvalidParameter** | SessionId は null または空にできません |
| **InvalidParameter** | 要求または Msods からの ProofData を解決できませんでした。 ProofData は不明です |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>次の手順

### <a name="troubleshoot-user-accounts"></a>ユーザー アカウントをトラブルシューティングする

[2 段階認証で問題が発生](../user-help/multi-factor-authentication-end-user-troubleshoot.md)している場合は、その問題を自己診断できるようユーザーをサポートします。

### <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

さらにサポートが必要な場合は、[Azure Multi-Factor Authentication Server サポート](https://support.microsoft.com/oas/default.aspx?prid=14947)を通して、サポートのプロフェッショナルにお問い合わせください。 お問い合わせの際は、問題に関する情報をできるだけお知らせいただくと役に立ちます。 エラーが表示されたページ、具体的なエラー コード、具体的なセッション ID、エラーが表示されたユーザーの ID、デバッグ ログなどの情報をご提供ください。

サポート診断用のデバッグ ログを収集するには、NPS サーバー上で次の手順に従います。

1. レジストリ エディターを開き、[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa] を参照して、 **[VERBOSE_LOG]** を **[TRUE]** に設定します。
2. 管理者のコマンド プロンプトを開き、次のコマンドを実行します。

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. 問題を再現します

4. 次のコマンドでトレースを停止します。

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. レジストリ エディターを開き、[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa] を参照して、 **[VERBOSE_LOG]** を **[FALSE]** に設定します。
6. C:\NPS フォルダーの内容を zip 形式で圧縮し、zip ファイルをサポート ケースに添付します。
