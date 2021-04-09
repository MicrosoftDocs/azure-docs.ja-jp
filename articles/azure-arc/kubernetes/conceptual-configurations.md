---
title: 構成と GitOps - Azure Arc 対応 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事では、Azure Arc 対応 Kubernetes の GitOps と構成の機能の概念的な概要を示します。
keywords: Kubernetes, Arc, Azure, コンテナー, 構成, GitOps
ms.openlocfilehash: a4193783a976f205b46cb8c5df29d4cef9da1d2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022876"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes に関する構成と GitOps

Kubernetes に関する場合、GitOps は、Git リポジトリで Kubernetes クラスター構成 (デプロイ、名前空間など) の望ましい状態を宣言する方法です。 この宣言の後に、オペレーターを使用してこれらのクラスター構成をポーリングし、プルベースのデプロイを実行します。 Git リポジトリには以下を含めることができます。
* Namespace、ConfigMap、Deployment、DaemonSet などの有効な Kubernetes リソースを記述する YAML 形式のマニフェスト。
* アプリケーションをデプロイするための Helm チャート。

GitOps の空間でよく使用されるオープンソース ツールである [Flux](https://docs.fluxcd.io/) を Kubernetes クラスターにデプロイすることにより、Git リポジトリから Kubernetes クラスターへの構成フローを容易にすることができます。 Flux では、クラスターと名前空間の両方のスコープで、そのオペレーターのデプロイがサポートされます。 名前空間スコープでデプロイされた flux オペレーターの場合、その特定の名前空間内の Kubernetes オブジェクトのみをデプロイできます。 クラスターか名前空間のどちらかのスコープを選択できることで、同じ Kubernetes クラスターでのマルチテナント デプロイ パターンを実現できます。

## <a name="configurations"></a>構成

[ ![構成のアーキテクチャ](./media/conceptual-configurations.png) ](./media/conceptual-configurations.png#lightbox)

クラスターと Git リポジトリの間の接続は、Azure Resource Manager の Azure Arc 対応 Kubernetes リソース (`Microsoft.Kubernetes/connectedClusters` によって表されます) 上に構成リソース (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) として作成されます。 

適切なパラメーター (マニフェストのプル元の Git リポジトリや、マニフェストをプルするポーリング間隔など) を指定した構成リソース プロパティを使用して、クラスターに Flux オペレーターをデプロイします。 構成リソース データは、保存時に暗号化されて Azure Cosmos DB データベースに格納されるので、データの機密性が確保されます。

クラスターで実行されている `config-agent` は、次の役割を担います。
* Azure Arc 対応 Kubernetes リソースで、新規または更新された構成リソースを追跡する。
* 構成リソースごとに Flux オペレーターをデプロイして Git リポジトリを監視する。
* 構成リソースに対して行われた更新を適用する。 

複数の名前空間スコープの構成リソースを同じ Azure Arc 対応 Kubernetes クラスター上に作成し、マルチテナントを実現することができます。

> [!NOTE]
> * `config-agent` は、Arc 対応 Kubernetes リソースで使用できる新規または更新された構成リソースかないか監視します。 そのため、目的の状態をクラスターにプルできるように、エージェントには接続が必要です。 エージェントが Azure に接続できない場合は、目的の状態をクラスターに反映する際に遅延が発生します。
> * 秘密キー、既知のホストのコンテンツ、HTTPS ユーザー名、トークン/パスワードなどの機密性の高い顧客入力は、Azure Arc 対応 Kubernetes サービスでは 48 時間以上保存されません。 構成で機密性の高い入力を使用している場合は、クラスターをできるだけ一定の間隔でオンラインにします。

## <a name="apply-configurations-at-scale"></a>構成を大規模に適用する

Azure Resource Manager によって構成が管理されるので、Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープ内のすべての Azure Arc 対応 Kubernetes リソースで同じ構成の作成を自動化することができます。 

このように大規模に適用することで、共通のベースライン構成 (ClusterRoleBindings、RoleBindings、NetworkPolicy などの構成を含む) を、Azure Arc 対応 Kubernetes クラスターのフリートまたはインベントリ全体に適用できます。

## <a name="next-steps"></a>次のステップ

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)します。
* Kubernetes クラスターが既に Azure Arc に接続されていますか? [Arc 対応 Kubernetes クラスターの構成を作成する](./tutorial-use-gitops-connected-cluster.md)。
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)方法について学ぶ。