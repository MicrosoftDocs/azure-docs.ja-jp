---
title: Azure Multi-Factor Authentication のユーザーの状態 - Azure Active Directory
description: Azure Multi-Factor Authentication でユーザーの状態を確認してください。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54146927bf344eed63e24a3df073aa13f7fa0676
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319908"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>ユーザーに 2 段階認証を要求する方法

2 段階認証を要求するには、2 つの方法のいずれかを使用できます。いずれの方法も、全体管理者アカウントを使用する必要があります。 1 つ目は、個々のユーザーに Azure Multi-Factor Authentication (MFA) を有効にする方法です。 ユーザーを個別に有効にした場合は、サインインするたびに 2 段階認証が実行されます (信頼済み IP アドレスからサインインするときや、_記憶されたデバイス_ の機能が有効なときなど、一部例外があります)。 2 つ目のオプションは、特定の条件の基で 2 段階認証を要求する条件付きアクセス ポリシーを設定することです。

> [!TIP]
> 条件付きアクセス ポリシーを使って Azure Multi-Factor Authentication を有効にするのが推奨されるアプローチです。 ライセンスに条件付きアクセスが含まれていない場合 (この場合、ユーザーはサインインするたびに MFA を実行する必要があります) を除き、ユーザーの状態を変更することは推奨されなくなりました。

## <a name="choose-how-to-enable"></a>有効にする方法を選択する

**ユーザーの状態を変更することで有効にする** - 2 段階認証を要求するための従来の方法であり、この記事の中で説明します。 これは、Azure MFA Server とクラウド内の Azure MFA の両方で機能します。 この方法を使用すると、ユーザーはサインインする**たびに** 2 段階認証を実行するよう求められ、条件付きアクセス ポリシーがオーバーライドされます。

条件付きアクセス ポリシーで有効にする - これは、ユーザーに対して 2 段階認証を有効にするための最も柔軟な手段です。 条件付きアクセス ポリシーを使用して有効にする方法は、クラウド内の Azure MFA に対してのみ機能し、Azure AD の Premium 機能です。 この方法の詳細については、「[クラウドベースの Azure Multi-Factor Authentication をデプロイする](howto-mfa-getstarted.md)」を参照してください。

Azure AD Identity Protection で有効にする - この方法では、Azure AD Identity Protection のリスク ポリシーを使用して、すべてのクラウド アプリケーションのサインイン リスクのみに基づいた 2 段階認証を要求します。 この方法では、Azure Active Directory P2 ライセンスが必要です。 この方法の詳細については、「[Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)」を参照してください。

> [!Note]
> ライセンスと価格の詳細については、[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) と [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) の価格に関するページを参照してください。

## <a name="enable-azure-mfa-by-changing-user-state"></a>ユーザーの状態を変更することで Azure MFA を有効する

Azure Multi-factor Authentication のユーザー アカウントには、次の 3 つの異なる状態があります。

| Status | 説明 | 非ブラウザー アプリに影響があるか | ブラウザー アプリに影響があるか | 影響を受ける先進認証 |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |新しいユーザーの既定の状態は、Azure MFA に登録されていません。 |いいえ |いいえ |いいえ |
| Enabled |ユーザーは Azure MFA にサインインできますが、登録されていません。 次回のサインイン時に登録することを求められます。 |いいえ。  これらは登録プロセスが完了するまで機能し続けます。 | はい。 セッションの有効期限が切れると、Azure MFA の登録が必要になります。| はい。 アクセス トークンの有効期限が切れると、Azure MFA の登録が必要になります。 |
| 適用 |ユーザーは、Azure MFA にサインインして Azure MFA に対する登録プロセスを完了しています。 |はい。 アプリはアプリ パスワードを必要とします。 |はい。 ログイン時に Azure MFA が必要です。 | はい。 ログイン時に Azure MFA が必要です。 |

ユーザーの状態は、管理者がユーザーをAzure MFA に登録し、ユーザーが登録プロセスを完了したかどうかを反映します。

