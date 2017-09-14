---
title: "Azure AD Connect: パススルー認証のトラブルシューティング | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) のパススルー認証のトラブルシューティングを行う方法について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証のトラブルシューティング, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 72bd39bcf720cf5704274fcdfa0f2b8fc44a77bc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/07/2017

---

# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証のトラブルシューティング

この記事は、Azure AD パススルー認証に関する一般的な問題のトラブルシューティング情報を見つける助けとなります。

>[!IMPORTANT]
>パススルー認証でユーザーのサインインの問題が発生している場合、フォールバックするためのクラウド専用グローバル管理者アカウントを用意せずに機能を無効にしたり、パススルー認証エージェントをアンインストールしたりしないでください。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 これを実行することは欠かせない手順で、テナントからロックアウトされないようになります。

## <a name="general-issues"></a>一般的な問題

### <a name="check-status-of-the-feature-and-authentication-agents"></a>機能と認証エージェントの状態を確認する

テナントでパススルー認証機能がまだ**有効**であること、および認証エージェントの状態が**アクティブ**であり、**非アクティブ**ではないことを確認します。 機能の状態は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)の **[Azure AD Connect]** ブレードで確認できます。

![Azure Active Directory 管理センター - [Azure AD Connect] ブレード](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 管理センター - [パススルー認証] ブレード](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>ユーザーに表示されるサインインのエラー メッセージ

ユーザーがパススルー認証を使用してサインインできない場合、Azure AD のサインイン画面に、次のようなユーザー向けエラーの 1 つが表示されることがあります。 

|エラー|Description|解決策
| --- | --- | ---
|AADSTS80001|Unable to connect to Active Directory (Active Directory に接続できません)|エージェント サーバーが、パスワードを検証する必要のあるユーザーと同じ AD フォレストのメンバーであり、Active Directory に接続できることを確認します。  
|AADSTS8002|A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)|Active Directory が使用可能で、エージェントからの要求に応答していることを確認します。
|AADSTS80004|The username passed to the agent was not valid (エージェントに渡されたユーザー名が無効です)|サインインしようとしているユーザーのユーザー名が正しいことを確認してください。
|AADSTS80005|Validation encountered unpredictable WebException (検証で予測外の WebException が発生しました)|一時的なエラーです。 要求をやり直してください。 引き続きエラーが発生する場合は、Microsoft サポートに連絡してください。
|AADSTS80007|An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)|Check the agent logs for more information and verify that Active Directory is operating as expected. (エージェント ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください。)

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センターでのサインイン失敗の理由

