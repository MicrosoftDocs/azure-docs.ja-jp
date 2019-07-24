---
title: B2B コラボレーションの招待の委任 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーション ユーザーのプロパティは構成できます
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d40397f30b471699f42878a38c88efebcc6305
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674518"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B コラボレーションの招待の委任

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーションでは、グローバル管理者でなくても招待を送信できます。 それには、ポリシーを使用して、招待の送信が許可されているロールを持つユーザーに招待を委任します。 ゲスト ユーザーの招待を委任するための重要な新しい方法は、ゲストの招待元ロールを通じた方法です。

## <a name="guest-inviter-role"></a>ゲストの招待元ロール
ゲストの招待元ロールにユーザーを割り当てて、招待を送信することができます。 全体管理者ロールのメンバーである必要はありません。 既定では、全体管理者が通常のユーザーの招待を無効にしていない限り、通常のユーザーも招待 API を呼び出すことができます。 ユーザーは、Azure ポータルまたは PowerShell を使用して API を呼び出すこともできます。

次の例は、PowerShell を使って、ゲストの招待元ロールにユーザーを追加する方法を示しています。

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>招待できるユーザーの制御

Azure Active Directory で **[ユーザー設定]** を選択します。 **[外部ユーザー]** で、**[外部コラボレーションの設定を管理します]** を選択します。

> [!NOTE]
> **[外部コラボレーションの設定]** は、**[組織の関係]** ページから利用することもできます。 Azure Active Directory の **[管理]** で、**[組織の関係]** > **[設定]** の順に移動します。

![外部コラボレーションの設定](./media/delegate-invitations/control-who-to-invite.png)

Azure AD B2B コラボレーションでは、テナント管理者が次の招待ポリシーを設定できます。

- 招待を無効にする
- 管理者と、ゲストの招待元ロールのユーザーのみが招待できる
- 管理者、ゲストの招待元ロール、およびメンバーが招待できる
- ゲストを含むすべてのユーザーが招待できる

既定では、テナントは #4  (ゲストを含むすべてのユーザーが B2B ユーザーを招待できる) に設定されます。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [招待を使用せずに B2B コラボレーション ゲスト ユーザーを追加する](add-user-without-invite.md)
- [B2B コラボレーション ユーザーのロールへの追加](add-guest-to-role.md)


