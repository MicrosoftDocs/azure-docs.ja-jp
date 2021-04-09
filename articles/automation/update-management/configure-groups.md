---
title: Azure Automation Update Management で動的グループを使用する
description: この記事では、Azure Automation の Update Management で動的グループを使用する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 318b5498c826b1e29baa35850594cebca72c4f3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575927"
---
# <a name="use-dynamic-groups-with-update-management"></a>Update Management を利用して動的グループを使用する

Update Management を使用すると、Azure または Azure 以外の VM の動的グループを更新プログラムの展開の対象にすることができます。 動的グループを使用すると、展開を編集してマシンを更新する必要がなくなります。

> [!NOTE]
> 動的なグループは、クラシック VM では動作しません。

Azure または Azure 以外のマシンの動的グループは、Azure portal の **Update Management** から定義できます。 [VM の更新プログラムの管理](manage-updates-for-vm.md)に関する記事を参照してください。

動的グループは、展開時に Azure Automation で評価されるクエリによって定義されます。 動的グループ クエリで多数のマシンを取得しても、Azure Automation では一度に最大 1000 台のマシンしか処理できません。 「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)」をご覧ください。

> [!NOTE]
> 1000 台を超えるマシンを更新することが予想される場合は、更新プログラムを複数の更新スケジュールに分割することをお勧めします。 

## <a name="define-dynamic-groups-for-azure-machines"></a>Azure マシンの動的グループを定義する

Azure マシンの動的グループ クエリを定義する場合は、次の項目を使用して動的グループを設定できます。

* サブスクリプション
* リソース グループ
* 場所
* Tags

![グループを選択する](./media/configure-groups/select-groups.png)

動的グループ クエリの結果をプレビューするには、 **[プレビュー]** をクリックします。 プレビューには、現時点でのグループ メンバーシップが表示されます。 この例では、グループ **BackendServer** のタグ `Role` を持つマシンを検索しています。 このタグが追加されているマシンがさらにある場合は、それらはそのグループに対する今後の展開に追加されます。

![グループをプレビューする](./media/configure-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Azure 以外のマシンの動的グループを定義する

Azure 以外のマシンの動的グループでは、保存された検索 (コンピューター グループとも呼ばれます) を使用します。 保存された検索の作成方法については、「[コンピューター グループの作成](../../azure-monitor/logs/computer-groups.md#creating-a-computer-group)」を参照してください。 保存された検索が作成されると、Azure portal の **Update Management** で保存された検索の一覧から選択できるようになります。 **[プレビュー]** をクリックすると、保存された検索でコンピューターをプレビューできます。

![Azure 以外のための [グループの選択] ページ (プレビュー) および右側に [プレビュー] ペインを表示するスクリーンショット。](./media/configure-groups/select-groups-2.png)

## <a name="next-steps"></a>次のステップ

[Azure Monitor ログに対してクエリを実行](query-logs.md)すると、更新プログラムの評価、展開、およびその他の関連する管理タスクを分析することができます。 これには、作業を開始するために役立つ事前定義されたクエリが含まれています。
