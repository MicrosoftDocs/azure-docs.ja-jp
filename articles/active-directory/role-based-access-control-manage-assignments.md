---
title: "Azure リソースのアクセス権の割り当ての確認 | Microsoft Docs"
description: "Azure Portal の任意のユーザーまたはグループについて、すべてのロールベースのアクセス制御の割り当てを確認および管理する"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/01/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 55baaf0984e01f3cbca7b2296f0b24661e70e7b5
ms.lasthandoff: 03/04/2017


---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Azure Portal のユーザーとグループのアクセス権の割り当てを表示 - パブリック プレビュー
> [!div class="op_single_selector"]
> * [ユーザーまたはグループによるアクセスの管理](role-based-access-control-manage-assignments.md)
> * [リソースによるアクセスの管理](role-based-access-control-configure.md)

Azure Active Directory のロールベースのアクセス制御 (RBAC) のプレビューでは、Azure リソースへのアクセス権を管理できます。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md)

2 つの方法でアクセス許可を制限できるため、RBAC ではアクセス権をきめ細かく割り当てられます。

* **スコープ:** RBAC ロールの割り当ては、指定したサブスクリプション、リソース グループ、またはリソースに限られます。 1 つのリソースのアクセス権を付与されたユーザーは、同じサブスクリプションで他のリソースにアクセスすることはできません。
* **ロール:** 割り当てのスコープ内で、ロールを割り当てることによって、アクセス権がさらに限定されます。 ロールは、所有者のように高レベルにすることも、仮想マシン リーダーのように限定することもできます。

ロールは、割り当てのスコープであるサブスクリプション、リソース グループ、またはリソース内からのみ割り当てられます。 ただし、特定のユーザーまたはグループのすべてのアクセス権の割り当てを&1; 箇所から確認できます。

[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](role-based-access-control-configure.md)方法について参照してください。

## <a name="view-access-assignments"></a>アクセス権の割り当ての表示
1 つのユーザーまたはグループのアクセス権の割り当てを調べるには、 [Azure Portal](http://portal.azure.com)の Azure Active Directory から開始します。

1. **[Azure Active Directory]**を選択します。 このオプションがナビゲーションの一覧に表示されない場合は、**[More Services (その他のサービス)]** を選択して、**[Azure Active Directory]** が表示されるまで下にスクロールします。
2. **[ユーザーとグループ]** を選択して、**[すべてのユーザー]** または **[すべてのグループ]** を選択します。 この例では、個々のユーザーについて説明します。
    ![Azure Active Directory のユーザーおよびグループの管理 - スクリーンショット](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. ユーザーを氏名またはユーザー名で検索します。
4. ユーザー ブレードで **[Azure resources (Azure リソース)]** を選択します。 そのユーザーのすべてのアクセス権の割り当てが表示されます。

### <a name="read-permissions-to-view-assignments"></a>読み取りアクセス許可の割り当ての表示
このページには、読み取りのアクセス許可のあるアクセス権の割り当てについてのみ表示されます。 たとえば、サブスクリプション A への読み取りアクセス権があり、Azure リソース ブレードに移動して、あるユーザーの割り当てを確認するとします。 そのユーザーのサブスクリプション A へのアクセス権の割り当てを確認できますが、そのユーザーがサブスクリプション B へのアクセス権を割り当てられているかどうかは確認できません。

## <a name="delete-access-assignments"></a>アクセス権の割り当ての削除
このブレードから、ユーザーまたはグループに直接割り当てられたアクセス権の割り当てを削除できます。 アクセス権の割り当てが親グループから継承された場合は、リソースまたはサブスクリプションに移動して、そこから割り当てを管理する必要があります。

1. ユーザーまたはグループのすべてのアクセス権の割り当てリストから、削除するものを選択します。
2. **[削除]** を選択し、**[はい]** を選択して確定します。
    ![アクセス権の割り当ての削除 - スクリーンショット](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>次のステップ

* ロールベースのアクセス制御を開始して、 [Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](role-based-access-control-configure.md)
* [RBAC の組み込みロール](role-based-access-built-in-roles.md)


