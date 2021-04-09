---
title: ユーザーとグループのライセンス割り当てを変更する - Azure AD | Microsoft Docs
description: Azure Active Directory のグループ ライセンスを使用してさまざまなサービス プランにグループ内のユーザーを移行する方法
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546541"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Azure Active Directory でのユーザーまたはグループのライセンス割り当ての変更

この記事では、Azure Active Directory (Azure AD) でユーザーおよびグループをサービス ライセンス プラン間で移動する方法について説明します。 Azure AD の目標とするアプローチは、ライセンスの変更中にサービスやデータが失われないようにすることです。 ユーザーはサービスをシームレスに切り替える必要があります。 この記事のライセンス プランの割り当て手順では、Office 365 E1 のユーザーまたはグループを Office 365 E3 に変更することについて説明しますが、この手順はすべてのライセンス プランに適用されます。 ユーザーまたはグループのライセンス割り当てを更新するとき、ライセンス割り当ての削除と新しい割り当てが同時に行われることにより、ライセンスの変更時にユーザーがサービスにアクセスできなくなったり、プラン間のライセンスの競合が発生したりすることはありません。

## <a name="before-you-begin"></a>開始する前に

ライセンス割り当てを更新する前に、更新されるすべてのユーザーまたはグループについて、特定の前提条件が真であることを確認することが重要です。 グループ内のすべてのユーザーについて前提条件が真でない場合、一部のユーザーの移行に失敗することがあります。 その結果、一部のユーザーがサービスまたはデータへのアクセスを失う可能性があります。 次のことを確認します。

- ユーザーは、グループに割り当てられ、ユーザーによって継承され、直接割り当てられていない、現在のライセンス プラン (この場合は Office 365 E1) を持っている。

- 割り当てるライセンス プランについて使用可能なライセンスが十分にある。 十分なライセンスがない場合、一部のユーザーに新しいライセンス プランが割り当てられない可能性があります。 使用可能なライセンス数を確認することができます。

- ユーザーは、目的のライセンスと競合するか、または現在のライセンスの削除を妨げる可能性があるその他のサービス ライセンスを割り当てられていない。 たとえば、Workplace Analytics や Project Online のようなサービスからのライセンスは、他のサービスに依存しています。

- グループをオンプレミスで管理し、それらを Azure AD Connect を介して Azure AD に同期している場合は、オンプレミス システムを使用してユーザーを追加または削除できます。 変更内容が Azure AD と同期されてグループ ライセンスによって取得されるまでに時間がかかることがあります。

- Azure AD 動的グループ メンバーシップを使用している場合は、属性を変更してユーザーを追加または削除しますが、ライセンス割り当ての更新プロセスは変わりません。

## <a name="change-user-license-assignments"></a>ユーザー ライセンス割り当てを変更する

**[Update license assignments]\(ライセンスの割り当ての更新\)** ページの一部のチェックボックスが使用できない場合、これはグループ ライセンスから継承されたため変更できないサービスであることを示しています。

1. Azure AD 組織のライセンス管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure Active Directory]**  >  **[ユーザー]** を選択し、ユーザーの **[Profile]\(プロファイル\)** ページを開きます。
1. **[ライセンス]** を選択します。
1. **[Assignments]\(割り当て\)** を選択して、ユーザーまたはグループのライセンス割り当てを編集します。 **[Assignments]\(割り当て\)** ページでは、ライセンス割り当ての競合を解決できます。
1. Office 365 E3 のチェック ボックスをオンにし、少なくともユーザーに割り当てられている E1 サービスがすべて選択されていることを確認します。
1. Office 365 E1 のチェック ボックスをオフにします。

    ![Office 365 E1 がオフになっていて Office 365 E3 が選択されているユーザーのライセンス割り当てページ](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. **[保存]** を選択します。

Azure AD は新しいライセンスを適用し、同時に古いライセンスを削除してサービスの継続性を提供します。

## <a name="change-group-license-assignments"></a>グループ ライセンス割り当てを変更する

1. Azure AD 組織のライセンス管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure Active Directory]**  >  **[グループ]** を選択し、グループの **[Overview]\(概要\)** ページを開きます。
1. **[ライセンス]** を選択します。
1. **[Assignments]\(割り当て\)** コマンドを選択して、ユーザーまたはグループのライセンス割り当てを編集します。
1. Office 365 E3 のチェック ボックスをオンにします。 サービスの継続性を維持するために、ユーザーに既に割り当てられている E1 サービスをすべて選択してください。
1. Office 365 E1 のチェック ボックスをオフにします。

    ![ユーザーまたはグループのライセンス ページで割り当てコマンドを選択する](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. **[保存]** を選択します。

サービスの継続性を提供するために、Azure AD はグループ内のすべてのユーザーに対し、新しいライセンスを適用し、同時に古いライセンスを削除します。

## <a name="next-steps"></a>次のステップ

グループによるライセンス管理の他のシナリオについては、以下の記事を参照してください。

- [Assigning licenses to a group in Azure Active Directory](licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
- [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
- [Azure Active Directory で個別にライセンスを付与されたユーザーをグループ ライセンスに移行する方法](licensing-groups-migrate-users.md)
- [Azure Active Directory グループ ライセンスのその他のシナリオ](licensing-group-advanced.md)
- [Azure Active Directory のグループ ライセンスの PowerShell の例](licensing-ps-examples.md)
