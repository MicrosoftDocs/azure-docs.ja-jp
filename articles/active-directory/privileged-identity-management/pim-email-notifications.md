---
title: PIM での電子メール通知 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でのメール通知について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576949"
---
# <a name="email-notifications-in-pim"></a>PIM での電子メール通知

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、ロールが割り当てられたときや、アクティブ化されたときなど、重要なイベントの発生を知ることができます。 PIM では、自分や他の参加者に電子メール通知を送信して、最新情報を知らせることができます。 これらの電子メールには、関連するタスク (ロールのアクティブ化や更新など) へのリンクが含まれる場合もあります。 この記事では、これらの電子メールがどのように表示されるかや、それらがいつ誰に送信されるのかについて説明します。

## <a name="sender-email-address-and-subject-line"></a>送信者の電子メール アドレスと件名行

PIM から Azure AD ロールと Azure リソース ロールの両方に送信される電子メールの送信者メール アドレスは、次のアドレスになります。

- メール アドレス: **azure-noreply\@microsoft.com**
- 表示名:Microsoft Azure

これらの電子メールの件名行には、**PIM** というプレフィックスが付けられます。 次に例を示します。

- PIM:Alain Charon にバックアップ リーダー ロールが永続的に割り当てられました

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD ロール向けの PIM 電子メール

PIM では、Azure AD ロールに関する次のイベントが発生した際に、電子メールが送信されます。

- 特権ロールのアクティブ化が承認待ちのとき
- 特権ロールのアクティブ化要求が完了したとき
- Azure AD PIM が有効になったとき

Azure AD ロールに対するこれらの電子メールをどのユーザーが受信するかは、ロール、イベント、通知設定によって決まります。

| User | ロールのアクティブ化が承認待ち | ロールのアクティブ化要求が完了した | PIM が有効になった |
| --- | --- | --- | --- |
| 特権ロール管理者</br>(アクティブ化/有資格) | はい</br>(明示的な承認者が指定されていない場合のみ) | はい* | はい |
| セキュリティ管理者</br>(アクティブ化/有資格) | いいえ  | はい* | はい |
| グローバル管理者</br>(アクティブ化/有資格) | いいえ  | はい* | はい |

\* [**通知**設定](pim-how-to-change-default-settings.md#notifications)が**有資格**に設定されている場合。

ユーザーが架空の Contoso 組織の Azure AD ロールをアクティブ化したときに送信される電子メールの例を次に示します。

![Azure AD ロール向けの新しい PIM 電子メール](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Azure AD ロール向けの週間 PIM ダイジェスト電子メール

Azure AD ロール向けの週間 PIM 概要電子メールは、PIM を有効にしている特権ロール管理者、セキュリティ管理者、およびグローバル管理者に送信されます。 この週間電子メールでは、その週の PIM アクティビティのスナップショットや、特権ロールの割り当て情報が提供されます。 これは、パブリック クラウド上のテナントについてのみ利用可能です。 電子メールの例を次に示します。

![Azure AD ロール向けの週間 PIM ダイジェスト電子メール](./media/pim-email-notifications/email-directory-weekly.png)

電子メールには 4 つのタイルが含まれています。

| タイル | 説明 |
| --- | --- |
| **[Users activated]\(アクティブ化されたユーザー\)** | テナント内でユーザーの有資格ロールがアクティブ化された回数です。 |
| **[Users made permanent]\(永続化されたユーザー\)** | 有資格割り当てを持つユーザーが永続化された回数です。 |
| **[Role assignments in PIM]\(PIM でのロール割り当て\)** | PIM 内でユーザーが有資格ロールを割り当てられた回数です。 |
| **[Role assignments outside of PIM]\(PIM の外部でのロール割り当て\)** | PIM の外部 (Azure AD 内) でユーザーが永続的なロールを割り当てられた回数です。 |

**[Overview of your top roles]\(上位ロールの概要\)** セクションには、各ロールの永続管理者と有資格管理者の合計数に基づいて、上位 5 つのロールが一覧表示されます。 **[アクションの実行]** リンクをクリックすると、[PIM ウィザード](pim-security-wizard.md)が開き、そこから永続管理者を有資格管理者に一括変換することができます。

## <a name="pim-emails-for-azure-resource-roles"></a>Azure リソース ロール向けの PIM 電子メール

PIM では、Azure リソース ロールに関する次のイベントが発生した際、所有者とユーザー アクセス管理者に電子メールが送信されます。

- ロールの割り当てが承認待ちのとき
- ロールが割り当てられたとき
- ロールの期限切れが近づいているとき
- ロールが延長資格を得たとき
- エンド ユーザーによってロールが更新されたとき
- ロールのアクティブ化要求が完了したとき

PIM では、Azure リソース ロールに関する次のイベントが発生した際、エンド ユーザーに電子メールが送信されます。

- ユーザーにロールが割り当てられたとき
- ユーザーのロールが期限切れになったとき
- ユーザーのロールが延長されたとき
- ユーザーのロールのアクティブ化要求が完了したとき

ユーザーに架空の Contoso 組織の Azure リソース ロールが割り当てられたときに送信される電子メールの例を次に示します。

![Azure リソース ロール向けの新しい PIM 電子メール](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure AD ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
