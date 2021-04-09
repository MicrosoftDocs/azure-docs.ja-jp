---
title: セルフサービス パスワード リセット ポリシー - Azure Active Directory
description: Azure Active Directory のさまざまなセルフサービス パスワード リセット ポリシー オプションについて学習します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 80be5ca22f3dfb673f09327108e66fccc9de6ddd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918045"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Azure Active Directory のパスワード ポリシーとアカウント制限

Azure Active Directory (Azure AD) には、パスワードの複雑さ、長さ、有効期間などの設定を定義するパスワード ポリシーがあります。 また、ユーザー名に使用できる文字と長さを定義するポリシーもあります。

セルフサービス パスワード リセット (SSPR) を使用して Azure AD 内のパスワードを変更またはリセットする場合は、パスワード ポリシーが確認されます。 パスワードがポリシーの要件を満たしていない場合、ユーザーは再試行するように求められます。 Azure 管理者には、通常のユーザー アカウントとは異なる、SSPR の使用についての制限がいくつかあります。

この記事では、Azure AD テナント内のユーザー アカウントに関連付けられたパスワード ポリシー設定および複雑さの要件について、さらに PowerShell を使用してパスワードの有効期限の設定を確認または設定する方法について説明します。

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>ユーザー名ポリシー

Azure AD にサインインする必要があるユーザー アカウントはいずれも、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。 Azure AD Connect を使用してオンプレミスの Active Directory Domain Services (AD DS) 環境を Azure AD に同期させているハイブリッド環境では、既定で Azure AD UPN がオンプレミスの UPN に設定されます。

次の表は、Azure AD に同期されているオンプレミスの AD DS アカウントと、Azure AD で直接作成されたクラウド専用のユーザー アカウントの両方に適用されるユーザー名ポリシーの概要を示しています。

| プロパティ | UserPrincipalName の要件 |
| --- | --- |
| 使用できる文字 |<ul> <li>A - Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 使用できない文字 |<ul> <li>ユーザー名とドメインの間以外にある "\@\" 文字。</li> <li>ピリオド文字 "." を "\@\" 記号の直前に含めることはできません</li></ul> |
| 長さの制限 |<ul> <li>全体の長さは 113 文字以内にする必要があります</li><li>"\@\" 記号の前に最大 64 文字まで可能</li><li>"\@\" 記号の後に最大 48 文字まで可能</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD パスワード ポリシー

パスワード ポリシーは、Azure AD で直接作成および管理されるすべてのユーザー アカウントに適用されます。 これらのパスワード ポリシー設定の一部は変更できませんが、[Azure AD のパスワード保護用のカスタム禁止パスワード](tutorial-configure-custom-password-protection.md)またはアカウント ロックアウト パラメーターを構成することができます。

既定では、間違ったパスワードを使用して 10 回サインインに失敗すると、アカウントはロックアウトされます。 ユーザーは 1 分間ロックされます。 不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 [スマート ロックアウト](howto-password-smart-lockout.md)では、直近 3 つの無効なパスワード ハッシュを追跡して、同じパスワードに対するロックアウト カウンターの増分を回避します。 同じ無効なパスワードが複数回入力された場合、この動作によってアカウントがロック アウトされることはありません。スマート ロックアウトのしきい値と期間を定義できます。

Azure AD のパスワード ポリシーは、*EnforceCloudPasswordPolicyForPasswordSyncedUsers* を有効にしない限り、Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたユーザー アカウントに対して適用されません。

次の Azure AD パスワード ポリシー オプションが定義されています。 特に明記されていない場合、これらの設定を変更することはできません。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |<ul><li>A - Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & \* - \_ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ;</li> <li>空白</li></ul> |
| 使用できない文字 | Unicode 文字。 |
| パスワードの制限 |<ul><li>8 文字以上 256 文字以下。</li><li>次の 4 つのうち、3 つが必要です。<ul><li>小文字。</li><li>大文字。</li><li>数字 (0-9)。</li><li>記号 (上述のパスワード制限を参照してください)。</li></ul></li></ul> |
| パスワードの有効期間 (パスワードの最大有効期間) |<ul><li>既定値:**90** 日。</li><li>値を構成するには、Windows PowerShell 用 Azure Active Directory モジュールから `Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 (ユーザーにパスワードの有効期限が通知されるタイミング) |<ul><li>既定値:**14** 日 (パスワードの有効期限が切れる前)。</li><li>値を構成するには、`Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの有効期限 (パスワードを無期限にします) |<ul><li>既定値: **false** (パスワードの有効期限が指定されていることを示します)。</li><li>各ユーザー アカウントの値を構成するには、`Set-MsolUser` コマンドレットを使用します。</li></ul> |
| パスワード変更履歴 | ユーザーがパスワードを変更する場合、前回のパスワードを再度使用することは *できません*。 |
| パスワード リセット履歴 | ユーザーが忘れたパスワードをリセットする場合、前回のパスワードを再度使用することが ''*できます*''。 |

## <a name="administrator-reset-policy-differences"></a>管理者リセット ポリシーの相違点

既定で、管理者アカウントはセルフサービスのパスワード リセットが有効になっており、強力な既定の *2 ゲート* パスワード リセット ポリシーが適用されます。 このポリシーは、ユーザーに対して定義したものとは異なる場合があります。また、このポリシーを変更することはできません。 パスワードのリセット機能は、必ず Azure 管理者ロールが割り当てられていないユーザーとしてテストする必要があります。

2 ゲート ポリシーでは、管理者にはセキュリティの質問を使用する機能がありません。

2 ゲート ポリシーには、電子メール アドレス、認証アプリ、電話番号など、2 つの認証データが必要です。 2 ゲート ポリシーは次のような状況で適用されます。

* 次のすべての Azure 管理者ロールが影響を受けます。
  * ヘルプデスク管理者
  * サービス サポート管理者
  * 課金管理者
  * パートナー レベル 1 のサポート
  * パートナー レベル 2 のサポート
  * Exchange 管理者
  * メールボックス管理者
  * Skype for Business 管理者
  * ユーザー管理者
  * ディレクトリ ライター
  * グローバル管理者または会社の管理者
  * SharePoint 管理者
  * コンプライアンス管理者
  * アプリケーション管理者
  * セキュリティ管理者
  * 特権ロール管理者
  * Intune 管理者
  * Azure AD 参加済みデバイスのローカル管理者
  * アプリケーション プロキシ サービス管理者
  * Dynamics 365 管理者
  * Power BI サービス管理者
  * 認証管理者
  * パスワード管理者
  * 特権認証管理者

* 試用版サブスクリプションで 30 日が経過している、または
* Azure AD テナント用に、*contoso.com* のようなカスタム ドメインが構成されている、または
* Azure AD Connect がオンプレミスのディレクトリからの ID を同期している

[Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) PowerShell コマンドレットを使用して、管理者アカウントに対する SSPR の使用を無効にすることができます。 `-SelfServePasswordResetEnabled $False` パラメーターを使用すると、管理者の SSPR は無効になります。

### <a name="exceptions"></a>例外

1 ゲート ポリシーには、1 つの認証データが必要です。電子メール アドレスまたは電話番号などです。 1 ゲート ポリシーは次のような状況で適用されます。

* 試用版サブスクリプションの最初の 30 日以内である、または
* Azure AD テナント用にカスタム ドメインが構成されていないため、既定の * *.onmicrosoft.com* を使用している。 既定の * *.onmicrosoft.com* ドメインは、実稼働環境での使用は推奨されません。
* Azure AD Connect が ID と同期していない

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>パスワードの有効期限のポリシー

"*グローバル管理者*" または "*ユーザー管理者*" は、[Windows PowerShell 用 Microsoft Azure AD モジュール](/powershell/module/Azuread/)を使用して、ユーザーのパスワード有効期限が切れないように設定できます。

