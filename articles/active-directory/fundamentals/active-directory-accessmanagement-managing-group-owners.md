---
title: グループ所有者を追加または削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してグループ所有者を追加または削除する方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b255f64547c3bae56d31415dc94a751989ca1f45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504901"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Azure Active Directory でグループ所有者を追加または削除する
Azure Active Directory (Azure AD) グループを所有および管理するのはグループ所有者です。 グループ所有者には、ユーザーまたはサービス プリンシパルがなることができ、メンバーシップを含むグループを管理できます。 既存のグループ所有者またはグループを管理する管理者のみがグループ所有者を割り当てることができます。 グループ所有者は、グループのメンバーである必要はありません。

グループに所有者がいない場合、まだグループを管理する管理者がグループを管理できます。 すべてのグループに少なくとも 1 人の所有者を割り当てることをお勧めします。 所有者がグループに割り当てられた後は、そのグループの最後の所有者を削除することはできません。 グループから最後の所有者を削除する前に、必ず別の所有者を選択してください。

## <a name="add-an-owner-to-a-group"></a>グループに所有者を追加する
次に、Azure AD portal を使用して、グループの所有者としてユーザーを追加する方法を示します。 グループ所有者としてサービス プリンシパルを追加するには、[PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner) を使用してそれを実行する手順に従ってください。

### <a name="to-add-a-group-owner"></a>グループ所有者を追加するには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、 **[グループ]** を選択して、所有者を削除するグループ (この例では、*MDM policy - West*) を選択します。

3. **[MDM policy - West の概要]** ページで、 **[所有者]** を選択します。

    ![[MDM policy - West の概要] ページ、[所有者] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. **[MDM policy - West - 所有者]** ページで、 **[所有者の追加]** を選択し、新しいグループ所有者にするユーザーを選択して、 **[選択]** を選択します。

    ![[MDM policy - West - 所有者] ページ、[所有者の追加] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    新しい所有者を選択して、 **[所有者]** ページを更新すると、所有者の一覧に名前が追加されているのがわかります。

## <a name="remove-an-owner-from-a-group"></a>グループから所有者を削除する
Azure AD を使用してグループから所有者を削除します。

### <a name="to-remove-an-owner"></a>所有者を削除するには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、 **[グループ]** を選択して、所有者を削除するグループ (この例では、*MDM policy - West*) を選択します。

3. **[MDM policy - West の概要]** ページで、 **[所有者]** を選択します。

    ![[MDM policy - West の概要] ページ、[所有者の削除] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. **[MDM policy - West - 所有者]** ページで、グループ所有者として削除を選択するユーザーを選択し、ユーザーの情報ページから **[削除]** を選択します。次に **[はい]** を確認して、削除を確定します。

    ![ユーザーの情報ページ、[削除] オプションが強調表示](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    所有者を削除した後、 **[所有者]** ページに戻ると、所有者の一覧から名前が削除されているのがわかります。

## <a name="next-steps"></a>次のステップ
- [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)

- [グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)

- [グループを使用して、統合された SaaS アプリに対するアクセス権を割り当てる](../enterprise-users/groups-saasapps.md)

- [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)

- [グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-v2-cmdlets.md)
