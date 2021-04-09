---
title: 最近削除されたユーザーの復元または完全な削除 - Azure AD
description: Azure Active Directory を使用して、復元可能なユーザーを表示する方法、削除されたユーザーを復元する方法、およびユーザーを完全に削除する方法。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514391"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory を使用して最近削除されたユーザーを復元または削除する
ユーザーを削除した後、アカウントは 30 日間、中断状態のままになります。 その 30 日の期間中は、ユーザー アカウントをそのすべてのプロパティと共に復元することができます。 30 日間の期間が経過すると、完全削除のプロセスが自動的に開始されます。

Azure portal 内で Azure Active Directory (Azure AD) を使用して、復元可能なユーザーを表示したり、削除されたユーザーを復元したり、ユーザーを完全に削除したりすることができます。

>[!Important]
>管理者も Microsoft カスタマー サポートも、完全に削除されたユーザーを復元することはできません。

## <a name="required-permissions"></a>必要なアクセス許可
ユーザーの復元または完全な削除を行うには、次のいずれかのロールが必要です。

- 全体管理者

- パートナー レベル 1 のサポート

- パートナー レベル 2 のサポート

- ユーザー管理者

## <a name="view-your-restorable-users"></a>復元可能なユーザーを表示する
削除されてからまだ 30 日が経過していないユーザーはすべて表示することができます。 これらのユーザーは復元することができます。

### <a name="to-view-your-restorable-users"></a>復元可能なユーザーを表示するには
1. 組織のグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. **[Azure Active Directory]** 、 **[ユーザー]** 、 **[削除済みのユーザー]** の順に選択します。

    復元可能なユーザーの一覧を確認します。

    ![まだ復元可能なユーザーが表示されている [ユーザー - 削除済みのユーザー] ページ](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>最近削除されたユーザーを復元する

ユーザー アカウントが組織から削除されると、そのアカウントは中断状態となりますが、関連する組織情報はすべて維持されます。 ユーザーを復元すると、この組織情報も復元されます。

> [!Note]
> ユーザーが復元されると、削除時点でユーザーに割り当てられていたライセンスも復元されます。それらのライセンスで利用可能なシートが存在していなかったとしても同様です。 その後、購入分を超えるライセンスを利用した場合、所属組織は、ライセンスの使用に関して一時的に非準拠状態となる可能性があります。

### <a name="to-restore-a-user"></a>ユーザーを復元するには
1. **[ユーザー - 削除済みユーザー]** ページ上で、利用可能なユーザーを検索し、その中のいずれかを選択します。 たとえば、_Mary Parker_ を選択します。

2. **[ユーザーの復元]** を選びます。

    ![[ユーザー - 削除済みユーザー] ページ上で強調表示されている [ユーザーの復元] オプション](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>ユーザーの完全な削除
30 日経過してから自動削除されるのを待つことなく、ご利用の組織からユーザーを完全に削除することができます。 完全に削除されたユーザーは、担当の管理者も、別の管理者も、Microsoft カスタマー サポートも復元することができません。

>[!Note]
>誤ってユーザーを完全に削除してしまった場合は、新しいユーザーを作成して、以前の情報をすべて手動で入力する必要があります。 新しいユーザーの作成の詳細については、[ユーザーの追加または削除](add-users-azure-active-directory.md)に関するページを参照してください。

### <a name="to-permanently-delete-a-user"></a>ユーザーを完全に削除するには

1. **[ユーザー - 削除済みユーザー]** ページ上で、利用可能なユーザーを検索し、その中のいずれかを選択します。 たとえば、_Rae Huff_ を選択します。

2. **[完全に削除]** を選びます。

    ![[ユーザー - 削除済みユーザー] ページ上で強調表示されている [ユーザーの削除] オプション](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>次のステップ
ユーザーを復元または削除したら、次のことができます。

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [別の組織からゲスト ユーザーを追加する](../external-identities/what-is-b2b.md)

他の使用可能なユーザー管理タスクの詳細については、[Azure AD のユーザー管理のドキュメント](../enterprise-users/index.yml)を参照してください。
