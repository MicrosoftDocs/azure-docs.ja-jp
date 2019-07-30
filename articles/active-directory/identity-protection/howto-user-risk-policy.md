---
title: Azure Active Directory Identity Protection でユーザー リスク ポリシーを構成する方法 | Microsoft Docs
description: Azure AD Identity Protection ユーザー リスク ポリシーを構成する方法について説明します。
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
ms.openlocfilehash: fc7ea05497d69a7ca833cc783e7a2bc6bf1a8b07
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335445"
---
# <a name="how-to-configure-the-user-risk-policy"></a>方法:ユーザー リスク ポリシーを構成する

ユーザー リスクでは、Azure AD はユーザー アカウントが侵害されている確率を検出します。 管理者は、特定のユーザー リスク レベルに自動的に対応するために、ユーザー リスクの条件付きアクセス ポリシーを構成できます。
 
この記事では、ユーザー リスク ポリシーを構成するために必要な情報を提供します。

## <a name="what-is-a-user-risk-policy"></a>ユーザー リスク ポリシーとは

Azure AD は、ユーザーの各サインインを分析します。 この分析の目的は、サインインに付随する疑わしいアクションを検出することです。 Azure AD では、システムが検出できる疑わしいアクションをリスク イベントとも呼びます。 一部のリスク イベントはリアルタイムに検出できますが、より長い時間を必要とするリスク イベントも存在します。 たとえば、異常な場所へのあり得ない移動を検出するには、システムにユーザーの通常の動作を学習するための 14 日間の初期の学習期間が必要になります。 検出されたリスク イベントを解決するためのオプションがいくつか存在します。 たとえば、個々のリスク イベントを手動で解決することも、サインイン リスクまたはユーザー リスクの条件付きアクセス ポリシーを使用して解決されるようにすることもできます。

ユーザーに対して検出され、まだ解決されていないリスク イベントはすべて、アクティブなリスク イベントと呼ばれます。 ユーザーに関連付けられたアクティブなリスク イベントは、ユーザー リスクと呼ばれます。 そのユーザー リスクに基づいて、Azure AD はユーザーが侵害されている確率 (低、中、高) を計算します。 この確率はユーザー リスク レベルと呼ばれます。

![ユーザーのリスク](./media/howto-user-risk-policy/1031.png)

ユーザー リスク ポリシーは、特定のユーザー リスク レベルに対して構成できる自動対応です。 ユーザー リスク ポリシーでは、リソースへのアクセスをブロックするか、またはユーザー アカウントをクリーンな状態に戻すにはパスワード変更を要求するようにできます。

## <a name="how-do-i-access-the-user-risk-policy"></a>ユーザー リスク ポリシーにどのようにアクセスするか
   
サインイン リスク ポリシーは、[[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) ページの **[構成]** セクションにあります。
   
![ユーザー リスクのポリシー](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>ポリシー設定

サインイン リスク ポリシーを構成する場合は、次を設定する必要があります。

- ポリシーの適用先のユーザーとグループ:

    ![ユーザーとグループ](./media/howto-user-risk-policy/11.png)

- ポリシーをトリガーするサインイン リスク レベル:

    ![ユーザーのリスク レベル](./media/howto-user-risk-policy/12.png)

- サインイン リスク レベルが満たされたときに適用されるアクセスの種類:  

    ![Access](./media/howto-user-risk-policy/13.png)

- ポリシーの状態:

    ![ポリシーを適用する](./media/howto-user-risk-policy/14.png)

ポリシーの構成ダイアログには、構成の影響を見積もるためのオプションが用意されています。

![推定される影響](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>知っておくべきこと

ユーザー リスク セキュリティ ポリシーを設定すると、リスク レベルに応じてサインイン時にユーザーをブロックできます。

![ブロック](./media/howto-user-risk-policy/16.png)

サインインをブロックすると、以下のことが行われます。

* 影響を受けるユーザーに対して新しいユーザー リスク イベントが生成されなくなります。
* 管理者は、ユーザーの ID に影響を与えるリスク イベントを手動で修復して、セキュリティで保護された状態に復元できます。

## <a name="best-practices"></a>ベスト プラクティス

**高** しきい値を選択すると、ポリシーがトリガーされる回数が減り、ユーザーへの影響が最小限になります。
ただし、**低**および**中**レベルのリスクのフラグ付きユーザーはポリシーから除外されるため、以前に疑いのあった、または侵害されたことが知られていた、ID またはデバイスをセキュリティで保護することはできません。

ポリシーを設定するときは次のようにします。

* 多数の誤検知を生成する可能性があるユーザー (開発者、セキュリティ アナリスト) を除外します。
* ポリシーを有効にするのが実際的でないロケールのユーザー (ヘルプデスクにアクセスできないユーザーなど) を除外します。
* ポリシーの初期展開中、またはエンド ユーザーに表示されるチャレンジを最小限に抑える必要がある場合は、 **高** しきい値を使用します。
* 組織のセキュリティを強化する必要がある場合は、 **低** しきい値を使用します。 **低** しきい値を選択すると、追加のユーザー サインイン チャレンジが導入されますが、セキュリティは強化されます。

ほとんどの組織に対する既定の設定として、 **中** しきい値の規則を構成し、使いやすさとセキュリティのバランスを取ることをお勧めします。

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

* [侵害されたアカウントの復旧フロー](flows.md#compromised-account-recovery)。  
* [ブロック済みの侵害されたアカウントのフロー](flows.md#compromised-account-blocked)。  

**関連する構成ダイアログ ボックスを開くには**:

- **[Azure AD Identity Protection]** ブレードの **[構成]** セクションで **[ユーザーのリスク ポリシー]** をクリックします。

    ![ユーザーのリスク ポリシー](./media/howto-user-risk-policy/1009.png "User risk policy")

## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview.md)に関するページを参照してください。
