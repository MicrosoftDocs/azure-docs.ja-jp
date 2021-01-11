---
title: Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy を使用してクラスター構成を大規模に適用する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050034"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy を使用してクラスター構成を大規模に適用する (プレビュー)

## <a name="overview"></a>概要

Azure Policy を使用して、各 `Microsoft.Kubernetes/connectedclusters` リソース、または GitOps 対応の `Microsoft.ContainerService/managedClusters` リソースに特定の `Microsoft.KubernetesConfiguration/sourceControlConfigurations` が適用されるように強制します。 Azure Policy を使用するには、既存のポリシー定義を選択して、ポリシーの割り当てを作成します。 ポリシーの割り当てを作成する場合、割り当てのスコープを設定します。これは、Azure リソース グループまたはサブスクリプションになります。 また、作成する `sourceControlConfiguration` のパラメーターも設定します。 割り当てを作成すると、スコープ内のすべての `connectedCluster` または `managedCluster` リソースをポリシー エンジンが識別し、それぞれに対して `sourceControlConfiguration` を適用します。

各クラスターの信頼できるソースとして複数の Git リポジトリを使用している場合 (たとえば、1 つのリポジトリは中央 IT/クラスター オペレーター用、別のリポジトリはアプリケーション チーム用として使用している場合)、異なる Git リポジトリを使用するように各ポリシー割り当てを構成して、複数のポリシー割り当てを使用することにより、これを有効にすることができます。

## <a name="prerequisite"></a>前提条件

このポリシー割り当てを作成するスコープ (サブスクリプションまたはリソース グループ) に対する `Microsoft.Authorization/policyAssignments/write` アクセス許可があることを確認します。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

1. Azure portal で、[ポリシー] に移動し、サイドバーの **[作成]** セクションで、 **[定義]** を選択します。
2. "Kubernetes" カテゴリの "GitOps を Kubernetes クラスターにデプロイする" 組み込みポリシーを選択し、 **[割り当て]** クリックします。
3. **[スコープ]** を、ポリシーの割り当てを適用する管理グループ、サブスクリプション、またはリソース グループに設定します。
4. ポリシーのスコープからリソースを除外する場合は、 **[除外]** を設定します。
5. ポリシーの割り当てに、簡単に識別するために使用できる **[名前]** と **[説明]** を設定します。
6. **[ポリシーの適用]** が、"*有効*" に設定されていることを確認します。
7. **[次へ]** を選択します。
8. `sourceControlConfiguration` の作成時に使用されるパラメーター値を設定します。
9. **[次へ]** を選択します。
10. **[修復タスクを作成する]** を有効にします。
11. **[マネージド ID を作成します]** がオンになっていること、および ID に **共同作成者**アクセス許可が付与されることを確認します。 必要なアクセス許可の詳細については、[こちらのドキュメント](../../governance/policy/assign-policy-portal.md)および[こちらのドキュメントのコメント](../../governance/policy/how-to/remediate-resources.md)を参照してください。
12. **[Review + create]\(レビュー + 作成\)** を選択します。

ポリシーの割り当てを作成した後、割り当てのスコープ内にあるすべての新しい `connectedCluster` リソース (または GitOps エージェントがインストールされている `managedCluster` リソース) に対して、`sourceControlConfiguration` が適用されます。 既存のクラスターに対しては、修復タスクを手動で実行する必要があります。 通常、ポリシーの割り当てが有効になるまで 10 から 20 分かかります。

## <a name="verify-a-policy-assignment"></a>ポリシーの割り当てを確認する

1. Azure portal で、`connectedCluster` リソースのいずれかに移動し、サイドバーの **[設定]** セクションで、 **[ポリシー]** を選択します (AKS クラスターの UX はまだ実装されていませんが、実装される予定です)。
2. 一覧に、上記で作成したポリシーの割り当てが表示され、 **[コンプライアンスの状態]** は "*準拠している*" になっています。
3. サイドバーの **[設定]** セクションで、 **[構成]** を選択します。
4. 一覧に、ポリシーの割り当てで作成された `sourceControlConfiguration` が表示されます。
5. **kubectl** を使用して、クラスターに問い合わせます。`sourceControlConfiguration` によって作成された名前空間と成果物が表示されます。
6. 5 分以内に、構成された Git リポジトリ内のマニフェストで説明されている成果物がクラスターに表示されます。

## <a name="next-steps"></a>次のステップ

* [Arc 対応 Kubernetes クラスターを使用して Azure Monitor for Containers を設定する](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
