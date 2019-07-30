---
title: RBAC を使用した Azure Portal ダッシュボードの共有 | Microsoft Docs
description: この記事では、Azure ポータルでロールベースのアクセス制御を使用して、ダッシュボードを共有する方法について説明します。
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: fbbc8a4f636a95d18baa0dc5de541279ce36789b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551998"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>ロールベースのアクセス制御を使用して Azure ダッシュボードを共有する
構成したダッシュボードは、発行して組織内の他のユーザーと共有することができます。 ダッシュボードを第三者が表示できるようにするには、Azure の[ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)を使用します。 特定のユーザーまたはユーザーのグループをロールに割り当て、発行したダッシュボードの表示または変更をそれらのユーザーに許可するかどうかをそのロールで定義します。 

発行したダッシュボードはすべて Azure リソースとして実装されます。つまり、それらは管理可能な要素としてサブスクリプションに存在し、リソース グループに属します。  アクセス制御の見地からは、ダッシュボードはその他のリソース (仮想マシン、ストレージ アカウントなど) と変わりありません。

> [!TIP]
> ダッシュボードの個々のタイルには、表示されているリソースに基づいて、独自のアクセス制御の要件が適用されます。  つまり、広く共有しながらも個々のタイルのデータを保護するようにダッシュボードを設計することができます。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>ダッシュボードのアクセス制御について
ロールベースのアクセス制御 (RBAC) では、以下に示した 3 つのスコープ レベルでユーザーをロールに割り当てることができます。

* サブスクリプション
* resource group
* resource

割り当てたアクセス許可は、サブスクリプションからリソースへと継承されます。 発行したダッシュボードはリソースです。 したがってサブスクリプションのロールに対して既にユーザーが割り当てられていると、発行したダッシュボードに対しても、そのロールが作用します。 

たとえば次のようになります。  Azure サブスクリプションがあり、チームのさまざまなメンバーに、サブスクリプションの**所有者**、**共同作成者**、または**閲覧者**ロールが割り当てられているとします。 所有者または共同作成者であるユーザーは、サブスクリプション内でダッシュボードを一覧表示、表示、作成、変更、および削除できます。  閲覧者であるユーザーは、ダッシュボードの一覧表示と表示はできますが、変更と削除はできません。  閲覧者アクセス許可を持つユーザーは、発行されたダッシュボードにローカルで編集を加えることはできますが (たとえば、問題のトラブルシューティングを行う場合)、その変更をサーバーに戻すことはできません。  このようなユーザーは、個人用にダッシュボードのプライベート コピーを作成することが可能です。

一方、複数のダッシュボードを含んだリソース グループにアクセス許可を割り当てたり、個々のダッシュボードにアクセス許可を割り当てたりすることもできます。 たとえば一連のユーザーについて、サブスクリプション レベルでは権限を制限しつつ、特定のダッシュボードについては、それよりも大きなアクセス権を割り当てることができます。 この場合は、一連のユーザーをそのダッシュボードのロールに割り当てることになります。 

## <a name="publish-dashboard"></a>ダッシュボードの発行
サブスクリプション内の一連のユーザーとの間で共有するダッシュボードの設定が済んでいるとしましょう。 以下の手順では、Storage Managers というカスタム グループを想定していますが、対象グループには好きな名前を付けてかまいません。 Active Directory グループの作成とそのグループへのユーザーの追加については、「 [Azure Active Directory におけるグループの管理](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

1. ダッシュボードで **[共有]** を選択します。
   
     ![select share](./media/azure-portal-dashboard-share-access/select-share.png)
2. アクセス権を割り当てる前に、ダッシュボードを発行する必要があります。 既定では、 **"ダッシュボード"** という名前のリソース グループにダッシュボードが発行されます。 **[発行]** を選択します。
   
     ![[発行]](./media/azure-portal-dashboard-share-access/publish.png)

ダッシュボードが発行されました。 サブスクリプションから継承されたアクセス許可が適切であれば、これ以上の作業は不要です。 同じ組織内の他のユーザーが、そのサブスクリプション レベルのロールに応じてダッシュボードにアクセスし、変更を加えることができます。 ただしこのチュートリアルでは、ダッシュボードのロールに一連のユーザーを割り当てることにします。

## <a name="assign-access-to-a-dashboard"></a>ダッシュボードへのアクセス権の割り当て
1. ダッシュボードを発行した後、 **[ユーザーの管理]** を選択します。
   
     ![[ユーザーの管理]](./media/azure-portal-dashboard-share-access/manage-users.png)
2. このダッシュボードのロールが既に割り当てられている既存のユーザーが一覧表示されます。 一覧表示される既存のユーザーは、以下の画像とは異なる場合があります。 ほとんどの割り当てはサブスクリプションから継承されたものであると考えられます。 新しいユーザーまたはグループを追加するには、 **[追加]** を選択します。
   
     ![add user](./media/azure-portal-dashboard-share-access/existing-users.png)
3. 許可するアクセス許可に相当するロールを選択します。 この例では、 **[共同作成者]** を選択します。
   
     ![select role](./media/azure-portal-dashboard-share-access/select-role.png)
4. ロールに割り当てるユーザーまたはグループを選択します。 探しているユーザーまたはグループが一覧に見当たらない場合は、検索ボックスを使用してください。 選択できるグループの一覧は、Active Directory に作成したグループによって異なります。
   
     ![select user](./media/azure-portal-dashboard-share-access/select-user.png) 
5. ユーザーまたはグループを追加したら、 **[OK]** を選択します。 
6. 新しい割り当てがユーザーの一覧に追加されます。 対応する **[アクセス]** 欄が、 **[継承済み]** ではなく **[割り当て済み]** になっていることに注目してください。
   
     ![assigned roles](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>次の手順
* ロールの一覧については、[RBAC: 組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。
* リソース管理の詳細については、「 [ポータルを使用した Azure リソースの管理](resource-group-portal.md)」を参照してください。

