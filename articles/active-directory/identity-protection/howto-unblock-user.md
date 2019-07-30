---
title: Azure Active Directory Identity Protection を使用してユーザーのブロックを解除する方法 | Microsoft Docs
description: Azure Active Directory Identity Protection ポリシーによってブロックされたユーザーのブロックを解除する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28e30b8e44b6888cdb7416b9c7b563b955a68ce
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335382"
---
# <a name="how-to-unblock-users"></a>方法:ユーザーのブロックを解除する

Azure Active Directory Identity Protection では、構成されている条件が満たされた場合にユーザーをブロックするポリシーを構成できます。 通常、ブロックされたユーザーは、ブロックを状態した状態になるようにヘルプ デスクに連絡します。 この記事では、ブロックされたユーザーのブロックを解除するための手順について説明します。

## <a name="determine-the-reason-for-blocking"></a>ブロックの理由を特定する

ユーザーのブロックを解除する最初の手順として、ユーザーをブロックしたポリシーの種類を特定する必要があります。これに応じて、次の手順が異なります。
Azure Active Directory Identity Protection を使用すると、ユーザーをサインイン リスク ポリシーまたはユーザー リスク ポリシーによってブロックすることができます。

ユーザーをブロックしたポリシーの種類は、サインインの試行中にユーザーに表示されたダイアログ ボックスの見出しからわかります。

| ポリシー | ユーザー ダイアログ |
| --- | --- |
| サインイン リスク |![Blocked sign-in](./media/howto-unblock-user/02.png) |
| ユーザー リスク |![Blocked account](./media/howto-unblock-user/104.png) |

ブロックされているユーザーは次のとおりです:

* サインイン リスク ポリシーによってブロックされたユーザーは、不審なサインインとも呼ばれます。
* ユーザー リスク ポリシーによってブロックされたユーザーは、危険にさらされたアカウントとも呼ばれます。

## <a name="unblocking-suspicious-sign-ins"></a>不審なサインインのブロック解除

不審なサインインのブロックを解除するには、次の方法があります。

1. **よく使用する場所やデバイスからサインインする** - 不審なサインインがブロックされる一般的な理由は、使用頻度の低い場所やデバイスからのサインイン試行にあります。 ユーザーは、よく使用する場所やデバイスからのサインインを試みることで、これがブロックの理由であるかどうかをすぐに確認できます。
2. **ポリシーから除外する** - サインイン ポリシーの現在の構成が原因で特定のユーザーに問題が発生していると考えられる場合は、そのポリシーからユーザーを除外できます。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。
3. **ポリシーを無効にする** - ポリシーの構成が原因ですべてのユーザーに問題が発生していると考えられる場合は、ポリシーを無効にすることができます。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。

## <a name="unblocking-accounts-at-risk"></a>危険にさらされたアカウントのブロック解除

危険にさらされたアカウントのブロックを解除するには、次の方法があります。

1. **パスワードをリセットする** - ユーザーのパスワードをリセットすることができます。 
2. **すべてのリスク イベントを破棄する** - アクセスをブロックするために構成されたユーザー リスク レベルに到達した場合、ユーザー リスク ポリシーによってユーザーがブロックされます。 報告されたリスク イベントを手動で閉じることにより、ユーザーのリスク レベルを下げることができます。 
3. **ポリシーから除外する** - サインイン ポリシーの現在の構成が原因で特定のユーザーに問題が発生していると考えられる場合は、そのポリシーからユーザーを除外できます。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。
4. **ポリシーを無効にする** - ポリシーの構成が原因ですべてのユーザーに問題が発生していると考えられる場合は、ポリシーを無効にすることができます。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
 
Azure AD Identity Protection の詳細については、 「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」を参照してください。