すべてのユーザーの状態は、 *[無効]* から始まります。 管理者がユーザーを Azure MFA に登録すると、ユーザーの状態は *[有効]* に変わります。 [有効] 状態のときにユーザーがサインインして登録プロセスを完了すると、ユーザーの状態は *[適用]* に変わります。  

### <a name="view-the-status-for-a-user"></a>ユーザーの状態を表示する

ユーザーの状態を表示および管理できるページにアクセスするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** に移動します。
3. **[Multi-Factor Authentication]** を選択します。
   ![Select Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. 新しいページが開き、ユーザーの状態が表示されます。
   ![多要素認証のユーザーの状態 - スクリーンショット](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>ユーザーの状態を変更する

1. 前述の手順を使用して、Azure Multi-Factor Authentication の**ユーザー** ページを取得します。
2. Azure MFA で有効にするユーザーを見つけます。 上部でビューを変更することが必要になる場合があります。
   ![ユーザー タブで状態を変更するユーザーを選択する](./media/howto-mfa-userstates/enable1.png)
3. ユーザーの名前の横にあるチェック ボックスをオンにします。
4. 右側にある **[クイック操作]** で **[有効にする]** または **[無効にする]** を選択します。
   ![[クイック操作] メニューの [有効にする] をクリックして、選択したユーザーを有効にする](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > "*有効*" なユーザーは、Azure MFA に登録すると自動的に "*適用*" に切り替えられます。 ユーザーの状態を手動で "*適用*" に変更してしないでください。

5. 開いたポップアップ ウィンドウで選択内容を確認します。

ユーザーを有効にした後は、ユーザーにメールで通知します。 次回サインインしたときに登録を求められることをユーザーに伝えます。 また、最新の認証をサポートしていない非ブラウザー アプリを組織で使用している場合は、アプリ パスワードを作成する必要があります。 ユーザーが参照できるように、[Azure MFA エンドユーザー ガイド](../user-help/multi-factor-authentication-end-user.md)に関するページのリンクを含めることもできます。

### <a name="use-powershell"></a>PowerShell の使用

[Azure AD PowerShell](/powershell/azure/overview) を使用してユーザーの状態を変更するには、`$st.State` を変更します。 状態は 3 つあります。

* Enabled
* 適用
* Disabled  

ユーザーを直接 "*適用*" の状態に移さないでください。 "適用" 状態に移行しても、ユーザーが MFA の登録を終えておらず、[アプリのパスワード](howto-mfa-mfasettings.md#app-passwords)を取得していないため、ブラウザーベースでないアプリが動作を停止します。

以下を使用して、まず Module をインストールします。

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> 必ず **Connect-MsolService** を使用して接続してください

この PowerShell スクリプトの例では、個々のユーザーに対して MFA を有効にします。

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

一括でユーザーを有効にする必要がある場合は、PowerShell の使用をお勧めします。 例としては、次のスクリプトでは、ユーザーのリストをループ処理し、それらのアカウントで MFA を有効にします。

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

MFA を無効にするには、次のスクリプトを使用します。

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

次のように短縮することもできます。

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する

次の PowerShell が、条件付きアクセス ベースの Azure Multi-Factor Authentication への変換に役立ちます。

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="next-steps"></a>次の手順

* MFA を実行する際にユーザーにプロンプトが表示される場合と表示されない場合については、 [Azure Multi-Factor Authentication のレポートに関するドキュメントの Azure AD サインイン レポート](howto-mfa-reporting.md#azure-ad-sign-ins-report)のセクションを参照してください。
* 信頼できる IP、カスタム音声メッセージ、不正アクセスのアラートに関する追加の設定を構成する方法については、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。
* Azure Multi-Factor Authentication のユーザー設定の管理について詳しくは、「[クラウドでの Azure Multi-Factor Authentication によるユーザー設定の管理](howto-mfa-userdevicesettings.md)」を参照してください。
