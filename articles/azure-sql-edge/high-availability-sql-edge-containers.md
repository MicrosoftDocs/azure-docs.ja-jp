---
title: Azure SQL Edge コンテナーの高可用性 - Azure SQL Edge
description: Azure SQL Edge コンテナーの高可用性について説明します
keywords: SQL Edge, コンテナー, 高可用性
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90930929"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Azure SQL Edge コンテナーの高可用性

Kubernetes でネイティブに Azure SQL Edge インスタンスを作成して管理します。 Azure SQL Edge を [Kubernetes](https://kubernetes.io/) によって管理される docker コンテナーにデプロイします。 Kubernetes では、クラスター ノードで障害が発生した場合に、Azure SQL Edge インスタンスを含むコンテナーを自動的に回復できます。 Kubernetes 永続ボリューム要求 (PVC) を使用して SQL Edge コンテナー イメージを構成できます。 Kubernetes は、コンテナー内の Azure SQL Edge プロセスをモニターします。 プロセス、ポッド、コンテナー、またはノードで障害が発生した場合、Kubernetes は自動的に別のインスタンスをブートストラップし、ストレージに再接続します。

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kubernetes の Azure SQL Edge コンテナー

Kubernetes 1.6 以降では、"[*ストレージ クラス*](https://kubernetes.io/docs/concepts/storage/storage-classes/)" と "[*永続ボリューム要求*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)" がサポートされています。

この構成では、Kubernetes はコンテナー オーケストレーターの役割を果たします。 

![Kubernetes クラスター内の Azure SQL Edge の図](media/deploy-kubernetes/kubernetes-sql-edge.png)

上の図で、`azure-sql-edge` は[ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod/)内のコンテナーです。 Kubernetes により、クラスター内のリソースが調整されます。 [レプリカ セット](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)によって、ノード障害が発生した後でポッドが自動的に復旧されます。 アプリケーションがサービスに接続します。 このケースでは、サービスは、`azure-sql-edge` の障害後も変化しない IP アドレスがホストされているロード バランサーを表します。

次の図では、`azure-sql-edge` コンテナーで障害が発生しています。 オーケストレーターとしての Kubernetes により、レプリカ セット内に適切な数の正常なインスタンスが存在することが保証され、構成に従って新しいコンテナーが開始されます。 オーケストレーターによって同じノード上で新しいポッドを開始され、`azure-sql-edge` によって同じ永続ストレージに再接続されます。 サービスは、再作成された `azure-sql-edge` に接続されます。

![ポッドで障害が発生した後の Kubernetes クラスター内の Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

次の図では、`azure-sql-edge` コンテナーがホストされているノードで障害が発生しています。 オーケストレーターによって異なるノード上で新しいポッドを開始され、`azure-sql-edge` によって同じ永続ストレージに再接続されます。 サービスは、再作成された `azure-sql-edge` に接続されます。

![ノードで障害が発生した後の Kubernetes クラスター内の Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Kubernetes にコンテナーを作成するには、「[Kubernetes に Azure SQL Edge コンテナーをデプロイする](deploy-Kubernetes.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Azure Kubernetes Service (AKS) で Azure SQL Edge コンテナーをデプロイするには、次の記事を参照してください。
- [Kubernetes で Azure SQL Edge コンテナーをデプロイする](deploy-Kubernetes.md)
- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)。
- [IoT Edge を使用して SQL Edge でエンド ツー エンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)