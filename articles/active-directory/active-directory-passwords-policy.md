---
title: "ポリシー: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセット ポリシーのオプション"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD セルフサービスによるパスワードのリセット"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b35c5d126375735f070a7fe2331896c524b5a61
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory のパスワード ポリシーと制限

この記事では、Azure AD テナントに格納されているユーザー アカウントと関連付けられたパスワード ポリシーと複雑さの要件について説明します。

## <a name="administrator-password-policy-differences"></a>管理者パスワード ポリシーの相違点

Microsoft では、Azure 管理者ロール (例: 全体管理者、ヘルプデスク管理者、パスワード管理者など) に対して、既定の強力な **2 ゲート** パスワード リセット ポリシーを適用します。

これにより、管理者がセキュリティの質問を使用できなくなり、次のことが適用されます。

認証データの 2 つの部分 (電子メール アドレス**および**電話番号) を必要とする 2 つのゲート ポリシーは、次の状況で適用されます。

* すべての Azure 管理者ロール
  * ヘルプデスク管理者
  * サービス サポート管理者
  * 課金管理者
  * パートナー レベル 1 のサポート
  * パートナー レベル 2 のサポート
  * Exchange サービス管理者
  * Lync サービス管理者
  * ユーザー アカウント管理者
  * ディレクトリ ライター
  * グローバル管理者/会社の管理者
  * SharePoint サービス管理者
  * コンプライアンス管理者
  * アプリケーション管理者
  * セキュリティ管理者
  * 特権ロール管理者
  * Intune サービス管理者
  * アプリケーション プロキシ サービス管理者
  * CRM サービス管理者
  * Power BI サービス管理者
  
* 30 日間の試用期間が経過した、**または**
* バニティ ドメインが存在する (contoso.com)、**または**
* Azure AD Connect がオンプレミスのディレクトリからの ID を同期している

### <a name="exceptions"></a>例外
認証データの 1 つの部分 (電子メール アドレス**または**電話番号) を必要とする 1 つのゲート ポリシーは、次の状況で適用されます。

* 最初の 30 日間の試用期間、**または**
* バニティ ドメインが存在しない (*.onmicrosoft.com)、**かつ**、Azure AD Connect が ID を同期していない


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>すべてのユーザー アカウントに適用される UserPrincipalName ポリシー

Azure AD にサインインする必要があるすべてのユーザー アカウントは、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。 下の表は、クラウドと同期するオンプレミスの Active Directory ユーザー アカウントとクラウドのみのユーザー アカウントの両方に適用されるポリシーの概要です。

| プロパティ | UserPrincipalName の要件 |
| --- | --- |
| 使用できる文字 |<ul> <li>A - Z</li> <li>a - z</li><li>0 – 9</li> <li> に関するページを参照してください。 - \_ ! \# ^ \~</li></ul> |
| 使用できない文字 |<ul> <li>ユーザー名とドメインの間以外にある '@' 文字</li> <li>'@' 記号の直前のピリオド文字 '.'</li></ul> |
| 長さの制限 |<ul> <li>全体の長さは 113 文字以内にする必要があります</li><li>‘@’ 記号の前に 64 文字</li><li>‘@’ 記号の後に 48 文字</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>クラウド ユーザー アカウントにのみ適用されるパスワード ポリシー

次の表は、Azure AD で作成し、管理しているユーザー アカウントに適用できるパスワード ポリシー設定の説明です。

| プロパティ | 必要条件 |
| --- | --- |
| 使用できる文字 |<ul><li>A - Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 使用できない文字 |<ul><li>Unicode 文字</li><li>スペース</li><li> **強力なパスワードのみ**: '@' 記号の直前にピリオド文字 ('.') を含めることはできません</li></ul> |
| パスワードの制限 |<ul><li>8 文字以上 16 文字以下</li><li>**強力なパスワードのみ**: 次の 4 つのうち、3 つが必要です。<ul><li>小文字</li><li>大文字</li><li>数値 (0 - 9)</li><li>記号 (上述のパスワード制限を参照してください)</li></ul></li></ul> |
| パスワードの有効期間 |<ul><li>規定値: **90** 日 </li><li>値を構成するには、Windows PowerShell の Azure Active Directory モジュールから Set-MsolPasswordPolicy コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 |<ul><li>既定値: **14** 日 (パスワードの有効期限が切れる前)</li><li>値は Set-MsolPasswordPolicy コマンドレットを使用して構成できます。</li></ul> |
| パスワードの期限切れ |<ul><li>既定値: **false** 日 (パスワードの有効期限が有効になっていることを示します) </li><li>各ユーザー アカウントの値を構成するには、Set-MsolUser コマンドレットを使用します。 </li></ul> |
| パスワード**変更**履歴 |パスワードを**変更する**ときに、前回のパスワードをもう一度使用することは**できません**。 |
| パスワード **リセット**履歴 | 忘れたパスワードを**リセット**するときに、前回のパスワードをもう一度使用することが**できます**。 |
| アカウントのロックアウト |正しくないパスワードを入力してサインイン試行に 10 回失敗したユーザーを、1 分間ロックアウトします。 不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Azure Active Directory でパスワード有効期限ポリシーを設定する

Microsoft クラウド サービスのグローバル管理者は、Windows PowerShell 用 Microsoft Azure Active Directory モジュールを使用して、ユーザーのパスワードの有効期限が切れないように設定できます。 また、Windows PowerShell コマンドレットを使用すると、期限が切れない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。 このガイダンスは、ID およびディレクトリ サービスについては Microsoft Azure Active Directory にも依存する、Microsoft Intune や Office 365 などの他のプロバイダーに適用されます。

> [!NOTE]
> 有効期限が切れないように構成できるのは、ディレクトリ同期によって同期されていないユーザー アカウントのパスワードだけです。 ディレクトリ同期の詳細については、「[AD と Azure AD を接続する](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)」を参照してください。
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>PowerShell を使用したパスワード ポリシーの設定または確認

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストールする](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

### <a name="how-to-check-expiration-policy-for-a-password"></a>パスワードの有効期限ポリシーを確認する方法
1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーについてパスワードの有効期限が切れないかどうかを確認するには、確認するユーザーのユーザー プリンシパル名 (UPN) (例: aprilr@contoso.onmicrosoft.com) またはユーザー ID を使用して、次のコマンドレットを実行します。`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * すべてのユーザーについて「パスワードを無期限にする」設定を表示するには、次のコマンドレットを実行します。 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>パスワードを期限付きに設定する

1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを期限付きに設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 組織内のすべてのユーザーのパスワードを期限付きに設定するには、次のコマンドレットを使用します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>パスワードを無期限に設定する

1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかのコマンドを実行します。

   * 特定のユーザーのパスワードを無期限に設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 組織内のすべてのユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**クイック スタート**](active-directory-passwords-getting-started.md) - Azure AD のセルフサービスによるパスワードのリセットの管理を始めることができます。 
* [**ライセンス**](active-directory-passwords-licensing.md) - Azure AD のライセンスを構成します。
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**展開**](active-directory-passwords-best-practices.md) - ここで見つかるガイダンスを使用してユーザーに対する SSPR を計画してデプロイできます
* [**カスタマイズ**](active-directory-passwords-customize.md) - 会社の SSPR エクスペリエンスの外観をカスタマイズします。
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - どのようにですか? なぜですか? 何ですか? どこですか? 誰がですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています

