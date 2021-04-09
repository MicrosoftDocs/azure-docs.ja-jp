---
title: Azure Active Directory の条件付きアクセスとは
description: 新しい ID ドリブン コントロール プレーンの中心になる条件付きアクセスについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 01/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 7b43cf23b3f01e64c0be31ed013aa990ff80d3db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938344"
---
# <a name="what-is-conditional-access"></a>条件付きアクセスとは

現在のセキュリティ境界は組織のネットワークの外にまで広がり、ユーザーとデバイスの ID が境界の中に含まれるようになっています。 組織では、アクセス制御に関する決定を行う過程で、これらの ID シグナルを利用できます。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MwZs]

条件付きアクセスは、Azure Active Directory で使用されるツールです。このツールによって、シグナルをまとめて、決定を行い、組織のポリシーを適用することができます。 条件付きアクセスは、新しい ID ドリブン コントロール プレーンの中心になるものです。

![概念的な条件付きシグナルと適用決定](./media/overview/conditional-access-signal-decision-enforcement.png)

条件付きアクセス ポリシーは、簡単に言えば、ユーザーがリソースにアクセスする場合、ユーザーはアクションを完了する必要があるという if-then ステートメントです。 例:給与管理者は、給与処理アプリケーションにアクセスする必要があります。アクセスには多要素認証を実行することが要求されます。

管理者が直面している 2 つの主な目標は次のとおりです。

- 場所や時間を問わず、常にユーザーの生産性を高められるようにすること
- 組織の資産を保護すること

条件付きアクセス ポリシーを使用すると、必要な場合は適切なアクセス制御を適用して組織のセキュリティを維持し、必要でない場合はユーザーの邪魔にならないようにすることができます。

![概念的な条件付きアクセスのプロセス フロー](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> 条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 条件付きアクセスはサービス拒否 (DoS) 攻撃などのシナリオに対する組織の防御の最前線を意図したものではありませんが、これらのイベントからのシグナルを使用してアクセス権を判定できます。

## <a name="common-signals"></a>一般的なシグナル

ポリシーに基づく決定を行うときに、条件付きアクセスで考慮することができる一般的なシグナルには、次のようなシグナルがあります。

- ユーザーまたはグループ メンバーシップ
   - 特定のユーザーとグループをポリシーの対象にすることができるため、管理者はアクセスをきめ細かく制御できます。
- IP の場所に関する情報
   - 組織では、ポリシーに基づく決定を行うときに使用できる、信頼された IP アドレス範囲を作成できます。 
   - 管理者は、国/地域全体の IP 範囲を指定して、その範囲からのトラフィックをブロックまたは許可することができます。
- Device
   - 条件付きアクセス ポリシーを適用するとき、特定のプラットフォームのデバイスまたは特定の状態であるとマークされたデバイスを使用しているユーザーを使用できます。
- Application
   - 特定のアプリケーションにアクセスしようとするユーザーは、さまざまな条件付きアクセス ポリシーをトリガーできます。 
- リアルタイムでの計算されたリスクの検出
   - シグナルと Azure AD Identity Protection の統合により、条件付きアクセス ポリシーで危険なサインイン動作を特定できます。 その上で、ポリシーでユーザーにパスワード変更や多要素認証の実行を強制してリスク レベルを下げることや、管理者が手動で対処するまでユーザーのアクセスをブロックすることができます。
- Microsoft Cloud App Security (MCAS)
   - ユーザーのアプリケーションへのアクセスとセッションをリアルタイムで監視および制御できるようにします。クラウド環境へのアクセスと、クラウド環境で実行されるアクティビティの可視性を高めて制御を強化できます。

## <a name="common-decisions"></a>一般的な決定

- アクセスのブロック
   - 最も制限の厳しい決定
- アクセス権の付与
   - 最も制限が弱い決定でも、次のオプションのうち 1 つ以上を要求することができます。
      - 多要素認証が必要です
      - デバイスは準拠としてマーク済みである必要がある
      - ハイブリッド Azure AD 参加済みのデバイスを必要とする
      - 承認済みクライアント アプリを必須にする
      - アプリの保護ポリシーが必要 (プレビュー)

## <a name="commonly-applied-policies"></a>一般的に適用されるポリシー

多くの組織には、次のような[一般的なアクセスの問題があり、それらに対して条件付きアクセス ポリシーが役に立ちます](concept-conditional-access-policy-common.md)。

- 管理者の役割を持つユーザーに多要素認証を要求する
- Azure 管理タスクに対して多要素認証を要求する
- レガシ認証プロトコルを使用しようとしているユーザーのサインインをブロックする
- Azure AD Multi-Factor Authentication の登録に信頼できる場所を要求する
- 特定の場所からのアクセスをブロックまたは許可する
- リスクの高いサインイン動作をブロックする
- 特定のアプリケーションに対して、組織のマネージド デバイスを必要とする

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

[Microsoft 365 Business Premium ライセンス](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。 

[サインイン リスク](concept-conditional-access-conditions.md#sign-in-risk)には、[ID 保護](../identity-protection/overview-identity-protection.md)が必要です。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス ポリシーを 1 つずつ作成する](concept-conditional-access-policies.md)
- [条件付きアクセスのデプロイを計画する](plan-conditional-access.md)
- [Identity Protection について学ぶ](../identity-protection/overview-identity-protection.md)
- [Microsoft Cloud App Security について学ぶ](/cloud-app-security/what-is-cloud-app-security)
- [Microsoft Intune について学ぶ](/intune/index)
