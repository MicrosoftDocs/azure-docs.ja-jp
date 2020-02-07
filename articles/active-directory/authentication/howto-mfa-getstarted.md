---
title: Azure Multi-Factor Authentication をデプロイする - Azure Active Directory
description: Microsoft Azure Multi-Factor Authentication のデプロイの計画
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6da67589b15b4ab043510c0375c26c12f645adb
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155148"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>クラウド ベースの Azure Multi-Factor Authentication のデプロイの計画

ユーザーが組織のリソースに接続するシナリオはますます複雑になっています。 ユーザーは、スマート フォン、タブレット、PC、ラップトップを使用し、多くの場合は複数のプラットフォームで、会社のネットワーク内またはネットワーク外にある、組織所有デバイス、個人デバイス、パブリック デバイスから接続します。 このような常時接続でマルチ デバイスおよびマルチ プラットフォームの世界では、ユーザー アカウントのセキュリティがますます重要になります。 デバイス、ネットワーク、プラットフォームで共通して使用されるパスワードは、その複雑さに関係なく、ユーザー アカウントのセキュリティを確保するのにもはや十分ではありません。ユーザーが複数のアカウントでパスワードを再利用する傾向がある場合は特にそうです。 高度になったフィッシングやその他のソーシャル エンジニア リング攻撃により、ユーザー名とパスワードが悪質な Web で投稿されたり販売されたりする可能性があります。

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) は、データやアプリケーションへのアクセスを保護するのに役立ちます。 第 2 の形式の認証を使用して、セキュリティのレイヤーが追加されます。 組織は、[条件付きアクセス](../conditional-access/overview.md)を使用して、ソリューションを組織の特定のニーズに適合させることができます。

## <a name="prerequisites"></a>前提条件

Azure Multi-factor Authentication のデプロイを始める前に考慮すべき前提条件があります。

| シナリオ | 前提条件 |
| --- | --- |
| 先進認証を使用する**クラウド専用**の ID 環境 | **追加の前提条件タスクはありません** |
| **ハイブリッド** ID のシナリオ | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) がデプロイされ、ユーザー ID はオンプレミスの Active Directory Domain Services および Azure Active Directory と同期またはフェデレーションされています。 |
| クラウド アクセス用に公開されたオンプレミスのレガシ アプリケーション | Azure AD の[アプリケーション プロキシ](../manage-apps/application-proxy.md)がデプロイされています。 |
| Azure MFA と RADIUS 認証の使用 | [ネットワーク ポリシー サーバー (NPS)](howto-mfa-nps-extension.md) がデプロイされています。 |
| ユーザーが Microsoft Office 2010 以前または Apple Mail for iOS 11 以前を使用している | [Microsoft Office 2013 以降](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)および Apple mail for iOS 12 以降にアップグレードします。 条件付きアクセスは、レガシ認証プロトコルではサポートされません。 |

## <a name="plan-user-rollout"></a>ユーザーのロールアウトを計画する

MFA のロールアウト計画には、パイロット デプロイと、それに続くサポート キャパシティ内でのデプロイ ウェーブが含まれる必要があります。 条件付きアクセス ポリシーをパイロット ユーザーの小さなグループに適用することによってロールアウトを開始します。 パイロット ユーザー、使用されたプロセス、および登録動作への影響を評価した後、ポリシーにグループを追加するか、既存のグループにユーザーを追加することができます。

### <a name="user-communications"></a>ユーザーへの伝達

今後の変更、Azure MFA の登録要件、必要なユーザー アクションについて、計画的にユーザーに通知することが重要です。 通信部門、変更管理部門、人事部門など、組織内の代表者と連携して、伝達方法を用意することをお勧めします。

