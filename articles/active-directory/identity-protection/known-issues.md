---
title: Azure Active Directory の Identity Protection (更新版) に関してよく寄せられる質問と既知の問題 | Microsoft Docs
description: Azure Active Directory の Identity Protection (更新版) に関してよく寄せられる質問と既知の問題。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d5aa50aec98b3944aed92b9da49182f0608f34c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333897"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Azure Active Directory の Identity Protection (更新版) に関してよく寄せられる質問と既知の問題

## <a name="dismiss-user-risk-known-issues"></a>"ユーザー リスクを無視する" の既知の問題

従来の Identity Protection で**ユーザー リスクを無視する**と、Identity Protection (更新版) のユーザーのリスク履歴のアクターが **Azure AD** に設定されます。

Identity Protection (更新版) で**ユーザーのリスクを無視する**と、Identity Protection (更新版) のユーザーのリスク履歴のアクターが **\<ユーザーのブレードを指すハイパーリンクが付いた管理者の名前\>** に設定されます。

現在、ユーザー リスクの無視フローに遅延が発生するという既知の問題があります。 "ユーザー リスク ポリシー" がある場合、このポリシーによって、[ユーザー リスクを無視する] をクリックしてから数分以内に、無視されたユーザーへの適用が停止されます。 ただし、無視されたユーザーの "リスク状態" が UX で更新されるまでに既知の遅延があります。 回避策としては、ブラウザー レベルでページを更新して最新のユーザーの "リスク状態" を確認します。

## <a name="risky-users-report-known-issues"></a>"危険なユーザー レポート" の既知の問題

**[ユーザー名]** フィールドに対するクエリでは大文字と小文字が区別されますが、 **[名前]** フィールドに対するクエリでは大文字と小文字が区別されません。

**[日付を次のように表示]** を切り替えると、 **[リスクの最終更新日時]** 列が非表示になります。 列を再度追加するには、[危険なユーザー] ブレードの上部にある **[列]** をクリックします。

従来の Identity Protection で**すべてのイベントを無視する**と、リスク イベントの状態が **[クローズ (解決済み)]** に設定されます。

## <a name="risky-sign-ins-report-known-issues"></a>"リスクの高いサインイン レポート" の既知の問題

リスク イベントで **[解決]** をクリックすると、状態が **[ユーザーが、リスク ベースのポリシーに要求された MFA に成功しました]** に設定されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>リスク イベントごとに独自のリスク レベルを設定できないのはなぜですか?

Identity Protection のリスク レベルは、検出の精度に基づいており、Microsoft の教師あり機械学習によって強化されています。 管理者は、ユーザー エクスペリエンスをカスタマイズするために、ユーザー リスク ポリシーとサインイン リスク ポリシーに対して特定のユーザーまたはグループを含めたり除外したりすることができます。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>サインインの場所が、ユーザーが実際にサインインした場所と一致しないのはなぜですか?

IP の地理的位置情報のマッピングについては、業界全体の課題となっています。 サインイン レポートに記載されている場所が実際の場所と一致しないと思われる場合は、サポートにご連絡ください。 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection のフィードバック メカニズムはどのように機能しますか?

**侵害ありと確認する** (サインイン時) – サインインが ID 所有者によって実行されなかったことを Azure AD Identity Protection に通知し、侵害を示します。

- このフィードバックを受けて、サインインおよびユーザー リスクの状態が **[セキュリティ侵害の確認済み]** に設定され、リスク レベルが **[高]** に設定されます。

- さらに、リスク評価における今後の改善のために、Microsoft の機械学習システムに情報が送信されます。

    > [!NOTE]
    > ユーザーが既に修復されている場合は、 **[侵害ありと確認する]** をクリックしないでください。クリックすると、サインインおよびユーザー リスクの状態が **[セキュリティ侵害の確認済み]** に設定され、リスク レベルが **[高]** に設定されるためです。

**安全と確認する** (サインイン時) – サインインが ID 所有者によって実行されたことを Azure AD Identity Protection に通知し、侵害を示しません。

- このフィードバックを受けて、(ユーザーではなく) サインイン リスクの状態が **[安全であるとの確認済み]** に設定され、リスク レベルが **-** に設定されます。

- さらに、リスク評価における今後の改善のために、Microsoft の機械学習システムに情報が送信されます。

    > [!NOTE]
    > ユーザーが侵害されていないと思われる場合は、サインイン レベルで **[安全であるとの確認済み]** を使用するのではなく、ユーザー レベルで **[ユーザー リスクを無視する]** を使用してください。 ユーザー レベルで **[ユーザー リスクを無視する]** を使用すると、ユーザーリスク、および過去に発生したリスクの高いサインインとリスク イベントがすべて閉じられます。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Identity Protection にリスクの高いサインインやリスク イベントが表示されていなくても、低 (またはそれ以上の) リスク スコアを持つユーザーが表示されるのはなぜですか?

ユーザー リスクが本質的には累積され、期限切れにならないことを考えると、直近のリスクの高いサインインやリスク イベントが Identity Protection に表示されていない場合でも、ユーザーには低またはそれ以上のユーザー リスクが設定されている可能性があります。 この状況は、リスクの高いサインインとリスク イベントの詳細が保存される期間を超えて、ユーザーに対して単一の悪意のあるアクティビティが行われた場合に発生する可能性があります。 悪意のあるアクターがお客様の環境で ID を侵害した状態で 140 日以上経過してから攻撃を開始することが知られているため、ユーザー リスクが期限切れにならないように設定されています。 お客様はユーザーのリスクのタイムラインを確認して、ユーザーがリスクにさらされている理由を判断できます。これを行うには、次のようにします。 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>サインインに関連付けられている検出のリスクが低または中の場合に、サインインの「サインイン リスク (集計)」スコアが「高」になるのはなぜですか?

集計リスク スコアが高であるのは、サインインの他の特徴、またはそのサインインに対して複数の検出が行われたという事実に基づく可能性があります。 逆に、サインインに関連付けられている検出のリスクが「高」の場合でも、サインインのサインイン リスク (集計) が「中」になることがあります。 
