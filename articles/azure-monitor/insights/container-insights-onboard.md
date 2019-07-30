---
title: Azure Monitor for containers を有効にする方法 | Microsoft Docs
description: この記事では、コンテナーがどのように動作し、パフォーマンスに関してどのような問題が特定されているかを把握できるように、Azure Monitor for containers を有効にして構成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2019
ms.author: magoedte
ms.openlocfilehash: aff2dcebdab1ad93b8b1958164764b66eb755d1c
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514494"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor for containers を有効にする方法

この記事では、Kubernetes 環境にデプロイされ、[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) 上でホストされているワークロードのパフォーマンスを監視するために、コンテナーに対して Azure Monitor を設定するために使用できるオプションの概要について説明します。

次のサポートされている方法を使用して、新規または 1 つ以上の既存の AKS のデプロイに対してコンテナー用の Azure Monitor 有効にできます。

* Azure Portal、Azure PowerShell、または Azure CLI から
* [Terraform と AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) を使用して

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
始める前に、必ず以下のものを用意してください。

* **Log Analytics ワークスペース。**

    Azure Monitor for containers は、**US Gov バージニア**を除き、Azure [のリージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンで Log Analytics ワークスペースをサポートします。

    新しい AKS クラスターの監視を有効にするときにワークスペースを作成すること、またはオンボード エクスペリエンスを使用して AKS クラスター サブスクリプションの既定のリソース グループに既定のワークスペースを作成することができます。 自分でワークスペースを作成する場合は、[Azure Resource Manager](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../learn/quick-create-workspace.md) のいずれかを使用して作成できます。 既定のワークスペースに使用される、サポートされているマッピング ペアの一覧については、[Azure Monitor for containers のリージョンのマッピング](container-insights-region-mapping.md)に関するページを参照してください。

* コンテナーの監視を有効にするために、**Log Analytics 共同作成者ロール**のメンバーである必要があります。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../platform/manage-access.md)」を参照してください。

* AKS クラスター リソースに対する **[[所有者]](../../role-based-access-control/built-in-roles.md#owner)** ロールのメンバーである必要があります。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>コンポーネント

パフォーマンスを監視する能力は、コンテナーの Azure Monitor 用に特化して開発された、Linux 用のコンテナー化 Log Analytics エージェントに依存します。 この特殊なエージェントは、クラスター内のすべてのノードからパフォーマンスとイベント データを収集します。エージェントは自動的にデプロイされ、デプロイ時に指定した Log Analytics ワークスペースに登録されます。 このエージェントのバージョンは microsoft/oms:ciprod04202018 以降であり、*mmddyyyy* という形式の日付で表されます。

>[!NOTE]
>AKS の Windows Server サポートのプレビュー リリースでは、Windows サーバー ノードを持つ AKS クラスターには、データを収集して Azure Monitor に転送するためのエージェントがインストールされていません。 代わりに、標準のデプロイの一部としてクラスターに自動的にデプロイされる Linux ノードが、クラスターのすべての Windows ノードに代わってデータを収集し、Azure Monitor に転送します。  
>

エージェントの新しいバージョンがリリースされた場合でも、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされることはありません。 リリースされたバージョンを確認するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。

>[!NOTE]
>AKS クラスターが既にデプロイされている場合は、この記事の後半で説明されているように、Azure CLI または提供されている Azure Resource Manager テンプレートを使用して、監視を有効にします。 `kubectl` を使用してアップグレード、エージェントを削除、再展開、または展開することはできません。
>テンプレートはクラスターと同じリソース グループ内に展開する必要があります。

以下の表で説明されている次の方法のいずれかを使用して、コンテナーの Azure Monitor を有効にします。

| デプロイの状態 | Method | 説明 |
|------------------|--------|-------------|
| 新しい AKS クラスター | [Azure CLI を使用してクラスターを作成](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| | [Terraform を使用してクラスターを作成](container-insights-enable-new-cluster.md#enable-using-terraform)| オープンソースのツールである Terraform を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| 既存の AKS クラスター | [Azure CLI を使用して有効にする](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| |[Terraform を使用して有効にする](container-insights-enable-existing-clusters.md#enable-using-terraform) | オープンソースのツールである Terraform を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| | [Azure Monitor から有効にする](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor の AKS マルチクラスター ページから既にデプロイされている 1 つまたは複数の AKS クラスターの監視を有効にできます。 |
| | [AKS クラスターから有効にする](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure portal の AKS クラスターから直接監視を有効にできます。 |
| | [Azure Resource Manager テンプレートを使用して有効にする](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 事前構成済みの Azure Resource Manager テンプレートを使用して AKS クラスターの監視を有効にできます。 |

## <a name="next-steps"></a>次の手順

* AKS クラスター ノードとポッドの両方について正常性メトリックを取得するための監視が有効になったので、これらの正常性メトリックを Azure portal で利用できます。 コンテナー用 Azure Monitor を使用する方法については、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
