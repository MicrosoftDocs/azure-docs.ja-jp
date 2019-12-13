---
title: セルフサービスによるパスワード リセット ポリシー - Azure Active Directory
description: Azure AD のセルフ サービスによるパスワード リセット ポリシーのオプションの構成
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95ea51db4f0c6bcdbfa905ff8b57a5a330411e6
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848546"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory のパスワード ポリシーと制限

この記事では、Azure Active Directory (Azure AD) テナント内のユーザー アカウントと関連付けられたパスワード ポリシーと複雑さの要件について説明します。

## <a name="administrator-reset-policy-differences"></a>管理者リセット ポリシーの相違点

**Microsoft は、既定の強力な "*2 ゲート*" パスワード リセット ポリシーをすべての Azure 管理者ロールに適用します**。このポリシーは、ユーザーに対して定義したポリシーと異なる可能性があり、変更することはできません。 パスワードのリセット機能は、必ず Azure 管理者ロールが割り当てられていないユーザーとしてテストする必要があります。

2 ゲート ポリシーでは、**管理者にはセキュリティの質問を使用する機能がありません**。

2 ゲート ポリシーには、**電子メール アドレス**、**認証子アプリ**、**電話番号**など、2 つの認証データが必要です。 2 ゲート ポリシーは次のような状況で適用されます。

* 次のすべての Azure 管理者ロールが影響を受けます。
  * ヘルプデスク管理者
  * サービス サポート管理者
  * 課金管理者
  * パートナー レベル 1 のサポート
  * パートナー レベル 2 のサポート
  * Exchange 管理者
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
  * アプリケーション プロキシ サービス管理者
  * Dynamics 365 管理者
  * Power BI サービス管理者
  * 認証管理者
  * 特権認証管理者

* 試用版サブスクリプションで 30 日が経過している、または
* バニティ ドメインが存在する (contoso.com など)、または
* Azure AD Connect がオンプレミスのディレクトリからの ID を同期している

### <a name="exceptions"></a>例外

1 ゲート ポリシーには、1 つの認証データが必要です。電子メール アドレス*または*電話番号などです。 1 ゲート ポリシーは次のような状況で適用されます。

* 試用版サブスクリプションの最初の 30 日以内である、または
* バニティ ドメインが存在しない (*.onmicrosoft.com)、および
* Azure AD Connect が ID と同期していない

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>すべてのユーザー アカウントに適用される UserPrincipalName ポリシー

Azure AD にサインインする必要があるすべてのユーザー アカウントは、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。 次の表は、クラウドに同期されているオンプレミスの Active Directory ユーザー アカウントとクラウドのみのユーザー アカウントの両方に適用されるポリシーの概要です。

| プロパティ | UserPrincipalName の要件 |
| --- | --- |
| 使用できる文字 |<ul> <li>A - Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| 使用できない文字 |<ul> <li>ユーザー名とドメインの間以外にある "\@\" 文字。</li> <li>ピリオド文字 "." を "\@\" 記号の直前に含めることはできません</li></ul> |
| 長さの制限 |<ul> <li>全体の長さは 113 文字以内にする必要があります</li><li>"\@\" 記号の前に最大 64 文字まで可能</li><li>"\@\" 記号の後に最大 48 文字まで可能</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>クラウド ユーザー アカウントにのみ適用されるパスワード ポリシー

