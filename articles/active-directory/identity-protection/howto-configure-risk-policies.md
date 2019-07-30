---
title: Azure Active Directory Identity Protection (更新版) でのリスク ポリシーの構成方法 | Microsoft Docs
description: Azure Active Directory Identity Protection (更新版) でのリスク ポリシーの構成方法。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334030"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>方法:Azure Active Directory Identity Protection (更新版) でのリスク ポリシーの構成

Azure AD は、ID が侵害されている可能性の指標となるリスク イベントを検出します。 リスク ポリシーを構成することにより、以下のように検出結果に対して自動応答を定義できます。

- サインイン リスク ポリシーにより、ユーザーのサインイン中に検出されたリアルタイム リスク イベントに対する応答を構成できます。 
- ユーザー リスク ポリシーにより、あるユーザーについて一定期間にわたって検出されたすべてのアクティブ ユーザー リスクに対する応答を構成できます。  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>サインイン リスク ポリシーとは

Azure AD は、ユーザーの各サインインを分析します。 この分析の目的は、サインインに付随する疑わしいアクションを検出することです。 たとえば、匿名 IP アドレスを使用して実行されたサインインや、なじみのない場所から開始されたサインインがあります。 Azure AD では、システムが検出できる疑わしいアクションをリスク イベントとも呼びます。 サインイン中に検出されたリスク イベントに基づいて、Azure AD は値を計算します。 この値は、サインインが正当なユーザーによって実行されていない確率 (低、中、高) を表します。 この確率は**サインイン リスク レベル**と呼ばれます。

サインイン リスク ポリシーは、特定のサインイン リスク レベルに対して構成できる自動対応です。 この対応では、リソースへのアクセスをブロックするか、またはアクセスを取得するには多要素認証 (MFA) チャレンジの指定を要求するようにできます。

サインイン リスク ポリシーによってトリガーされた MFA プロンプトをユーザーが正しく遂行すると、正当なユーザーによってサインインが行われたというフィードバックが Identity Protection に送られます。 その後、MFA プロンプトをトリガーしたサインイン リスク イベントは自動的に終了し、このイベントがユーザー リスクの昇格につながらないよう、Identity Protection によって設定されます。 サインイン リスク ポリシーを有効にすると、MFA のダイアログが表示されたときにユーザーが自身で修正し、その後、関連する危険なサインインは自動的にクローズされるため、"危険なサインイン" ビューの表示件数を減らすことができます。

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>サインイン リスク ポリシーにアクセスする方法
   
サインイン リスク ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ページの **[構成]** セクションにあります。
   
![サインインのリスク ポリシー](./media/howto-configure-risk-policies/1014.png "Sign-in risk policy")

## <a name="sign-in-risk-policy-settings"></a>サインイン リスク ポリシーの設定

サインイン リスク ポリシーを構成する場合は、次を設定する必要があります。

- ポリシーの適用先のユーザーとグループ:

   ![ユーザーとグループ](./media/howto-configure-risk-policies/11.png)

- ポリシーをトリガーするサインイン リスク レベル:

   ![サインインのリスク レベル](./media/howto-configure-risk-policies/12.png)

- サインイン リスク レベルが満たされたときに適用されるアクセスの種類:  

   ![Access](./media/howto-configure-risk-policies/13.png)

- ポリシーの状態:

   ![ポリシーを適用する](./media/howto-configure-risk-policies/14.png)

ポリシーの構成ダイアログには、再構成の影響を見積もるためのオプションが用意されています。

