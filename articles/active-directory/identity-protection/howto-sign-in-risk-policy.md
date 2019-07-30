---
title: Azure Active Directory Identity Protection でサインイン リスク ポリシーを構成する方法 | Microsoft Docs
description: Azure AD Identity Protection サインイン リスク ポリシーを構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645e01c8ad9c620b77abd9af6cf7fe7c26ab4ea
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335409"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>方法:サインイン リスク ポリシーを構成する

Azure Active Directory で検出される[リスク イベントの種類](../reports-monitoring/concept-risk-events.md#risk-event-types)の中には、リアルタイムとオフラインがあります。 ユーザーのサインイン時に検出されたの各リスク イベントは、リスクの高いサインインと呼ばれる論理概念に関係します。 リスクの高いサインインは、サインイン試行が、ユーザー アカウントの正当な所有者によって行われていない可能性があることを示します。

## <a name="what-is-the-sign-in-risk-policy"></a>サインイン リスク ポリシーとは

Azure AD は、ユーザーの各サインインを分析します。 この分析の目的は、サインインに付随する疑わしいアクションを検出することです。 たとえば、匿名 IP アドレスを使用して実行されたサインインや、なじみのない場所から開始されたサインインがあります。 Azure AD では、システムが検出できる疑わしいアクションをリスク イベントとも呼びます。 サインイン中に検出されたリスク イベントに基づいて、Azure AD は値を計算します。 この値は、サインインが正当なユーザーによって実行されていない確率 (低、中、高) を表します。 この確率は**サインイン リスク レベル**と呼ばれます。

サインイン リスク ポリシーは、特定のサインイン リスク レベルに対して構成できる自動対応です。 この対応では、リソースへのアクセスをブロックするか、またはアクセスを取得するには多要素認証 (MFA) チャレンジの指定を要求するようにできます。
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>サインイン リスク ポリシーにアクセスする方法
   
サインイン リスク ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ページの **[構成]** セクションにあります。
   
![サインインのリスク ポリシー](./media/howto-sign-in-risk-policy/1014.png "Sign-in risk policy")

## <a name="policy-settings"></a>ポリシー設定

サインイン リスク ポリシーを構成する場合は、次を設定する必要があります。

- ポリシーの適用先のユーザーとグループ:

    ![ユーザーとグループ](./media/howto-sign-in-risk-policy/11.png)

- ポリシーをトリガーするサインイン リスク レベル:

    ![サインインのリスク レベル](./media/howto-sign-in-risk-policy/12.png)

- サインイン リスク レベルが満たされたときに適用されるアクセスの種類:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- ポリシーの状態:

    ![ポリシーを適用する](./media/howto-sign-in-risk-policy/14.png)

ポリシーの構成ダイアログには、再構成の影響を見積もるためのオプションが用意されています。

![推定される影響](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>知っておくべきこと

MFA を要求するようにサインイン リスク セキュリティ ポリシーを構成できます。

![Require MFA (MFA が必須)](./media/howto-sign-in-risk-policy/16.png)

ただし、セキュリティ上の理由から、この設定は既に MFA に登録されているユーザーに対してのみ機能します。 ID 保護では、ユーザーがまだ MFA に登録されていない場合、MFA の要件でそのユーザーをブロックします。

リスクの高いサインインに対して MFA を要求する場合は、次のことを行う必要があります。

1. 影響を受けるユーザーの[多要素認証の登録ポリシー](howto-mfa-policy.md)を有効にします。
2. 影響を受けるユーザーに対して、リスクのないセッションにサインインして MFA の登録を実行するように求めます。

上記の手順を完了すると、リスクの高いサインインには多要素認証が必要になります。

サインイン リスク ポリシーは次のように使用されます。

- 最新の認証を使用するすべてのブラウザー トラフィックとサインインに適用されます。
- ADFS などのフェデレーション IDP で WS-Trust エンドポイントを無効にすることによって従来のセキュリティ プロトコルを使用するアプリケーションには適用されません。

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

* [リスクの高いサインインの復旧](flows.md#risky-sign-in-recovery)
* [ブロックされたリスクの高いサインイン](flows.md#risky-sign-in-blocked)  
* [Azure AD Identity Protection を使用したサインイン エクスペリエンス](flows.md)  

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

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview.md)に関するページを参照してください。