また、PowerShell コマンドレットを使用すると、期限が切れない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。

このガイダンスは、Intune や Microsoft 365 などの他のプロバイダーに適用され、これらは ID およびディレクトリ サービスについては Azure AD にも依存します。 パスワード有効期限が、ポリシーの変更できる唯一の部分です。

> [!NOTE]
> Azure AD Connect を介して同期されていないユーザー アカウントのパスワードのみ、期限切れにならないように構成できます。 ディレクトリ同期の詳細については、[AD と Azure AD の接続](../hybrid/whatis-hybrid-identity.md)に関するページをご覧ください。

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell を使用したパスワード ポリシーの設定または確認

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストール](/powershell/module/Azuread/)し、[それをお使いの Azure AD テナントに接続](/powershell/module/azuread/connect-azuread#examples)します。

モジュールがインストールされたら、次の手順を使用して、各タスクを必要に応じて完了します。

### <a name="check-the-expiration-policy-for-a-password"></a>パスワードの有効期限ポリシーを確認する

1. PowerShell プロンプトを開き、"*グローバル管理者*" アカウントまたは "*ユーザー管理者*" アカウントを使用して [Azure AD テナントに接続](/powershell/module/azuread/connect-azuread#examples)します。
1. 個々のユーザーまたはすべてのユーザーに対して、次のいずれかのコマンドを実行します。

   * 1 人のユーザーのパスワードが無期限に設定されているかどうかを確認するには、次のコマンドレットを実行します。 `<user ID>` を、確認したいユーザーのユーザー ID (*driley\@contoso.onmicrosoft.com* など) に置き換えます。

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * すべてのユーザーについて **パスワードを無期限にする** 設定を表示するには、次のコマンドレットを実行します。

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>パスワードを期限付きに設定する

1. PowerShell プロンプトを開き、"*グローバル管理者*" アカウントまたは "*ユーザー管理者*" アカウントを使用して [Azure AD テナントに接続](/powershell/module/azuread/connect-azuread#examples)します。
1. 個々のユーザーまたはすべてのユーザーに対して、次のいずれかのコマンドを実行します。

   * 1 人のユーザーのパスワードを期限付きに設定するには、次のコマンドレットを実行します。 `<user ID>` を、確認したいユーザーのユーザー ID (*driley\@contoso.onmicrosoft.com* など) に置き換えます。

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * 組織内のすべてのユーザーのパスワードを期限付きに設定するには、次のコマンドレットを使用します。

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>パスワードを無期限に設定する

1. PowerShell プロンプトを開き、"*グローバル管理者*" アカウントまたは "*ユーザー管理者*" アカウントを使用して [Azure AD テナントに接続](/powershell/module/azuread/connect-azuread#examples)します。
1. 個々のユーザーまたはすべてのユーザーに対して、次のいずれかのコマンドを実行します。

   * 1 人のユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。 `<user ID>` を、確認したいユーザーのユーザー ID (*driley\@contoso.onmicrosoft.com* など) に置き換えます。

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * 組織内のすべてのユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > `-PasswordPolicies DisablePasswordExpiration` を設定したパスワードは、引き続き `pwdLastSet` 属性に基づいて使用時間が計測されます。 `pwdLastSet` 属性に基づいて、有効期限を `-PasswordPolicies None` に変更すると、90 日より古い `pwdLastSet` を持つすべてのパスワードは、ユーザーが次回サインインで変更する必要があります。 この変更は多数のユーザーに影響を与える可能性があります。

## <a name="next-steps"></a>次のステップ

SSPR の使用を始めるには、「[チュートリアル: Azure Active Directory のセルフサービス パスワード リセットを使用して、ユーザーが自分のアカウントのロック解除またはパスワードのリセットを実行できるようにする](tutorial-enable-sspr.md)」を参照してください。

SSPR で問題が発生した場合は、「[セルフサービス パスワード リセットのトラブルシューティング](./troubleshoot-sspr.md)」を参照してください