![推定される影響](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>サインイン リスク ポリシーについて知っておくべきこと

MFA を要求するようにサインイン リスク セキュリティ ポリシーを構成できます。

![Require MFA (MFA が必須)](./media/howto-configure-risk-policies/16.png)

ただし、セキュリティ上の理由から、この設定は既に MFA に登録されているユーザーに対してのみ機能します。 ID 保護では、ユーザーがまだ MFA に登録されていない場合、MFA の要件でそのユーザーをブロックします。

リスクの高いサインインに対して MFA を要求する場合は、次のことを行う必要があります。

1. 影響を受けるユーザーの多要素認証の登録ポリシーを有効にします。
2. 影響を受けるユーザーに対して、リスクのないセッションにログインして MFA 登録を実行するように求めます。

上記の手順を完了すると、リスクの高いサインインには多要素認証が必要になります。

サインイン リスク ポリシーは次のように使用されます。

- 最新の認証を使用するすべてのブラウザー トラフィックとサインインに適用されます。
- ADFS などのフェデレーション IDP で WS-Trust エンドポイントを無効にすることによって従来のセキュリティ プロトコルを使用するアプリケーションには適用されません。

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

* [リスクの高いサインインの復旧](flows.md#risky-sign-in-recovery)
* [ブロックされたリスクの高いサインイン](flows.md#risky-sign-in-blocked)  
* [Azure AD Identity Protection を使用したサインイン エクスペリエンス](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>ユーザー リスク ポリシーとは

Azure AD は、ユーザーの各サインインを分析します。 この分析の目的は、サインインに付随する疑わしいアクションを検出することです。 Azure AD では、システムが検出できる疑わしいアクションをリスク イベントとも呼びます。 一部のリスク イベントはリアルタイムに検出できますが、より長い時間を必要とするリスク イベントも存在します。 たとえば、異常な場所へのあり得ない移動を検出するには、システムにユーザーの通常の動作を学習するための 14 日間の初期の学習期間が必要になります。 検出されたリスク イベントを解決するためのオプションがいくつか存在します。 たとえば、個々のリスク イベントを手動で解決することも、サインイン リスクまたはユーザー リスクの条件付きアクセス ポリシーを使用して解決されるようにすることもできます。

ユーザーに対して検出され、まだ解決されていないリスク イベントはすべて、アクティブなリスク イベントと呼ばれます。 ユーザーに関連付けられたアクティブなリスク イベントは、ユーザー リスクと呼ばれます。 そのユーザー リスクに基づいて、Azure AD はユーザーが侵害されている確率 (低、中、高) を計算します。 この確率はユーザー リスク レベルと呼ばれます。

![ユーザーのリスク](./media/howto-configure-risk-policies/11031.png)

ユーザー リスク ポリシーは、特定のユーザー リスク レベルに対して構成できる自動対応です。 ユーザー リスク ポリシーでは、リソースへのアクセスをブロックするか、またはユーザー アカウントをクリーンな状態に戻すにはパスワード変更を要求するようにできます。

## <a name="how-do-i-access-the-user-risk-policy"></a>ユーザー リスク ポリシーにどのようにアクセスするか
   
ユーザー リスク ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ぺージの **[構成]** セクションにあります。
   
![ユーザー リスクのポリシー](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>ユーザー リスク ポリシーの設定

ユーザー リスク ポリシーを構成する場合は、次を設定する必要があります。

- ポリシーの適用先のユーザーとグループ:

   ![ユーザーとグループ](./media/howto-configure-risk-policies/111.png)

- ポリシーをトリガーするサインイン リスク レベル:

   ![ユーザーのリスク レベル](./media/howto-configure-risk-policies/112.png)

- サインイン リスク レベルが満たされたときに適用されるアクセスの種類:  

   ![Access](./media/howto-configure-risk-policies/113.png)

- ポリシーの状態:

   ![ポリシーを適用する](./media/howto-configure-risk-policies/114.png)

ポリシーの構成ダイアログには、構成の影響を見積もるためのオプションが用意されています。

![推定される影響](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>ユーザー リスク ポリシーについて知っておくべきこと

ユーザー リスク セキュリティ ポリシーを設定すると、リスク レベルに応じてサインイン時にユーザーをブロックできます。

![ブロック](./media/howto-configure-risk-policies/116.png)

サインインをブロックすると、以下のことが行われます。

* 影響を受けるユーザーに対して新しいユーザー リスク イベントが生成されなくなります。
* 管理者は、ユーザーの ID に影響を与えるリスク イベントを手動で修復して、セキュリティで保護された状態に復元できます。

## <a name="best-practices"></a>ベスト プラクティス

**高** しきい値を選択すると、ポリシーがトリガーされる回数が減り、ユーザーへの影響が最小限になります。  

ただし、**低**および**中**レベルのリスクのフラグ付きサインインはポリシーから除外されるため、攻撃者が侵害された ID を悪用するのをブロックすることはできません。

ポリシーを設定するときは次のようにします。

- 多要素認証を使用しない/できないユーザーを除外します。
- ポリシーを有効にするのが実際的でないロケールのユーザー (ヘルプデスクにアクセスできないユーザーなど) を除外します。
- 多数の誤検知を生成する可能性があるユーザー (開発者、セキュリティ アナリスト) を除外します。
- 初期のポリシー展開中、またはエンド ユーザーに表示されるチャレンジを最小限に抑える必要がある場合は、 **[高]** しきい値を使用します。
- 組織のセキュリティを強化する必要がある場合は、**低**しきい値を使用します。 **低** しきい値を選択すると、追加のユーザー サインイン チャレンジが導入されますが、セキュリティは強化されます。

ほとんどの組織に対する既定の設定として、 **中** しきい値の規則を構成し、使いやすさとセキュリティのバランスを取ることをお勧めします。

## <a name="next-steps"></a>次の手順

 [Channel 9:Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
