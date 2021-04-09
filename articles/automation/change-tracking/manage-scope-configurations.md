---
title: Azure Automation の Change Tracking とインベントリのデプロイのスコープを制限する
description: この記事では、スコープ構成を使用して、Change Tracking とインベントリのデプロイのスコープを制限する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209170"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Change Tracking とインベントリのデプロイのスコープを制限する

この記事では、[Change Tracking とインベントリ](overview.md)機能を使用して VM に変更をデプロイする際のスコープ構成の操作方法について説明します。 詳細については、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](../../azure-monitor/insights/solution-targeting.md)」を参照してください。

## <a name="about-scope-configurations"></a>スコープ構成について

スコープ構成は、Change Tracking とインベントリのスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件 (クエリ) のグループです。 スコープ構成は、有効にするコンピューターを対象として設定するために、Log Analytics ワークスペース内で使用されます。 この機能から変更にコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。

## <a name="set-the-scope-limit"></a>スコープの制限を設定する

Change Tracking とインベントリのデプロイのスコープを制限するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Automation**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Automation アカウント]** を選択します。

3. Automation アカウントの一覧で、変更履歴とインベントリを有効にしたときに選択したアカウントを選択します。

4. お使いの Automation アカウントで、 **[関連リソース]** の下にある **[リンクされたワークスペース]** を選択します。

5. **[ワークスペースに移動]** をクリックします。

6. **[Workspace Data Sources]\(ワークスペース データ ソース\)** の下の **[Scope Configurations (Preview)]\(スコープ構成 (プレビュー)\)** を選択します。

7. `MicrosoftDefaultScopeConfig-ChangeTracking` スコープ構成の右側にある省略記号を選択し、 **[編集]** をクリックします。

8. 編集ペインで **[コンピューター グループの選択]** を選択します。 [コンピューター グループ] ペインに、スコープ構成を作成するために使用された、保存された検索条件が表示されます。 Change Tracking とインベントリで使用される保存済みの検索条件は次のとおりです。

    |名前     |カテゴリ  |エイリアス  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. 保存した検索条件を選択して、グループの設定に使用されるクエリを表示および編集します。 次の図は、クエリとその結果を示しています。

    ![保存した検索条件](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>次のステップ

* 変更履歴とインベントリの操作については、[変更履歴とインベントリの管理](manage-change-tracking.md)に関するページを参照してください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。
