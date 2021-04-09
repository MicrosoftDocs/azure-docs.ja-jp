---
title: Azure Arc 対応 Kubernetes に関してよく寄せられる質問
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事には、Azure Arc 対応 Kubernetes に関してよく寄せられる質問の一覧が記載されています。
keywords: Kubernetes, Arc, Azure, コンテナー, 構成, GitOps, faq
ms.openlocfilehash: d7facb842509bdf852957d46d91dfcb3912e99ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121713"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>よく寄せられる質問 - Azure Arc 対応 Kubernetes

この記事では、Azure Arc 対応 Kubernetes についてよく寄せられる質問を記載します。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Azure Arc 対応 Kubernetes と Azure Kubernetes Service (AKS) の違いは何ですか?

AKS は、Azure によるマネージド Kubernetes オファリングです。 AKS は、複雑さと運用上のオーバーヘッドの多くを Azure にオフロードすることで、Azure でのマネージド Kubernetes クラスターのデプロイを簡素化します。 Kubernetes マスターは Azure によって管理されるので、お客様はエージェント ノードの管理と保守のみを行います。

Azure Arc 対応 Kubernetes を使用すると、Kubernetes クラスターを Azure に接続することで、Azure の管理機能 (Azure Monitor や Azure Policy など) を拡張できます。 ユーザーは、基になる Kubernetes クラスター自体を管理します。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Azure で実行されている AKS クラスターを Azure Arc に接続する必要はありますか?

いいえ。 Azure Monitor や Azure Policy (Gatekeeper) を含む Azure Arc 対応 Kubernetes のすべての機能は、AKS (Azure Resource Manager のネイティブ リソース) 上で利用できます。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Azure Stack Hub と Azure Stack Edge 上の AKS-HCI クラスターと Kubernetes クラスターを Azure Arc に接続する必要がありますか?

はい。Azure Stack Edge または Azure Stack Hub 上の AKS-HCI クラスターまたは Kubernetes クラスターを Azure Arc に接続することで、Azure Resource Manager のリソース表現がクラスターに提供されます。 このリソースの表現により、クラスターの構成、Azure Monitor、Azure Policy (Gatekeeper) などの機能が、接続された Kubernetes クラスターに拡張されます。

Azure Arc 対応 Kubernetes クラスターが Azure Stack Edge、AKS on Azure Stack HCI (2021 年 4 月以降の更新プログラム)、または AKS on Windows Server 2019 Datacenter (2021 年 4 月以降の更新プログラム) 上にある場合、Kubernetes の構成は無料で提供されます。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>期限切れの Azure Arc 対応 Kubernetes リソースの処理方法は?

Azure Arc 対応 Kubernetes に関連付けられているマネージド サービス ID (MSI) 証明書の有効期限は 90 日です。 この証明書の有効期限が切れると、リソースは `Expired` であると見なされ、このクラスターですべての機能 (構成、監視、ポリシーなど) が停止します。 Kubernetes クラスターが Azure Arc でもう一度機能するようにするには

1. クラスター上の Azure Arc 対応 Kubernetes リソースとエージェントを削除します。 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. クラスターにエージェントをデプロイして、Azure Arc 対応 Kubernetes リソースを再作成します。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` を使用すると、クラスター上の構成も削除されます。 `az connectedk8s connect` を実行した後に、手動で、または Azure Policy を使用して、クラスター上で構成を再作成します。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>CI/CD パイプラインを既に使用している場合でも、Azure Arc 対応 Kubernetes と構成を使用できますか?

はい。その場合でも CI/CD パイプラインを介してデプロイを受け取るクラスターで構成を使用できます。 従来の CI/CD パイプラインと比較すると、構成で 2 つの追加メリットが得られます。

**ドリフト調整**

CI/CD パイプラインでは、パイプラインの実行中に変更が 1 回だけ適用されます。 ただし、クラスター上の Kubernetes リソースの望ましい状態を取得するために、クラスター上の GitOps オペレーターによって Git リポジトリが継続的にポーリングされます。 GitOps オペレーターによって、リソースの望ましい状態がクラスター上のリソースの実際の状態とは異なることが検出されると、このドリフトが調整されます。

**GitOps が全体的に適用される**

CI/CD パイプラインは、Kubernetes クラスターへのイベント ドリブン デプロイ (Git リポジトリへのプッシュなど) に役立ちます。 ただし、すべての Kubernetes クラスターに同じ構成をデプロイする場合は、各 Kubernetes クラスターの資格情報を CI/CD パイプラインに手動で構成する必要があります。 

Azure Arc 対応 Kubernetes の場合、Azure Resource Manager によって構成が管理されるので、Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープ内のすべての Azure Arc 対応 Kubernetes リソースで同じ構成の作成を自動化することができます。 この機能は、ポリシーの割り当て後に作成された Azure Arc 対応 Kubernetes リソースにも適用できます。

この機能は、コンプライアンスとガバナンスの要件を満たすために、Kubernetes クラスターのインベントリ全体でベースライン構成 (ネットワーク ポリシー、ロール バインド、ポッド セキュリティ ポリシーなど) に適用されます。

## <a name="next-steps"></a>次のステップ

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./connect-cluster.md)します。
* Kubernetes クラスターが既に Azure Arc に接続されていますか? [Arc 対応 Kubernetes クラスターの構成を作成する](./use-gitops-connected-cluster.md)。
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)方法について学ぶ。