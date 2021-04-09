---
title: PIM を管理するアクセス権を付与する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を管理するためのアクセス権を他の管理に付与する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835205"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Privileged Identity Management へのアクセスを委任する

Privileged Identity Management (PIM) へのアクセス権を委任する場合は、グローバル管理者が、他のユーザーに特権ロール管理者ロールを割り当てることができます。 既定では、セキュリティ管理者とセキュリティ リーダーが持つのは Privileged Identity Management に対する読み取り専用アクセス権だけです。 Privileged Identity Management へのアクセス権を付与するには、最初のユーザーが他のユーザーに **特権ロール管理者** ロールを割り当てます。 Azure AD ロールのみを管理するのに必要なのは、特権ロール管理者ロールです。 Azure リソースの設定を管理するために、特権ロール管理者のアクセス許可は必要ありません。

> [!NOTE]
> Privileged Identity Management の管理には、Azure AD Multi-Factor Authentication が必要です。 Microsoft アカウントは Azure AD Multi-Factor Authentication に登録できないため、Microsoft アカウントでサインインするユーザーは、Privileged Identity Management にアクセスできません。

1 人のユーザーがロックアウトされたり、アカウントが削除されたりしたときのために、特権ロール管理者ロールを割り当てられたユーザーが常に少なくとも 2 人いるようにしてください。

## <a name="delegate-access-to-manage-pim"></a>PIM を管理するためのアクセスを委任する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure AD で **[Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[役割]** を選びます。

    ![Privileged Identity Management の Azure AD ロール - ロール](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. **[特権ロール管理者]** ロールを選択して、メンバー ページを開きます。

    ![特権ロール管理者 - メンバー](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **[メンバーの追加]** を選択して **[管理されるメンバーの追加]** ウィンドウを開きます。

1. **[メンバーの選択]** を選択して、 **[メンバーの選択]** ウィンドウを開きます。

    ![特権ロール管理者 - メンバーの選択](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. メンバーを選択し、**[選択]** をクリックします。

1. **[OK]** を選択して、メンバーを **特権ロール管理者** ロールの対象にします。

    新しいロールを Privileged Identity Management の他のユーザーに割り当てると、そのユーザーは、ロールをアクティブ化する **対象** として自動的に構成されます。

1. メンバーを固定するには、特権ロール管理者メンバーの一覧でユーザーを選択します。

1. **[詳細]** を選択し、**[永続化]** を選択して、割り当てを永続化します。

    ![特権ロール管理者 - 永続化](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. [Privileged Identity Management の使用を開始する](pim-getting-started.md)へのリンクをユーザーに送信します。

## <a name="remove-access-to-manage-pim"></a>PIM を管理するアクセス権を削除する

特権ロール管理者ロールからユーザーを削除する際は、割り当てられているユーザーの人数が削除後も少なくとも 2 人は存在するように、必ず事前に確認してください。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[役割]** を選びます。

1. **[特権ロール管理者]** ロールを選択して、メンバー ページを開きます。

1. 削除するユーザーの横のチェックボックスを選択した後、**[メンバーの削除]** を選択します。

    ![特権ロール管理者 - メンバーの削除](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. ロールからメンバーを削除するかどうかを確認するメッセージが表示されたら、**[はい]** を選択します。

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management の使用開始](pim-getting-started.md)
