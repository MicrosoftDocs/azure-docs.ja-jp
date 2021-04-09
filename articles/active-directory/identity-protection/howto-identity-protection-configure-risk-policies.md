---
title: リスク ポリシー - Azure Active Directory Identity Protection
description: Azure Active Directory Identity Protection でリスク ポリシーを構成して有効にします
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997632"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>方法:リスク ポリシーを構成して有効にする

前の記事「[Identity Protection ポリシー](concept-identity-protection-policies.md)」で説明したように、ディレクトリで有効にできるリスク ポリシーは 2 つあります。 

- サインインのリスク ポリシー
- ユーザー リスクのポリシー

![ユーザー リスク ポリシーとサインイン リスク ポリシーを有効にするためのセキュリティの [概要] ページ](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

どちらのポリシーも、環境内のリスク検出に対する応答を自動化し、リスクが検出されたときにユーザーが自己修復できるようにします。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>前提条件 

リスクが検出されたときにユーザーが自己修復を実行できるようにする場合は、セルフサービス パスワード リセット と Azure AD Multi-Factor Authentication の両方にユーザーが登録されている必要があります。 最適なエクスペリエンスを実現するには、[統合されたセキュリティ情報の登録エクスペリエンス](../authentication/howto-registration-mfa-sspr-combined.md)を有効にすることをお勧めします。 ユーザーに自己修復を許可すると、管理者の介入を必要とせずに、生産的な状態に迅速に戻ることができます。 管理者は、事後にこれらのイベントを表示して調査することができます。 

## <a name="choosing-acceptable-risk-levels"></a>許容されるリスク レベルの選択

組織は、ユーザー エクスペリエンスとセキュリティ体制のバランスを考慮しつつ、受け入れることができるリスクのレベルを決定する必要があります。 

Microsoft で推奨しているのは、ユーザー リスク ポリシーのしきい値を **[高]** に設定し、サインイン リスク ポリシーを **[中以上]** に設定することです。

**高** しきい値を選択すると、ポリシーがトリガーされる回数が減り、ユーザーへの影響が最小限になります。 ただし、 **[低]** と **[中]** のリスク検出はポリシーから除外されるため、攻撃者が侵害された ID を悪用するのをブロックすることができない可能性があります。 **[低]** しきい値を選択すると、ユーザーによる割り込みの処理が増加しますが、セキュリティ体制は強化されます。

## <a name="exclusions"></a>除外

すべてのポリシーで、[緊急時の管理者アカウント](../roles/security-emergency-access.md)などのユーザーを除外できます。 組織は、アカウントの使用方法に基づいて、特定のポリシーから他のアカウントを除外する必要があると判断できます。 すべての除外を定期的に見直して、適用を続行するかどうかを確認する必要があります。

Identity Protection で構成済みのセキュリティで保護された[ネットワークの場所](../conditional-access/location-condition.md)を使用することで、リスクを検出して偽陽性を減らすことができます。

## <a name="enable-policies"></a>ポリシーを有効にする

ユーザー リスクとサインイン リスク ポリシーを有効にするには、次の手順を完了します。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[概要]** を参照します。
1. **[ユーザー リスク ポリシー]** を選択します。
   1. **[割り当て]** で:
      1. **[ユーザー]** - **[すべてのユーザー]** を選択します。ロールアウトを制限する場合は **[個人と グループの選択]** を選択します。
         1. 必要に応じて、ポリシーからユーザーを除外できます。
      1. **[条件]**  -  **[ユーザー リスク]** 。Microsoft で推奨しているのは、このオプションを **[高]** に設定することです。
   1. **[コントロール]** で:
      1. **[アクセス]** - Microsoft で推奨しているのは、 **[アクセスを許可]** と **[パスワードの変更を必須とする]** です。
   1. **[ポリシーの適用]**  -  **[オン]**
   1. **[保存]** - この操作を行うと、 **[概要]** ページに戻ります。
1. **[サインインのリスク ポリシー]** を選択します。
   1. **[割り当て]** で:
      1. **[ユーザー]** - **[すべてのユーザー]** を選択します。ロールアウトを制限する場合は **[個人と グループの選択]** を選択します。
         1. 必要に応じて、ポリシーからユーザーを除外できます。
      1. **[条件]**  -  **[サインイン リスク]** 。Microsoft で推奨しているのは、このオプションを **[中以上]** に設定することです。
   1. **[コントロール]** で:
      1. **[アクセス]** - Microsoft で推奨しているのは、 **[アクセスを許可]** と **[多要素認証が必要です]** を設定することです。
   1. **[ポリシーの適用]**  -  **[オン]**
   1. **および**

## <a name="next-steps"></a>次のステップ

- [Azure AD Multi-Factor Authentication 登録ポリシーを有効にする](howto-identity-protection-configure-mfa-policy.md)

- [リスクとは](concept-identity-protection-risks.md)

- [リスク検出を調査する](howto-identity-protection-investigate-risk.md)

- [リスク検出をシミュレートする](howto-identity-protection-simulate-risk.md)
