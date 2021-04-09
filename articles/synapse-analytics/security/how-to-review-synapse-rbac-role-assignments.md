---
title: Synapse Studio で Synapse RBAC ロールの割り当てを確認する方法
description: この記事では、Synapse Studio を使用して Synapse RBAC ロールの割り当てを確認する方法について説明します
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102157"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Synapse RBAC ロールの割り当てを確認する方法

Synapse RBAC ロールを使用してユーザー、グループ、その他のセキュリティ プリンシパルにアクセス許可を割り当て、Synapse リソースへのアクセスと使用を可能にすることができます。  [詳細を表示](./synapse-workspace-synapse-rbac.md)

この記事では、ワークスペースの現在のロールの割り当てを確認する方法について説明します。

任意の Synapse RBAC ロールを使用して、アクセス権のないオブジェクトの割り当てを含めたすべてのスコープの Synapse RBAC ロールの割り当てを一覧表示できます。 Synapse RBAC アクセスを許可できるのは、Synapse 管理者だけです。  

>[!Note]
>ゲスト ユーザー (別の AD テナントのユーザー) は、Synapse 管理者ロールが割り当てられている場合でも、ロールの割り当てを表示または管理できません。    

## <a name="open-synapse-studio"></a>Synapse Studio を開く  

ロールの割り当てを確認するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。 

![ワークスペースへのログイン](./media/common/login-workspace.png) 
 
 ワークスペースを開いたら、左側の **[管理]** ハブを選択し、 **[セキュリティ]** セクションを展開して **[アクセス制御]** を選択します。 

 ![左側の [セキュリティ] セクションの [アクセス制御] を選択する](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>ワークスペースのロールの割り当てを確認する

[アクセス制御] 画面には、ワークスペースに対する現在のすべてのロールの割り当てが、ロール別にグループ化されて表示されます。 各割り当てには、プリンシパル名、プリンシパルの種類、ロール、およびそのスコープが含まれます。

![[アクセス制御] 画面](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

プリンシパルに異なるスコープで同じロールが割り当てられている場合は、そのプリンシパルに対して複数の割り当てが表示されます (スコープごとに 1 つ)。  

ロールがセキュリティ グループに割り当てられている場合は、親グループから継承されたロールではなく、そのグループに明示的に割り当てられているロールが表示されます。  

この一覧をプリンシパル名または電子メールでフィルター処理したり、オブジェクトの種類、ロール、またはスコープを選択的にフィルター処理したりできます。 [名前] フィルターに名前または電子メール エイリアスを入力すると、自分に割り当てられているロールが表示されます。 ロールを変更できるのは、Synapse 管理者だけです。

>[!Important] 
>ロールが割り当てられているグループの直接的または間接的なメンバーである場合、表示されていないアクセス許可が与えられている可能性があります。

>[!tip]
>グループ メンバーシップは、Azure portal の Azure Active Directory を使用して見つけることができます。  

新しいワークスペースを作成すると、ワークスペースのスコープでの Synapse 管理者ロールが、自分とワークスペースの MSI サービス プリンシパルに自動的に付与されます。

## <a name="next-steps"></a>次のステップ

[Synapse RBAC ロールの割り当てを管理する方法](./how-to-manage-synapse-rbac-role-assignments.md)について学習する。

[特定のタスクを実行するために必要なロール](./synapse-workspace-understand-what-role-you-need.md)について学習する。