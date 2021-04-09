---
title: PIM での電子メール通知 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でのメール通知について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394732"
---
# <a name="email-notifications-in-pim"></a>PIM での電子メール通知

Privileged Identity Management (PIM) を使用すると、Azure Active Directory (Azure AD) 組織で、ロールが割り当てられたり、アクティブ化されたりした場合などの重要なイベントがいつ発生したかを知ることができます。 Privileged Identity Management では、自分やその他の参加者に電子メール通知が送信されるため、常に最新情報を取得できます。 これらの電子メールには、関連するタスク (ロールのアクティブ化や更新など) へのリンクが含まれる場合もあります。 この記事では、これらの電子メールがどのように表示されるかや、それらがいつ誰に送信されるのかについて説明します。

## <a name="sender-email-address-and-subject-line"></a>送信者の電子メール アドレスと件名行

Azure AD ロールと Azure リソース ロールの両方に関して Privileged Identity Management から送信される電子メールには、次の送信者メール アドレスが含まれています。

- メール アドレス: **azure-noreply\@microsoft.com**
- 表示名:Microsoft Azure

これらの電子メールの件名行には、**PIM** というプレフィックスが付けられます。 次に例を示します。

- PIM:Alain Charon にバックアップ リーダー ロールが永続的に割り当てられました

## <a name="notifications-for-azure-ad-roles"></a>Azure AD ロールに関する通知

Privileged Identity Management は、Azure AD ロールに関して次のイベントが発生したときに電子メールを送信します。

- 特権ロールのアクティブ化が承認待ちのとき
- 特権ロールのアクティブ化要求が完了したとき
- Azure AD Privileged Identity Management が有効になったとき

Azure AD ロールに対するこれらの電子メールをどのユーザーが受信するかは、ロール、イベント、通知設定によって決まります。

| User | ロールのアクティブ化が承認待ち | ロールのアクティブ化要求が完了した | PIM が有効になった |
| --- | --- | --- | --- |
| 特権ロール管理者</br>(アクティブ化/有資格) | はい</br>(明示的な承認者が指定されていない場合のみ) | はい* | はい |
| セキュリティ管理者</br>(アクティブ化/有資格) | いいえ | はい* | はい |
| グローバル管理者</br>(アクティブ化/有資格) | いいえ | はい* | はい |

\*[**通知** 設定](pim-how-to-change-default-settings.md#notifications)が **有資格** に設定されている場合。

ユーザーが架空の Contoso 組織の Azure AD ロールをアクティブ化したときに送信される電子メールの例を次に示します。

![Azure AD ロールに関する新しい Privileged Identity Management 電子メール](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD ロールに関する週間 Privileged Identity Management ダイジェスト電子メール

Azure AD ロールに関する週間 Privileged Identity Management サマリー電子メールは、Privileged Identity Management が有効になっている特権ロール管理者、セキュリティ管理者、およびグローバル管理者に送信されます。 この週間電子メールは、その週の Privileged Identity Management アクティビティのスナップショットのほか、特権ロールの割り当てを提供します。 これは、パブリック クラウド上の Azure AD 組織についてのみ使用できます。 電子メールの例を次に示します。

![Azure AD ロールに関する週間 Privileged Identity Management ダイジェスト電子メール](./media/pim-email-notifications/email-directory-weekly.png)

電子メールには 4 つのタイルが含まれています。

| タイル | 説明 |
| --- | --- |
| **[Users activated]\(アクティブ化されたユーザー\)** | 組織内でユーザーの有資格ロールがアクティブ化された回数です。 |
| **[Users made permanent]\(永続化されたユーザー\)** | 有資格割り当てを持つユーザーが永続化された回数です。 |
| **Privileged Identity Management でのロールの割り当て** | Privileged Identity Management の内部でユーザーに資格のあるロールが割り当てられた回数。 |
| **[Role assignments outside of PIM]\(PIM の外部でのロール割り当て\)** | Privileged Identity Management の外部 (Azure AD の内部) でユーザーに永続的なロールが割り当てられた回数。 |

**[Overview of your top roles]\(上位ロールの概要\)** セクションには、各ロールの永続管理者と有資格管理者の合計数に基づいて、組織内の上位 5 つのロールが一覧表示されます。 **[アクションの実行]** リンクをクリックすると、[PIM ウィザード](pim-security-wizard.md)が開き、そこから永続管理者を有資格管理者に一括変換することができます。

## <a name="email-timing-for-activation-approvals"></a>アクティブ化承認の電子メールのタイミング

ユーザーが各自のロールをアクティブにし、ロールの設定で承認が必要な場合、承認者は承認ごとに次の 2 通の電子メールを受け取ります。

- ユーザーのアクティブ化要求を承認または拒否する要求 (要求承認エンジンによって送信されます)
- ユーザーの要求が承認された (要求承認エンジンによって送信されます)

また、グローバル管理者と特権ロール管理者は、各承認で次の電子メールを受信します。

- ユーザーのロールがアクティブ化された (Privileged Identity Management によって送信されます)

要求承認エンジンによって送信される最初の 2 つの電子メールは、遅延する場合があります。 現在、電子メールの 90% は 3 分から 10 分かかりますが、1% のお客様については、最大で 15 分ほどになる可能性があります。

最初の電子メールが送信される前に Azure portal で承認要求が承認されると、最初の電子メールはトリガーされなくなり、他の承認者が承認要求の電子メールで通知されることはありません。 他の承認者に電子メールが届かないように見えるかもしれませんが、想定されている動作です。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure リソース ロール向けの PIM 電子メール

Privileged Identity Management は、Azure リソース ロールに関して次のイベントが発生したときに所有者とユーザー アクセス管理者に電子メールを送信します。

- ロールの割り当てが承認待ちのとき
- ロールが割り当てられたとき
- ロールの期限切れが近づいているとき
- ロールが延長資格を得たとき
- エンド ユーザーによってロールが更新されたとき
- ロールのアクティブ化要求が完了したとき

Privileged Identity Management は、Azure リソース ロールに関して次のイベントが発生したときにエンド ユーザーに電子メールを送信します。

- ユーザーにロールが割り当てられたとき
- ユーザーのロールが期限切れになったとき
- ユーザーのロールが延長されたとき
- ユーザーのロールのアクティブ化要求が完了したとき

ユーザーに架空の Contoso 組織の Azure リソース ロールが割り当てられたときに送信される電子メールの例を次に示します。

![Azure リソース ロールに関する新しい Privileged Identity Management 電子メール](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