ユーザー サインインの問題のトラブルシューティングでは、最初に [Azure Active Directory 管理センター](https://aad.portal.azure.com/)で[サインイン アクティビティ レポート](../active-directory-reporting-activity-sign-ins.md)を確認します。

![Azure Active Directory 管理センター - サインイン レポート](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で **[Azure Active Directory]** -> **[サインイン]** に移動し、特定のユーザーのサインイン アクティビティをクリックします。 **[サインインのエラー コード]** フィールドを探します。 次の表を使用して、そのフィールドの値を、失敗の理由と解決策にマップします。

|サインイン エラー コード|サインインが失敗した理由|解決策
| --- | --- | ---
| 50144 | ユーザーの Active Directory パスワードの有効期限が切れています。 | オンプレミスの Active Directory でユーザーのパスワードをリセットします。
| 80001 | 利用できる認証エージェントがありません。 | 認証エージェントをインストールして登録します。
| 80002 | 認証エージェントのパスワード検証要求がタイムアウトしました。 | 認証エージェントから Active Directory に到達可能かどうかを調べます。
| 80003 | 認証エージェントが無効な応答を受信しました。 | 複数のユーザーにわたって問題が一貫して再現される場合は、Active Directory の構成を確認します。
| 80004 | サインイン要求で使用されたユーザー プリンシパル名 (UPN) が正しくありません。 | 正しいユーザー名でサインインするようユーザーに求めます。
| 80005 | 認証エージェント: エラーが発生しました。 | 一時的なエラーです。 後でもう一度やり直してください。
| 80007 | 認証エージェントが Active Directory に接続できません。 | 認証エージェントから Active Directory に到達可能かどうかを調べます。
| 80010 | 認証エージェントはパスワードを復号化できません。 | 一貫して問題を再現できる場合は、新しい認証エージェントをインストールして登録します。 また、現在のものはアンインストールします。 
| 80011 | 認証エージェントは復号化キーを取得できません。 | 一貫して問題を再現できる場合は、新しい認証エージェントをインストールして登録します。 また、現在のものはアンインストールします。

## <a name="authentication-agent-installation-issues"></a>認証エージェントのインストールに関する問題

### <a name="an-unexpected-error-occurred"></a>予期しないエラーが発生する

サーバーから[エージェントのログを収集](#collecting-pass-through-authentication-agent-logs)し、問題について Microsoft サポートに連絡してください。

## <a name="authentication-agent-registration-issues"></a>認証エージェントの登録に関する問題

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>ポートがブロックされていたため認証エージェントの登録に失敗した

認証エージェントがインストールされているサーバーが、[こちら](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)に記載されているサービス URL およびポートと通信できることを確認します。

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため認証エージェントの登録に失敗した

Azure AD Connect またはスタンドアロンの認証エージェントのインストールおよび登録のすべての操作に、クラウド専用グローバル管理者アカウントを使用していることを確認します。 MFA 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

### <a name="an-unexpected-error-occurred"></a>予期しないエラーが発生する

サーバーから[エージェントのログを収集](#collecting-pass-through-authentication-agent-logs)し、問題について Microsoft サポートに連絡してください。

## <a name="authentication-agent-uninstallation-issues"></a>認証エージェントのアンインストールに関する問題

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Azure AD Connect のアンインストール時に警告メッセージが表示される

テナントでパススルー認証を有効にしている場合に、Azure AD Connect をアンインストールしようとすると、"Users will not be able to sign-in to Azure AD unless you have other pass-through authentication agents installed on other servers. (他のサーバーに他のパススルー認証エージェントがインストールされていない場合、ユーザーは Azure AD にサインインできなくなります。)" という警告メッセージが表示されます。

ユーザーのサインインを中断しないようにするため、Azure AD Connect をアンインストールする前に、セットアップの種類が[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)であることを確認します。

## <a name="issues-with-enabling-the-feature"></a>機能の有効化に関する問題

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>使用できる認証エージェントがないため機能の有効化に失敗した

テナントでパススルー認証を有効にするには、アクティブな認証エージェントが少なくとも 1 つ必要です。 Azure AD Connect またはスタンドアロンの認証エージェントのいずれかをインストールすれば、認証エージェントをインストールできます。

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>ポートがブロックされていたため機能の有効化に失敗した

Azure AD Connect がインストールされているサーバーが、[こちら](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)に記載されているサービス URL およびポートと通信できることを確認します。

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>トークンまたはアカウント認証エラーのため、機能の有効化に失敗した

機能を有効にする場合は、クラウド専用グローバル管理者アカウントを使用するようにします。 Multi-Factor Authentication (MFA) 対応グローバル管理者アカウントには既知の問題があります。回避策として、MFA を一時的にオフにします (操作を完了するためのみ)。

## <a name="exchange-activesync-configuration-issues"></a>Exchange ActiveSync の構成に関する問題

これは、パススルー認証のために Exchange ActiveSync のサポートを構成するときによく起こる問題です。

### <a name="exchange-powershell-issue"></a>Exchange PowerShell の問題

"**パラメーター名 'PerTenantSwitchToESTSEnabled' に一致するパラメーターが見つかりません。\.**" というエラーが `Set-OrganizationConfig` Exchange PowerShell コマンドの実行時に表示される場合は、Microsoft サポートに問い合わせてください。

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync が機能していない

構成が有効になるまでには時間がかかります。この時間は、お使いの環境によって異なります。 この状態が長く続く場合は、Microsoft サポートに問い合わせてください。

## <a name="collecting-pass-through-authentication-agent-logs"></a>パススルー認証エージェントのログの収集

発生する問題の種類に応じて、異なる場所にあるパススルー認証エージェントのログを確認する必要があります。

### <a name="authentication-agent-event-logs"></a>認証エージェントのイベント ログ

認証エージェントに関するエラーの場合、サーバーでイベント ビューアー アプリケーションを開き、**アプリケーションとサービス ログ\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** の下を調べます。

詳細な分析が必要な場合は "セッション" ログを有効にします。 通常の操作中に、このログを有効にして認証エージェントを実行しないでください。これはトラブルシューティングにのみ使用します。 ログの内容は、ログを再度無効にした後にのみ表示されます。

### <a name="detailed-trace-logs"></a>詳細なトレース ログ

ユーザー サインイン エラーのトラブルシューティングを行うときは、**%programdata%\Microsoft\Azure AD Connect Authentication Agent\Trace\\** でトレース ログを探します。 これらのログには、パススルー認証機能を使用した特定のユーザー サインインが失敗した原因が記録されています。 これらのエラーは、前の[表](#sign-in-failure-reasons-on-the-Azure-portal)に示したサインインの失敗の理由にもマップされます。 次にログ エントリの例を示します。

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

コマンド プロンプトを開き、次のコマンドを実行することで、エラー (上記の例では "1328") の詳細を取得できます (注意: "1328" は、ログで見た実際のエラー番号に置き換えてください)。

`Net helpmsg 1328`

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

監査ログが有効になっている場合は、ドメイン コントローラーのセキュリティ ログで追加情報を確認できます。 パススルー認証エージェントから送信されたサインイン要求を簡単に照会する方法を次に示します。

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

### <a name="performance-monitor-counters"></a>パフォーマンス モニター カウンター

認証エージェントを監視するもう 1 つの方法は、認証エージェントがインストールされている各サーバーで特定のパフォーマンス モニター カウンターを追跡することです。 下記に示すグローバルなカウンター (**# PTA authentications**、**#PTA failed authentications**、**#PTA successful authentications**) とエラー カウンター (**# PTA authentication errors**) を使用します。

![パススルー認証のパフォーマンス モニター カウンター](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>パススルー認証では、負荷分散_ではなく_、複数の認証エージェントを使用して高可用性を確保します。 お使いの構成によっては、必ずしもすべての認証エージェントがほぼ_同数_の要求を受け取るとは_限りません_。 特定の認証エージェントがトラフィックを一切受け取らないということもあり得ます。

