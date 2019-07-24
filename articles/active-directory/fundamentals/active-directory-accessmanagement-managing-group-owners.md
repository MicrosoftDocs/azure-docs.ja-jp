---
title: グループ所有者を追加または削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してグループ所有者を追加または削除する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2541a1d76b56f92b250fb422951769db7877213e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181747"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Azure Active Directory でグループ所有者を追加または削除する
Azure Active Directory (Azure AD) グループを所有および管理するのはグループ所有者です。 グループ所有者には、グループとそのメンバーの管理をリソース所有者 (管理者) によって割り当てられます。 グループ所有者は、グループのメンバーである必要はありません。 割り当てられたグループ所有者を追加または削除できるのは、リソース所有者だけです。

場合によっては、管理者はグループ所有者を割り当てないこともできます。 その場合、管理者がグループ所有者になります。 また、グループ設定で制限されない限り、所有者は他の所有者を自身のグループに割り当てることができます。

## <a name="add-an-owner-to-a-group"></a>グループに所有者を追加する
Azure AD を使用して、追加のグループ所有者をグループに追加します。

### <a name="to-add-a-group-owner"></a>グループ所有者を追加するには
1. ディレクトリのグローバル管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択して、所有者を削除するグループ (この例では、*MDM policy - West*) を選択します。

3. **[MDM policy - West の概要]** ページで、**[所有者]** を選択します。

    ![[MDM policy - West の概要] ページ、[所有者] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. **[MDM policy - West - 所有者]** ページで、**[所有者の追加]** を選択し、新しいグループ所有者にするユーザーを選択して、**[選択]** を選択します。

    ![[MDM policy - West - 所有者] ページ、[所有者の追加] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    新しい所有者を選択して、**[所有者]** ページを更新すると、所有者の一覧に名前が追加されているのがわかります。

## <a name="remove-an-owner-from-a-group"></a>グループから所有者を削除する
Azure AD を使用してグループから所有者を削除します。

### <a name="to-remove-an-owner"></a>所有者を削除するには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択して、所有者を削除するグループ (この例では、*MDM policy - West*) を選択します。

3. **[MDM policy - West の概要]** ページで、**[所有者]** を選択します。

    ![[MDM policy - West の概要] ページ、[所有者] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. **[MDM policy - West - 所有者]** ページで、グループ所有者として削除を選択するユーザーを選択し、ユーザーの情報ページから **[削除]** を選択します。次に **[はい]** を確認して、削除を確定します。

    ![ユーザーの情報ページ、[削除] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    所有者を削除した後、**[所有者]** ページに戻ると、所有者の一覧から名前が削除されているのがわかります。

## <a name="next-steps"></a>次の手順
- [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)

- [グループの設定を構成するための Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-cmdlets.md)

- [グループを使用して、統合された SaaS アプリに対するアクセス権を割り当てる](../users-groups-roles/groups-saasapps.md)

- [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)

- [グループの設定を構成するための Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-v2-cmdlets.md)
