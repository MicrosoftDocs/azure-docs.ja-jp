---
title: 認証方法アクティビティ - Azure Active Directory
description: サインインしてパスワードをリセットするためにユーザーが登録する認証方法の概要。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/04/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0865fb2bda04f5a7e9ba2ef73a717946fa656a5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175313"
---
# <a name="authentication-methods-activity"></a>認証方法アクティビティ 

新しい認証方法アクティビティのダッシュボードを使用すると、管理者が組織全体の認証方法の登録と使用を監視できます。 このレポート機能は、組織が登録中の方法や各方法の使用状況を確認する手段となるものです。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>アクセス許可とライセンス

以下のアクセス許可が付与されている組み込みロールとカスタム ロールを使用すると、[認証方法アクティビティ] ブレードと API にアクセスできます。

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

以下のロールには、この必要なアクセス許可が付与されています。

- レポート閲覧者
- セキュリティ閲覧者
- グローバル閲覧者
- セキュリティ オペレーター
- セキュリティ管理者
- グローバル管理者

 使用状況と分析情報にアクセスするには、Azure AD Premium P1 または P2 ライセンスが必要です。 Azure AD Multi-Factor Authentication とセルフサービス パスワード リセット (SSPR) のライセンス情報は、[Azure Active Directory の価格に関するサイト](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="how-it-works"></a>しくみ

認証方法の使用状況と分析情報にアクセスする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[認証方法]**  >  **[アクティビティ]** の順にクリックします。
1. レポートには、 **[登録]** と **[使用]** という 2 つのタブがあります。

   ![認証方法アクティビティの概要](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>登録の詳細

[ **[登録] タブ**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) にアクセスして、多要素認証、パスワードレス認証、セルフサービス パスワード リセットに対応しているユーザーの数を表示できます。 

次のいずれかのオプションをクリックして、ユーザー登録の詳細の一覧を事前にフィルター処理します。

- **[Users capable of Azure Multi-Factor Authentication]\(Azure 多要素認証に対応しているユーザー\)** には、次の両方に当てはまるユーザーの内訳が表示されます。
  - 強力な認証方法に登録されている 
  - MFA でその方法を使用するためのポリシーによって有効にされている 
  
  この数には、Azure AD 以外で MFA に登録されているユーザーは反映されていません。 
- **[Users capable of passwordless authentication]\(パスワードレス認証に対応しているユーザー\)** には、FIDO2、Windows Hello for Business、または Microsoft Authenticator アプリでのパスワードレスの電話によるサインインによって、パスワードなしでサインインするように登録されているユーザーの内訳が表示されます。 
- **[Users capable of self-service password reset]\(セルフサービス パスワード リセットに対応しているユーザー\)** には、自分でパスワードをリセットできるユーザーの内訳が表示されます。 ユーザーが自分のパスワードをリセットできるのは、次の両方に当てはまる場合です。
  - セルフサービス パスワード リセットに関する組織のポリシーを満たすのに十分な方法で登録されている 
  - パスワードのリセットが有効になっている 

  ![登録できるユーザーのスクリーンショット](media/how-to-authentication-methods-usage-insights/users-capable.png)

**[Users registered by authentication method]\(認証方法別の登録ユーザー\)** には、各認証方法に登録されているユーザーの数が表示されます。 認証方法をクリックすると、その方法に登録されているユーザーが表示されます。

![登録されているユーザーのスクリーンショット](media/how-to-authentication-methods-usage-insights/users-registered.png)

**[Recent registration by authentication method]\(認証方法別の最近の登録数\)** には、登録の成功と失敗の数が認証方法別に並べ替えられて表示されます。 認証方法をクリックすると、その方法の最近の登録イベントが表示されます。

![最近の登録のスクリーンショット](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Usage details

**[使用]** レポートには、パスワードのサインインとリセットに使用される認証方法が表示されます。

![[使用] ページのスクリーンショット](media/how-to-authentication-methods-usage-insights/usage-page.png)

**[Sign-ins by authentication requirement]\(認証要件別のサインイン\)** には、Azure AD で単一要素の、または多要素認証に必要だった、ユーザー対話型サインインの成功数が表示されます。 サードパーティーの MFA プロバイダーによって MFA が適用されたサインインは含まれません。

![認証要件別のサインインのスクリーンショット](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**[Sign-ins by authentication method]\(認証方法別のサインイン\)** には、ユーザー対話型サインイン (成功と失敗) の数が、使用された認証方法別に表示されます。 トークン内の要求によって認証要件が満たされたサインインは含まれません。

![方法別のサインインのスクリーンショット](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**[Number of password resets and account unlocks]\(パスワードのリセットとアカウントのロック解除の数\)** には、パスワードの変更の成功とパスワードのリセット (セルフサービスと管理者) の数が時間の経過と共に表示されます。

![リセットとロック解除のスクリーンショット](media/how-to-authentication-methods-usage-insights/password-changes.png)

**[Password resets by authentication method]\(認証方法別のパスワードのリセット\)** には、パスワードのリセットのフロー中に認証が成功した数と失敗した数が、認証方法別に表示されます。

![方法別のリセットのスクリーンショット](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>ユーザー登録の詳細 

一覧の一番上にあるコントロールを使うと、特定のユーザーを検索したり、表示されている列に基づいてユーザーの一覧を絞り込んだりできます。

登録の詳細レポートには、ユーザーごとに次の情報が表示されます。

- ユーザー プリンシパル名
- 名前
- MFA 対応 (対応、非対応)
- パスワードレス対応 (対応、非対応)
- SSPR 登録済み (登録済み、未登録)
- SSPR 有効 (有効、有効でない)
- SSPR 対応 (対応、非対応) 
- 登録された方法 (メールアドレス、携帯電話、代替携帯電話、会社電話、Microsoft Authenticator プッシュ、ソフトウェア ワン タイム パスコード、FIDO2、セキュリティ キー、セキュリティの質問)

  ![ユーザー登録の詳細のスクリーンショット](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>登録とリセットのイベント 

**[Registration and reset events]\(登録とリセットのイベント\)** には、次のような、過去 24 時間、過去 7 日間、または過去 30 日間の登録とリセットのイベントが表示されます。

- Date
- ユーザー名
- User 
- 特徴 (登録、リセット)
- 使用された方法 (アプリ通知、アプリ コード、電話、会社電話、代替携帯電話による通話、SMS、メール、セキュリティの質問)
- 状態 (成功、失敗)
- 失敗の理由 (説明)

  ![登録とリセット イベントのスクリーンショット](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>制限事項

- レポート内のデータはリアルタイムで更新されておらず、反映されるまでに数時間の待機時間が発生する場合があります。
- 一時アクセス パスの登録は、短時間のみ有効であるため、レポートの [登録] タブには反映されません。

## <a name="next-steps"></a>次のステップ

- [認証方法の使用状況レポート API を使用する](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [組織の認証方法を選択する](concept-authentication-methods.md)
- [統合された登録エクスペリエンス](concept-registration-mfa-sspr-combined.md)