Microsoft では、伝達方法の原案として役立つ[通信テンプレート](https://aka.ms/mfatemplates)と[エンドユーザー文書](../user-help/security-info-setup-signin.md)が用意されています。 ユーザーに [https://myprofile.microsoft.com](https://myprofile.microsoft.com) にアクセスさせ、そのページの **[セキュリティ情報]** リンクを選択して直接登録させることができます。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

Azure Multi-Factor Authentication は、条件付きアクセスでポリシーを適用することによってデプロイされます。 [条件付きアクセス ポリシー](../conditional-access/overview.md)では、次のような特定の条件が満たされた場合、ユーザーに多要素認証を実行するよう要求できます。

* すべてのユーザー、特定のユーザー、グループのメンバー、または割り当てられたロール
* 特定のクラウド アプリケーションでのアクセス
* デバイスのプラットフォーム
* デバイスの状態
* ネットワークの場所または地理的な場所にある IP アドレス
* クライアント アプリケーション
* サインイン リスク (ID 保護が必要)
* 準拠デバイス
* ハイブリッド Azure AD 参加済みデバイス
* 承認されたクライアント アプリケーション

[多要素認証のロールアウトに関する資料](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)にあるカスタマイズ可能なポスターや電子メール テンプレートを使用して、多要素認証を組織にロールアウトします。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>条件付きアクセスを使用して Multi-Factor Authentication を有効にする

条件付きアクセス ポリシーでは登録が強制され、登録されていないユーザーは最初のサインイン時に登録を完了するよう要求されます。これは、重要なセキュリティに関する考慮事項です。

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) は、Azure Multi-factor Authentication の状況に対し、登録ポリシーと、自動化されたリスクの検出と修復のポリシーの両方の点で寄与します。 ID 侵害の脅威があるときにパスワードの変更を強制するポリシー、または次の[イベント](../reports-monitoring/concept-risk-events.md)によってサインインにリスクがあると認められるときに MFA を要求するポリシーを作成できます。

* 漏洩した資格情報
* 匿名の IP アドレスからのサインイン
* 特殊な場所へのあり得ない移動
* 未知の場所からのサインイン
* 感染しているデバイスからのサインイン
* 不審なアクティビティのある IP アドレスからのサインイン

Azure Active Directory Identity Protection によって検出されるリスク検出には、リアルタイムで発生するものや、オフライン処理が必要なものがあります。 管理者は、危険な動作を見せるユーザーをブロックして手動で修復したり、パスワード変更を要求したり、条件付きアクセス ポリシーの一部として多要素認証を要求したりすることを選択できます。

## <a name="define-network-locations"></a>ネットワークの場所を定義する

Microsoft では、組織が条件付きアクセスを使用し、[ネームド ロケーション](../conditional-access/location-condition.md#named-locations)を使用してネットワークを定義することを推奨しています。 組織で Identity Protection を使用している場合は、ネームド ロケーションではなく、リスクベースのポリシーを使用することを検討します。

### <a name="configuring-a-named-location"></a>ネームド ロケーションを構成する

1. Azure portal で **[Azure Active Directory]** を開きます
2. **[セキュリティ]** を選択します。
3. **[管理]** で、 **[ネームド ロケーション]** を選択します。
4. **[新しい場所]** を選択します。
5. **[名前]** フィールドに、わかりやすい名前を入力します
6. 場所の定義に *[IP 範囲]* または *[国/地域]* のどちらを使用するかを選択します
   1. *[IP 範囲]* を使用する場合
      1. *[信頼できる場所としてマークする]* かどうかを決定します。 信頼できる場所からサインインすることで、ユーザーのサインイン リスクが低下します。 入力した IP 範囲が確立され、組織内で信用されていることがわかる場合のみ、この場所を信頼できる場所とマークしてください。
      2. IP 範囲を指定します
   2. *[国/地域]* を使用する場合
      1. ドロップダウン メニューを開き、このネームド ロケーションに対して定義する国または地域を選択します。
      2. *[不明な領域を含める]* かどうかを決定します。 不明な領域は、国/地域にマップできない IP アドレスです。
7. **[作成]**

## <a name="plan-authentication-methods"></a>認証方法を計画する

管理者は、ユーザーに対して使用可能にする[認証方法](../authentication/concept-authentication-methods.md)を選択できます。 主要な方法を使用できないときにユーザーがバックアップの方法を使用できるよう、複数の認証方法を許可することが重要です。 管理者は次の方法を有効にできます。

### <a name="notification-through-mobile-app"></a>モバイル アプリでの通知

モバイル デバイスの Microsoft Authenticator アプリに、プッシュ通知が送信されます。 ユーザーは通知を表示し、 **[承認]** を選択して認証を完了します。 モバイル アプリによるプッシュ通知では、ユーザーへの影響が最も少ないオプションが提供されます。 テレフォニーではなくデータ接続を使用するため、最も信頼性が高くて安全なオプションでもあります。

> [!NOTE]
> 組織に中国勤務のスタッフや中国に出張中のスタッフがいる場合、**Android デバイス**での**モバイル アプリによる通知**メソッドはその国では機能しません。 それらのユーザーには別の方法を使用できるようにする必要があります。

### <a name="verification-code-from-mobile-app"></a>モバイル アプリからの確認コード

Microsoft Authenticator アプリなどのモバイル アプリで、30 秒ごとに新しい OATH 確認コードが生成されます。 ユーザーは確認コードをサインイン インターフェイスに入力します。 モバイル アプリのオプションは、スマート フォンがデータ信号でも携帯ネットワーク信号でも使用できます。

### <a name="call-to-phone"></a>電話の呼び出し

ユーザーに自動音声通話を行います。 ユーザーは、呼び出しに応答し、電話のキーパッドの **#** を押して認証を承認します。 電話の呼び出しは、モバイル アプリからの通知または確認コードに対する優れたバックアップ方法です。

### <a name="text-message-to-phone"></a>電話へのテキスト メッセージ

確認コードを含むテキスト メッセージがユーザーに送信され、ユーザーは確認コードをサインイン インターフェイスに入力するよう求められます。

### <a name="choose-verification-options"></a>認証オプションを選択する

1. **[Azure Active Directory]** 、 **[ユーザー]** 、 **[Multi-Factor Authentication]** に移動します。

   ![Azure Portal の Azure AD ユーザー ブレードから Multi-Factor Authentication ポータルへのアクセス](media/howto-mfa-getstarted/users-mfa.png)

1. 開いた新しいタブで、 **[サービスの設定]** に移動します。
1. **[検証オプション]** で、ユーザーが使用できる方法のすべてのボックスをオンにします。

   ![Multi-Factor Authentication の [サービスの設定] タブで認証方法を構成する](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. **[Save]** をクリックします。
1. **[サービスの設定]** タブを閉じます。

## <a name="plan-registration-policy"></a>登録ポリシーを計画する

管理者は、ユーザーが方法を登録する方法を決定する必要があります。 組織では、Azure MFA とセルフサービス パスワード リセット (SSPR) に対する[新しい組み合わせの登録エクスペリエンスを有効にする](howto-registration-mfa-sspr-combined.md)必要があります。 SSPR では、ユーザーは、多要素認証に使用するのと同じ方法を使用して、セキュリティで保護された方法でパスワードをリセットすることができます。 両方のサービスに 1 回で登録できるユーザーにとって優れたエクスペリエンスであるため、この組み合わせ登録 (現在パブリック プレビュー中) をお勧めします。 SSPR と Azure MFA に対して同じ方法を有効にすると、ユーザーは両方の機能を使用するよう登録できます。

### <a name="registration-with-identity-protection"></a>Identity Protection を使用する登録

組織が Azure Active Directory Identity Protection を使用して場合は、ユーザーが対話形式で次にサインインするときに登録を求めるよう、[MFA 登録ポリシーを構成](../identity-protection/howto-mfa-policy.md)します。

### <a name="registration-without-identity-protection"></a>Identity Protection を使用しない登録

組織が Identity Protection を有効にするライセンスを持っていない場合は、ユーザーは、次に MFA を必要とするサインインを行うときに、登録を求められます。 MFA で保護されたアプリケーションを使用していないユーザーは、MFA に登録されない可能性があります。 悪意のあるユーザーがユーザーのパスワードを推測し、ユーザーに代わって MFA に登録して、効果的にアカウントを制御できるようになるのを防ぐため、すべてのユーザーに登録させることが重要です。

#### <a name="enforcing-registration"></a>登録を強制する

次の手順を使用すると、条件付きアクセス ポリシーはユーザーに Multi-Factor Authentication に登録することを強制できます。

1. グループを作成し、現在登録されていないすべてのユーザーを追加します。
2. 条件付きアクセスを使用して、このグループに、すべてのリソースへのアクセスのための多要素認証を強制します。
3. 定期的に、グループのメンバーシップを再評価し、登録の済んだユーザーをグループから削除します。

[MSOnline PowerShell モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)に依存する PowerShell コマンドを使用して、登録済みと未登録の Azure MFA ユーザーを識別できます。

#### <a name="identify-registered-users"></a>登録済みのユーザーを識別する

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>未登録のユーザーを識別する

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する

ユーザーごとの MFA の使用が有効になっていたユーザーが Azure Multi-Factor Authentication を強制された場合は、次の PowerShell が、条件付きアクセス ベースの Azure Multi-Factor Authentication への変換に役立ちます。

この PowerShell を ISE ウィンドウで実行するか、.PS1 ファイルとして保存し、ローカルで実行します。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 最近、これに応じてこの動作と上記の PowerShell スクリプトを変更しました。 以前は、このスクリプトにより、MFA メソッドが保存され、MFA が無効化され、メソッドが復元されました。 無効化の既定の動作でメソッドが消去されないため、これは不要になりました。

## <a name="plan-conditional-access-policies"></a>条件付きアクセス ポリシーを計画する

MFA やその他の制御がいつ必要になるかを決定する条件付きアクセス ポリシーの戦略を計画するには、[Azure Active Directory の条件付きアクセス](../conditional-access/overview.md)に関するページを参照してください。

誤って自分が Azure AD テナントからロックアウトされないようにすることが重要です。 [テナント内に 2 つ以上の緊急アクセス用アカウントを作成](../users-groups-roles/directory-emergency-access.md)し、それらを条件付きアクセス ポリシーから除外することによって、この誤って管理アクセス権がなくなる影響を軽減できます。

### <a name="create-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

1. グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
   ![パイロット グループ内の Azure portal ユーザーに対して MFA を有効にする条件付きアクセス ポリシーを作成する](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. ポリシーのわかりやすい名前を指定します。
1. **[ユーザーとグループ]** で次の手順を実行します。
   * **[Include]\(追加\)** タブで、 **[All users]\(すべてのユーザー\)** ラジオ ボタンを選択します
   * **[除外]** タブで **[ユーザーとグループ]** のボックスをオンにして、緊急アクセス用アカウントを選択します。
   * **[Done]** をクリックします。
1. **[クラウド アプリ]** で、 **[すべてのクラウド アプリ]** ラジオ ボタンを選択します。
   * オプション: **[Exclude]\(除外\)** タブで、組織において MFA の必要がないクラウド アプリを選択します。
   * **[Done]** をクリックします。
1. **[条件]** セクションで次の手順を実行します。
   * オプション:Azure Identity Protection を有効にしてある場合は、ポリシーの一部としてサインイン リスクを評価することを選択できます。
   * オプション:信頼できる場所またはネームド ロケーションを構成してある場合は、これらの場所をポリシーに追加するか、ポリシーから除外するよう指定できます。
1. **[許可]** で、 **[アクセス権の付与]** ラジオ ボタンが選択されていることを確認します。
    * **[多要素認証を要求する]** のボックスをオンにします。
    * **[選択]** をクリックします。
1. **[セッション]** セクションはスキップします
1. **[ポリシーを有効にする]** トグルを **[オン]** に設定します。
1. **Create** をクリックしてください。

## <a name="plan-integration-with-on-premises-systems"></a>オンプレミスのシステムとの統合を計画する

Azure AD に対して直接認証を行わない一部の従来およびオンプレミスのアプリケーションでは、MFA を使用するために次のような追加手順が必要です。

* 従来のオンプレミス アプリケーションでは、アプリケーション プロキシを使用する必要があります。
* オンプレミスの RADIUS アプリケーションでは、MFA アダプターと NPS サーバーを使用する必要があります。
* オンプレミスの AD FS アプリケーションでは、MFA アダプターと AD FS 2016 以降を使用する必要があります。

Azure AD に対して直接認証し、先進認証 (WS-Fed、SAML、OAuth、OpenID Connect) を使用するアプリケーションは、条件付きアクセス ポリシーを直接使用できます。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシで Azure MFA を使用する

オンプレミスに存在するアプリケーションは、[Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)を使用して Azure AD テナントに公開でき、Azure AD の事前認証を使用するように構成されている場合は、Azure Multi-Factor Authentication を利用できます。

これらのアプリケーションは、他のすべての Azure AD 統合型アプリケーションと同様に、Azure Multi-Factor Authentication を適用する条件付きアクセス ポリシーに従います。

同様に、すべてのユーザー サインインに対して Azure Multi-Factor Authentication が強制されている場合は、Azure AD アプリケーション プロキシで公開されたオンプレミスのアプリケーションは保護されます。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Azure Multi-Factor Authentication とネットワーク ポリシー サーバーを統合する

Azure MFA のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、電話、テキスト メッセージ、またはモバイル アプリによる検証を、既存の認証フローに追加できます。 この統合には次の制限事項があります。

* CHAPv2 プロトコルでは、認証アプリのプッシュ通知と音声通話のみがサポートされます。
* 条件付きアクセス ポリシーは適用できません。

NPS 拡張機能は、RADIUS とクラウドベースの Azure MFA の間のアダプターとして機能し、[VPN](howto-mfa-nps-extension-vpn.md)、[リモート デスクトップ ゲートウェイ接続](howto-mfa-nps-extension-rdg.md)、またはその他の RADIUS 対応アプリケーションを保護するための、第 2 の認証要素を提供します。 この環境で Azure MFA に登録するユーザーには、すべての認証の試みが必要になります。条件付きアクセス ポリシーを使用しない場合は、常に MFA が必要です。

#### <a name="implementing-your-nps-server"></a>NPS サーバーの実装

既に NPS インスタンスがデプロイされて使用されている場合は、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](howto-mfa-nps-extension.md)」をご覧ください。 NPS を初めて設定する場合は、「[ネットワーク ポリシー サーバー (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)」の手順をご覧ください。 トラブルシューティングのガイダンスについては、「[Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)」をご覧ください。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>MFA に登録されていないユーザーのために NPS を準備する

MFA に登録されていないユーザーが認証を試みたときの処理を選択します。 レジストリ パス `HKLM\Software\Microsoft\AzureMFA` のレジストリ設定 `REQUIRE_USER_MATCH` を使用して、機能の動作を制御します。 この設定の構成オプションは 1 つだけです。

| Key | 値 | Default |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | 未設定 (TRUE に相当) |

この設定により、MFA に登録されていないユーザーの扱いが決まります。 この設定を変更したときの効果を、次の表に示します。

| 設定 | ユーザーの MFA の状態 | 効果 |
| --- | --- | --- |
| キーが存在しない | 登録されていない | MFA チャレンジは失敗 |
| 値が True に設定されている/設定されていない | 登録されていない | MFA チャレンジは失敗 |
| キーが False に設定されている | 登録されていない | MFA なしの認証 |
| キーが False または True に設定されている | 登録されている | MFA で認証する必要がある |

### <a name="integrate-with-active-directory-federation-services"></a>Active Directory フェデレーション サービス (AD FS) と統合する

組織が Azure AD とフェデレーションされている場合は、オンプレミスとクラウドの両方で、[Azure Multi-Factor Authentication を使用して AD FS リソースをセキュリティ保護する](multi-factor-authentication-get-started-adfs.md)ことができます。 Azure MFA を使用すると、パスワードを減らし、より安全な認証方法を提供できます。 Windows Server 2016 以降、プライマリ認証用に Azure MFA を構成できるようになりました。

Windows Server 2012 R2 での AD FS とは異なり、AD FS 2016 の Azure MFA アダプターは Azure AD と直接統合されるので、オンプレミスの Azure MFA サーバーは必要ありません。 Azure MFA アダプターは Windows Server 2016 に組み込まれており、追加のインストールは必要ありません。

AD FS 2016 で Azure MFA を使用しており、ターゲット アプリケーションが条件付きアクセス ポリシーに従っている場合は、次の追加の考慮事項があります。

* アプリケーションが、AD FS 2016 以降とフェデレーションされた Azure AD の証明書利用者である場合は、条件付きアクセスを使用できます。
* アプリケーションが AD FS 2016 または AD FS 2019 の証明書利用者であり、AD FS 2016 または AD FS 2019 と共に管理またはフェデレーションされている場合は、条件付きアクセスを使用できません。
* AD FS 2016 または AD FS 2019 がプライマリ認証方法として Azure MFA を使用するように構成されている場合も、条件付きアクセスを使用できません。

#### <a name="ad-fs-logging"></a>AD FS のログ記録

Windows セキュリティ ログと AD FS 管理者ログ両方の標準的な AD FS 2016 および AD FS 2019 ログ記録には、認証要求とその成功または失敗に関する情報が含まれます。 これらのイベント内のイベント ログ データでは、Azure MFA が使用されたかどうかが示されます。 たとえば、AD FS の監査イベント ID 1200 には、次のような情報が含まれる場合があります。

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>証明書を更新および管理する

各 AD FS サーバーでは、ローカル コンピューターの My ストアに、OU=Microsoft AD FS Azure MFA というタイトルの自己署名された Azure MFA 証明書があり、証明書の有効期限の日付が含まれます。 有効期限を確認するには、各 AD FS サーバーでこの証明書の有効期間を調べます。

証明書の有効期限が近づいている場合は、[各 AD FS サーバーで新しい MFA 証明書を生成して検証](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)します。

次のガイダンスでは、AD FS サーバー上の Azure MFA 証明書を管理する方法について詳しく説明します。 Azure MFA で AD FS を構成するとき、`New-AdfsAzureMfaTenantCertificate` PowerShell コマンドレットで生成される証明書の有効期間は 2 年です。 MFA サービスが中断するのを防ぐため、期限が切れる前に証明書を更新してインストールします。

## <a name="implement-your-plan"></a>計画を実装する

ソリューションの計画が済んだので、以下の手順に従って実装できます。

1. 必要な前提条件をすべて満たします
   1. ハイブリッド シナリオの場合は [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) をデプロイします
   1. クラウド アクセス用に公開されるすべてのオンプレミス アプリには [Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)を展開します
   1. RADIUS 認証の場合は [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) をデプロイします
   1. ユーザーが先進認証を有効にしたサポートされるバージョンの Microsoft Office にアップグレードしたことを確認します
1. 選択した[認証方法](#choose-verification-options)を構成します
1. [名前付きのネットワークの場所](../conditional-access/location-condition.md#named-locations)を定義します
1. MFA のロールアウトを開始するグループを選択します。
1. [条件付きアクセス ポリシー](#create-conditional-access-policy)を構成する
1. MFA 登録ポリシーを構成します
   1. [結合された MFA と SSPR](howto-registration-mfa-sspr-combined.md)
   1. [Identity Protection](../identity-protection/howto-mfa-policy.md) の場合
1. ユーザーに通知を送り、[https://aka.ms/mfasetup](https://aka.ms/mfasetup) でユーザーに登録させます
1. [登録したユーザーを追跡します](#identify-non-registered-users)

> [!TIP]
> 政府機関クラウド ユーザーは、[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup) で登録できます

## <a name="manage-your-solution"></a>ソリューションを管理する

Azure MFA に対するレポート

Azure Multi-Factor Authentication では、Azure portal でレポートが提供されます。

| レポート | Location | 説明 |
| --- | --- | --- |
| 利用状況と不正アクセス アラート | [Azure AD] > [サインイン数] | 全体的な利用状況、ユーザーの概要、およびユーザーの詳細に関する情報を提供します。また、指定した日付範囲の間に送信された不正アクセス アラートの履歴も提供します。 |

## <a name="troubleshoot-mfa-issues"></a>MFA の問題をトラブルシューティングする

Azure MFA での一般的な問題の解決策については、Microsoft サポート センターで [Azure Multi-Factor Authentication のトラブルシューティングに関する記事](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)を検索してください。

## <a name="next-steps"></a>次のステップ

* [認証方法とは](concept-authentication-methods.md)
* [Azure Multi-Factor Authentication と Azure AD のセルフサービス パスワード リセット で集中型登録を有効にする](concept-registration-mfa-sspr-converged.md)
* MFA を実行する際にユーザーにプロンプトが表示される場合と表示されない場合については、 [Azure Multi-Factor Authentication のレポートに関するドキュメントの Azure AD サインイン レポート](howto-mfa-reporting.md#azure-ad-sign-ins-report)のセクションを参照してください。