次の表は、Azure AD 内で作成および管理されているユーザー アカウントに適用されるパスワード ポリシー設定の説明です。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |<ul><li>A - Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & \* - \_ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li> <li>空白</li></ul> |
| 使用できない文字 | Unicode 文字。 |
| パスワードの制限 |<ul><li>8 文字以上 256 文字以下。</li><li>次の 4 つのうち、3 つが必要です。<ul><li>小文字。</li><li>大文字。</li><li>数字 (0-9)。</li><li>記号 (上述のパスワード制限を参照してください)。</li></ul></li></ul> |
| パスワードの有効期間 (パスワードの最大有効期間) |<ul><li>既定値:**90** 日。</li><li>値を構成するには、Windows PowerShell 用 Azure Active Directory モジュールから `Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 (ユーザーにパスワードの有効期限が通知されるタイミング) |<ul><li>既定値:**14** 日 (パスワードの有効期限が切れる前)。</li><li>値を構成するには、`Set-MsolPasswordPolicy` コマンドレットを使用します。</li></ul> |
| パスワードの期限切れ (パスワードが期限切れになるか) |<ul><li>既定値: **false** 日 (パスワードの有効期限が有効になっていることを示します)。</li><li>各ユーザー アカウントの値を構成するには、`Set-MsolUser` コマンドレットを使用します。</li></ul> |
| パスワード変更履歴 | ユーザーがパスワードを変更する場合、前回のパスワードを再度使用することは*できません*。 |
| パスワード リセット履歴 | ユーザーが忘れたパスワードをリセットする場合、前回のパスワードを再度使用することは*できます*。 |
| アカウントのロックアウト | 正しくないパスワードでサインイン試行に 10 回失敗すると、ユーザーを 1 分間ロックアウトします。 不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 [スマート ロックアウト](howto-password-smart-lockout.md)では、直近 3 つの無効なパスワード ハッシュを追跡して、同じパスワードに対するロックアウト カウンターの増分を回避します。 同じ無効なパスワードが複数回入力された場合、この動作によってアカウントがロックアウトされることはありません。 |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Azure AD でパスワード有効期限ポリシーを設定する

Microsoft クラウド サービスのグローバル管理者またはユーザー管理者は、Windows PowerShell 用 Microsoft Azure AD モジュールを使用して、ユーザーのパスワード有効期限が切れないように設定できます。 また、Windows PowerShell コマンドレットを使用すると、期限が切れない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。 

このガイダンスは、Intune や Office 365 などの他のプロバイダーに適用され、これらは ID およびディレクトリ サービスについては Azure AD にも依存します。 パスワード有効期限が、ポリシーの変更できる唯一の部分です。

> [!NOTE]
> 有効期限が切れないように構成できるのは、ディレクトリ同期によって同期されていないユーザー アカウントのパスワードだけです。 ディレクトリ同期の詳細については、[AD と Azure AD の接続](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)に関するページをご覧ください。

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell を使用したパスワード ポリシーの設定または確認

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

### <a name="check-the-expiration-policy-for-a-password"></a>パスワードの有効期限ポリシーを確認する

1. ユーザー管理者または会社の管理者の資格情報を使用して Windows PowerShell に接続します。
1. 次のいずれかのコマンドを実行します。

   * 特定のユーザーについてパスワードの有効期限が切れないよう設定されているかどうかを確認するには、確認するユーザーの UPN (例: *aprilr\@contoso.onmicrosoft.com*) またはユーザー ID を使用して、次のコマンドレットを実行します。

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * すべてのユーザーについて**パスワードを無期限にする**設定を表示するには、次のコマンドレットを実行します。

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>パスワードを期限付きに設定する

1. ユーザー管理者または会社の管理者の資格情報を使用して Windows PowerShell に接続します。
1. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを期限付きに設定するには、そのユーザーの UPN またはユーザー ID を使用して、次のコマンドレットを実行します。

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * 組織内のすべてのユーザーのパスワードを期限付きに設定するには、次のコマンドレットを使用します。

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>パスワードを無期限に設定する

1. ユーザー管理者または会社の管理者の資格情報を使用して Windows PowerShell に接続します。
1. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを無期限に設定するには、そのユーザーの UPN またはユーザー ID を使用して、次のコマンドレットを実行します。

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * 組織内のすべてのユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > `-PasswordPolicies DisablePasswordExpiration` を設定したパスワードは、引き続き `pwdLastSet` 属性に基づいて使用時間が計測されます。 ユーザーのパスワードを無期限に設定し、90 日以上たつと、パスワードは期限切れになります。 `pwdLastSet` 属性に基づいて、有効期限を `-PasswordPolicies None` に変更すると、90 日より古い `pwdLastSet` を持つすべてのパスワードは、ユーザーが次回サインインで変更する必要があります。 この変更は多数のユーザーに影響を与える可能性があります。

## <a name="next-steps"></a>次の手順

次の記事では、Azure AD によるパスワードのリセットに関する追加情報が得られます。

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットと変更。](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](../user-help/active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法。](concept-sspr-howitworks.md#authentication-methods)
* [パスワード ライトバックと、それが必要な理由。](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)
