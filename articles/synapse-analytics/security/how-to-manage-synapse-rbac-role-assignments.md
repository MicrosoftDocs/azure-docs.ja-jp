---
title: Synapse Studio で Synapse RBAC の割り当てを管理する方法
description: この記事では、AAD セキュリティ プリンシパルに Synapse RBAC ロールを割り当てたり、取り消したりする方法について説明します。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102191"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Synapse Studio で Synapse RBAC ロールの割り当てを管理する方法

Synapse RBAC は、ロールを使用してユーザー、グループ、その他のセキュリティ プリンシパルにアクセス許可を割り当てることにより、Synapse リソースおよびコード成果物のアクセスや使用を可能にします。  [詳細を表示](./synapse-workspace-synapse-rbac.md)

この記事では、Synapse RBAC ロールの割り当てを追加および削除する方法について説明します。

>[!Note]
>- Synapse RBAC ロールの割り当てを管理するには、ワークスペース上の、または管理するオブジェクトを含むより低いレベルのスコープでの Synapse 管理者ロールが必要です。 ワークスペース上の Synapse 管理者である場合は、ワークスペース内のすべてのオブジェクトへのアクセス権を付与できます。 
>- 別の AD テナントからの **ゲスト ユーザー** は、Synapse 管理者ロールが割り当てられている場合でも、ロールの割り当てを表示したり、管理したりできません。
>- どの Synapse 管理者も割り当てられていないか、または使用不可である場合にワークスペースへのアクセスを回復しやすくするために、ワークスペース上の **Azure RBAC** ロールの割り当てを管理するためのアクセス許可を持つユーザーは **Synapse RBAC** ロールの割り当ても管理できるため、Synapse 管理者またはその他の Synapse ロールの割り当てを追加できます。
>- SQL プールへのアクセスは、SQL アクセス許可を使用して管理されます。  Synapse 管理者および Synapse SQL 管理者ロールを除き、Synapse RBAC ロールで SQL プールへのアクセス権は付与されません。

>[!important]
>- Synapse RBAC ロールの割り当てに加えられた変更は、有効になるまでに 2 ～ 5 分かかることがあります。 
>- セキュリティ グループのメンバーシップを変更することによって Synapse RBAC アクセス許可を管理している場合、メンバーシップへの変更は Azure Active Directory を使用して管理されます。  グループ メンバーシップへの変更は、有効になるまでに 10 ～ 15 分以上かかることがあります。

## <a name="open-synapse-studio"></a>Synapse Studio を開く  

ユーザー、グループ、サービス プリンシパル、またはマネージド ID にロールを割り当てるには、まず [Synapse Studio を開いて](https://web.azuresynapse.net/)ワークスペースを選択します。 

![ワークスペースへのログイン](./media/common/login-workspace.png) 
 
 ワークスペースを開いたら、左側の **[セキュリティ]** セクションを展開し、 **[アクセス制御]** を選択します。 

 ![左側の [セキュリティ] セクションの [アクセス制御] を選択する](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

[アクセス制御] 画面には、現在のロールの割り当てが一覧表示されます。  この一覧をプリンシパル名または電子メールでフィルター処理したり、含まれているオブジェクトの種類、ロール、またはスコープを選択的にフィルター処理したりできます。 この画面から、ロールの割り当てを追加または削除できます。  

## <a name="add-a-synapse-role-assignment"></a>Synapse ロールの割り当てを追加する

[アクセス制御] 画面で、 **[+ 追加]** を選択して新しいロールの割り当てを作成します。

![[+ 追加] をクリックして新しいロールの割り当てを作成する](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

[ロールの割り当ての追加] タブで、ワークスペース スコープまたはワークスペース項目スコープのロールの割り当てを作成できます。 

## <a name="add-workspace-scoped-role-assignment"></a>ワークスペース スコープのロールの割り当てを追加する

まず、スコープとして **[ワークスペース]** を選択してから **[Synapse RBAC ロール]** を選択します。  ロールが割り当てられる **プリンシパル** を選択してから、ロールの割り当てを作成します。 

![ワークスペース ロールの割り当てを追加する - ロールを選択する](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

割り当てられたロールは、ワークスペース内の該当するすべてのオブジェクトに適用されます。

## <a name="add-workspace-item-scoped-role-assignment"></a>ワークスペース項目スコープのロールの割り当てを追加する

きめ細かなスコープでロールを割り当てるには、スコープとして **[ワークスペース項目]** を選択してから、スコーピングの **[項目の種類]** を選択します。       

![ワークスペース項目ロールの割り当てを追加する - 項目の種類を選択する](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

スコープとして使用される特定の **項目** を選択してから、割り当てられる **ロール** をドロップダウンから選択します。  このドロップダウンには、選択されている項目の種類で有効なロールのみが一覧表示されます。 [詳細については、こちらを参照してください](./synapse-workspace-synapse-rbac.md)。  

![ワークスペース項目ロールの割り当てを追加する - ロールを選択する](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
次に、ロールが割り当てられる **プリンシパルを選択** します。  複数のプリンシパルを繰り返し選択できます。  **[適用]** を選択して、ロールの割り当てを作成します。

## <a name="remove-a-synapse-rbac-role-assignment"></a>Synapse RBAC ロールの割り当てを削除する

Synapse RBAC アクセス権を取り消すには、該当するロールの割り当てを削除します。  [アクセス制御] 画面で、フィルターを使用して、削除されるロールの割り当てを検索します。  ロールの割り当てをオンにして **[アクセス権の削除]** を選択します。   

![アクセス権を削除するロールの割り当てを削除する](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

ロールの割り当てへの変更は、有効になるまでに 2 ～ 5 分かかることに注意してください。   

## <a name="next-steps"></a>次のステップ

[一般的なタスクを実行するために必要な Synapse RBAC ロールを理解する](./synapse-workspace-understand-what-role-you-need.md)