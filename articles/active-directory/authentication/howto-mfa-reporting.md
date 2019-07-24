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
ms.openlocfilehash: 1f78a3135fca290d50370652b33fe0a4d16a6f83
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369056"
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
2. 左側で **[Azure Active Directory]** > **[MFA サーバー]** を選択します。
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

**MFA の必要性:** サインインに MFA が必要かどうか。 ユーザーごとの MFA、条件付きアクセス、または他の理由で、MFA を必須にすることができます。 指定できる値は、**はい**または**いいえ**です。

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

**条件付きアクセス** 以下を含む、サインイン試行に影響を与えた条件付きアクセス ポリシーに関する情報を確認します。

- ポリシー名
- 許可コントロール
- セッション コントロール
- 結果

## <a name="powershell-reporting"></a>PowerShell のレポート

以下の PowerShell を使用して、MFA に登録しているユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

以下の PowerShell を使用して、MFA に登録されていないユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>次の手順

* [ユーザー向け](../user-help/multi-factor-authentication-end-user.md)
* [デプロイする場所](concept-mfa-whichversion.md)
