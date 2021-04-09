---
title: Azure Policy を使用して VM insights を有効にする
description: Azure Policy を使用して、複数の Azure 仮想マシンまたは仮想マシン スケール セットで VM insights を有効にする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 51baf009543208fbbfe091238d0215a24761641d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031958"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Azure Policy を使用して VM insights を有効にする
この記事では、Azure Policy を使用して、Azure 仮想マシンまたは Azure Arc (プレビュー) に接続されたハイブリッド仮想マシンで VM insights を有効にする方法について説明します。 Azure Policy を使用すると、Azure 環境全体にわたって VM insights に必要なエージェントをインストールし、各仮想マシンが作成されたら VM に対する監視を自動的に有効にするポリシー定義を割り当てることができます。 VM insights には、環境内の準拠していない VM を検出して修復できる機能が用意されています。 この機能は、Azure Policy を直接操作する代わりに使用します。

Azure Policy に精通していない場合は、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](../deploy-scale.md)」にある簡単な概要を参照してください。

> [!NOTE]
> Azure 仮想マシン スケール セットで Azure Policy を使用するか、または Azure 仮想マシンを有効にするために Azure Policy を直接操作するには、「[Azure Policy を使用して大規模に Azure Monitor をデプロイする](../deploy-scale.md#vm-insights)」を参照してください。

## <a name="prerequisites"></a>前提条件
- [Log Analytics ワークスペースを作成して構成](./vminsights-configure-workspace.md)します。
- 「[サポートされるオペレーティング システム](./vminsights-enable-overview.md#supported-operating-systems)」を参照して、有効にする仮想マシンまたは仮想マシン スケール セットのオペレーティング システムがサポートされていることを確認してください。 


## <a name="vm-insights-initiative"></a>VM insights のイニシアティブ
VM insights には、Azure 仮想マシンに Log Analytics エージェントと依存関係エージェントをインストールするための組み込みのポリシー定義が用意されています。 イニシアティブ **[Enable VM insights]\(VM insights を有効にする\)** には、これらの各ポリシー定義が含まれています。 このイニシアティブを管理グループ、サブスクリプション、またはリソース グループに割り当てて、そのスコープ内のすべての Windows または Linux Azure 仮想マシンにエージェントを自動的にインストールします。

## <a name="open-policy-coverage-feature"></a>ポリシー カバレッジ機能を開く
**[VM insights ポリシー対象範囲]** にアクセスするには、Azure portal で **[Azure Monitor]** メニューの **[仮想マシン]** に移動します。 **[その他のオンボード オプション]** を選択してから、 **[ポリシーを使用して有効にする]** の **[有効化]** を選択します。

[![Azure Monitor from VMs の [はじめに] タブ](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>新しい割り当てを作成する
まだ割り当てがない場合は、 **[ポリシーの割り当て]** をクリックして新しい割り当てを作成します。

[![割り当てを作成する](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

これは、Azure Policy でイニシアティブを割り当てるのと同じページですが、選択したスコープと **[Enable VM insights]\(VM insights を有効にする\)** イニシアティブの定義がハードコードされている点が異なります。 必要に応じて、 **[割り当て名]** を変更したり、 **[説明]** を追加したりできます。 スコープに対する除外を指定する場合は、 **[除外]** を選択します。 たとえば、スコープが管理グループである場合は、その管理グループ内のあるサブスクリプションを割り当てから除外するように指定できます。

[![イニシアティブを割り当てる](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

**[パラメーター]** ページで、割り当て内のすべての仮想マシンによって使用される **[Log Analytics ワークスペース]** を選択します。 仮想マシンごとに異なるワークスペースを指定する場合は、それぞれ独自のスコープを持つ複数の割り当てを作成する必要があります。 

   > [!NOTE]
   > ワークスペースが割り当てのスコープの範囲外にある場合は、ポリシー割り当てのプリンシパル ID に "*Log Analytics 共同作成者*" アクセス許可を付与します。 これを行わないと、次のようなデプロイ エラーが表示されることがあります。`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![ワークスペース](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

**[作成]** をクリックして作成する前に割り当ての詳細を確認するには、 **[確認と作成]** をクリックします。 既存の仮想マシンを有効にするには複数の修復タスクが必要になる可能性が高いため、この時点では修復タスクを作成しないでください。 後の「[コンプライアンスの結果を修復する](#remediate-compliance-results)」を参照してください。

## <a name="review-compliance"></a>コンプライアンスを確認する
割り当てが作成されたら、管理グループやサブスクリプションにまたがる **[Enable VM insights]\(VM insights を有効にする\)** イニシアティブの対象範囲を確認して管理できます。 これにより、各管理グループまたはサブスクリプションに存在する仮想マシンの数や、それらのコンプライアンス状態が示されます。

[![VM insights のポリシーの管理ページ](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


次の表は、このビューにある情報の説明を示しています。

| 機能 | 説明 | 
|----------|-------------| 
| **スコープ** | 現在保有しているか、または管理グループ階層を通してドリル ダウンする機能を使用してアクセスする権限を継承した管理グループおよびサブスクリプション。|
| **ロール** | スコープ内のロール。これは、閲覧者、所有者、共同作成者のいずれかです。 サブスクリプションにはアクセスできるが、それが属している管理グループにアクセスできない場合、これは空白になります。 このロールによって、表示できるデータや、ポリシーまたはイニシアティブの割り当て (所有者)、それらの編集、またはコンプライアンスの表示に関して実行できるアクションが決定されます。 |
| **[Total VMs] (VM の総数)** | そのスコープ内の VM の合計数 (各 VM の状態には関係ありません)。 管理グループの場合、これは、サブスクリプションまたは子管理グループの下で入れ子になっている VM の総数です。 |
| **[Assignment Coverage] (割り当て対象範囲)** | イニシアティブの対象となる VM の割合 (%)。 |
| **[Assignment Status] (割り当て状態)** | **[成功]** - スコープ内のすべての VM に Log Analytics エージェントと依存関係エージェントがデプロイされています。<br>**[警告]** - サブスクリプションが管理グループの下にありません。<br>**[未開始]** - 新しい割り当てが追加されました。<br>**[ロック]** - 管理グループに対する十分な特権がありません。<br>**[空白]** - VM が存在しないか、またはポリシーが割り当てられていません。 |
| **[Compliant VMs] (準拠している VM)** | 準拠している VM の数。これは、Log Analytics エージェントと依存関係エージェントの両方がインストールされている VM の数です。 割り当てがないか、スコープ内に VM が存在しないか、またはアクセス許可が適切でない場合、これは空白になります。 |
| **コンプライアンス** | 全体的なコンプライアンスの数値は、準拠している個別のリソースの総数をすべての個別のリソースの総数で割った値になります。 |
| **コンプライアンスの状態** | **[準拠している]** - スコープ内のすべての VM に Log Analytics エージェントと依存関係エージェントがデプロイされているか、または割り当てに従うスコープ内のどの新しい VM もまだ評価されていません。<br>**[準拠していない]** - 既に評価されているが、有効になっておらず、修復が必要な可能性がある VM が存在します。<br>**[未開始]** - 新しい割り当てが追加されました。<br>**[ロック]** - 管理グループに対する十分な特権がありません。<br>**[空白]** - ポリシーが割り当てられていません。  |


イニシアティブを割り当てる場合、割り当てで選択されたスコープは、一覧表示されているスコープまたはそのサブセットである可能性があります。 たとえば、管理グループ (対象範囲スコープ) ではなく、サブスクリプション (ポリシー スコープ) の割り当てを作成している可能性があります。 この場合、 **[割り当て対象範囲]** の値は、イニシアティブ スコープ内の VM 数を対象範囲スコープ内の VM 数で割った値を示します。 あるいは、ご自分がポリシー スコープからいくつかの VM、リソース グループ、またはサブスクリプションを除外している可能性があります。 値が空白の場合は、ポリシーまたはイニシアチブが存在しないか、アクセス許可がないことを示します。 情報は **[割り当ての状態]** の下に表示されます。


## <a name="remediate-compliance-results"></a>コンプライアンスの結果を修復する
イニシアティブは、作成または変更されると仮想マシンに適用されますが、既存の VM には適用されません。 割り当てが 100% のコンプライアンスを示していない場合は、既存の VM を評価して有効にするための修復タスクを作成し、省略記号 (...) を選択して **[コンプライアンスの表示]** を選択します。

[![[コンプライアンスの表示]](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

**[コンプライアンス]** ページには、指定されたフィルターに一致する割り当てと、それが準拠しているかどうかが一覧表示されます。 割り当てをクリックすると、その詳細が表示されます。

[![Azure VM のポリシーのコンプライアンス](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

**[Initiative compliance] (イニシアティブのコンプライアンス)** ページには、イニシアティブのポリシー定義と、各ポリシー定義が準拠しているかどうかが一覧表示されます。

[![コンプライアンスの詳細](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

ポリシー定義をクリックすると、その詳細が表示されます。 ポリシー定義が準拠していないと表示されるシナリオには、次のものが含まれます。

* Log Analytics エージェントも依存関係エージェントデプロイされていない。 緩和するための修復タスクを作成します。
* VM イメージ (OS) が、ポリシー定義で識別されない。 デプロイ ポリシーの基準には、よく知られている Azure VM イメージからデプロイされた VM のみが含まれます。 VM の OS がサポートされているかどうか、ドキュメントで確認します。
* VM が、指定された Log Analytics ワークスペースにログインしていない。 イニシアティブ スコープ内の一部の VM が、ポリシー割り当てで指定されているもの以外の Log Analytics ワークスペースに接続されています。

[![ポリシーのコンプライアンスの詳細](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

コンプライアンスの問題を緩和するための修復タスクを作成するには、 **[修復タスクの作成]** をクリックします。 

[![新しい修復タスク](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

**[修復]** をクリックして修復タスクを作成した後、 **[修復]** をクリックしてそれを起動します。 ほとんどの場合は、複数の修復タスク (ポリシー定義ごとに 1 つ) の作成が必要になります。 イニシアティブに対して修復タスクを作成することはできません。

[![モニターのポリシーの修復ウィンドウのスクリーンショット | 仮想マシン。](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


修復タスクが完了すると、VM はエージェントがインストールされた準拠した状態になり、VM insights が有効になります。 

## <a name="next-steps"></a>次のステップ

これで、仮想マシンに対する監視が有効になったので、この情報を VM insights での分析に使用できます。 

- 検出されたアプリケーションの依存関係を表示するには、[VM insights マップの表示](vminsights-maps.md)に関する記事を参照してください。 
- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事をご覧ください。
