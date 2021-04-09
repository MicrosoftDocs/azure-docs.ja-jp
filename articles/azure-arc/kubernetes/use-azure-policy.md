---
title: Azure Policy を使用してクラスター構成を大規模に適用する
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy を使用してクラスター構成を大規模に適用する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121458"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Azure Policy を使用してクラスター構成を大規模に適用する

Azure Policy を使用すると、Azure Arc 対応 Kubernetes クラスター (`Microsoft.Kubernetes/connectedclusters`) に構成 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) を大規模に適用できます。

Azure Policy を使用するには、既存のポリシー定義を選択して、ポリシーの割り当てを作成します。 ポリシー割り当てを作成する場合は、次の手順を実行します。
1. 割り当てのスコープを設定します。
    * スコープは、Azure リソース グループまたはサブスクリプションになります。 
2. 作成される構成に対してパラメーターを設定します。 

割り当てが作成されると、Azure Policy エンジンによって、スコープ内にあるすべての Azure Arc 対応 Kubernetes クラスターが識別され、各クラスターに構成が適用されます。

複数のポリシー割り当てを使用して、それぞれ異なる Git リポジトリをポイントする複数の構成を作成できます。 たとえば、中央の IT またはクラスター オペレーター用に 1 つのリポジトリと、アプリケーション チーム用に他のリポジトリとすることができます。

## <a name="prerequisite"></a>前提条件

このポリシー割り当てを作成するスコープ (サブスクリプションまたはリソース グループ) に対する `Microsoft.Authorization/policyAssignments/write` アクセス許可があることをご確認ください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

1. Azure portal で **[ポリシー]** に移動します。
1. サイドバーの **[作成]** セクションで **[定義]** を選択します。
1. "Kubernetes" カテゴリの "GitOps を Kubernetes クラスターにデプロイする" 組み込みポリシーを選択します。 
1. **[割り当て]** をクリックします。
1. **[スコープ]** を、ポリシー割り当てを適用する管理グループ、サブスクリプション、またはリソース グループに設定します。
    * ポリシーのスコープからリソースを除外する場合は、 **[除外]** を設定します。
1. ポリシー割り当てにわかりやすい **[名前]** と **[説明]** を指定します。
1. **[ポリシーの適用]** が "**有効**" に設定されていることを確認します。
1. **[次へ]** を選択します。
1. `sourceControlConfiguration` の作成時に使用するパラメーター値を設定します。
1. **[次へ]** を選択します。
1. **[修復タスクを作成する]** を有効にします。
1. **[マネージド ID を作成します]** がオンになっていること、および ID に **共同作成者** アクセス許可が付与されることを確認します。 
    * 詳細については、[ポリシー割り当ての作成のクイック スタート](../../governance/policy/assign-policy-portal.md)および [Azure Policy に準拠していないリソースの修復に関する記事](../../governance/policy/how-to/remediate-resources.md)を参照してください。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

ポリシー割り当てを作成すると、ポリシー割り当てのスコープ内で作成された新しい Azure Arc 対応 Kubernetes クラスターに構成が適用されます。

既存のクラスターに対しては、修復タスクを手動で実行する必要があります。 通常、このタスクには、ポリシー割り当てが有効になるまで 10 ～ 20 分かかります。

## <a name="verify-a-policy-assignment"></a>ポリシーの割り当てを確認する

1. Azure portal で、Azure Arc 対応 Kubernetes クラスターのいずれかに移動します。
1. サイドバーの **[設定]** セクションで、 **[ポリシー]** を選択します。 
    * ポリシー一覧に、前に作成した、 **[コンプライアンスの状態]** が *[準拠している]* に設定されているポリシーの割り当てが表示されます。
1. サイドバーの **[設定]** セクションで、 **[構成]** を選択します。
    * 構成の一覧に、ポリシー割り当てによって作成された構成が表示されます。
1. `kubectl` を使用してクラスターを問い合わせます。 
    * 構成リソースによって作成された名前空間と成果物が表示されます。
    * (クラスターが Azure にネットワーク接続されていると仮定して) 5 分以内に、Git リポジトリのマニフェストで記述されているオブジェクトがクラスター上に作成されていることを確認する必要があります。

## <a name="next-steps"></a>次のステップ

[Azure Arc 対応 Kubernetes クラスターを使用して Azure Monitor for Containers を設定する](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)。
