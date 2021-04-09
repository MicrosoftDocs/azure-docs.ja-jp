---
title: Container insights で GPU の監視を構成する | Microsoft Docs
description: この記事では、Container insights を使用して、NVIDIA および AMD の GPU が有効になっているノードが含まれる Kubernetes クラスターの監視を構成する方法について説明します。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731869"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Container insights で GPU の監視を構成する

Container insights に統合されたバージョン *ciprod03022019* 以降のエージェントでは、GPU (グラフィカル処理ユニット) 対応の Kubernetes クラスター ノードでの GPU の使用状況の監視、および GPU リソースを要求および使用しているポッドとコンテナーの監視が、サポートされるようになっています。

## <a name="supported-gpu-vendors"></a>サポートされる GPU ベンダー

Container insights では、次の GPU ベンダーによる GPU クラスターの監視がサポートされています。

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Container insights を使用すると、次のメトリックが 60 秒間隔で収集され、**InsightMetrics** テーブルに格納されることで、ノードでの GPU の使用率および GPU を要求するポッドとワークロードの監視が自動的に開始されます。

>[!NOTE]
>GPU ノードを使用してクラスターをプロビジョニングした後、GPU ワークロードを実行するために AKS に必要とされる [GPU ドライバー](../../aks/gpu-cluster.md)を、確実にインストールしてください。 Container insights により、ノードで実行されている GPU ドライバー ポッドを介して GPU メトリックが収集されます。 

|メトリックの名前 |メトリック ディメンション (タグ) |説明 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor|過去のサンプリング期間 (60 秒) 中に、コンテナーに対して GPU がビジーであるかアクティブに処理を行っていた時間の割合。 デューティ サイクルは 1 から 100 までの値です。 |
|containerGpuLimits |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |各コンテナーでは、1 つまたは複数の GPU として制限を指定できます。 GPU の一部を要求または制限することはできません。 |
|containerGpuRequests |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName |各コンテナーでは、1 つまたは複数の GPU を要求できます。 GPU の一部を要求または制限することはできません。|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |特定のコンテナーに使用できる GPU メモリの量 (バイト)。 |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、containerName、gpuId、gpuModel、gpuVendor |特定のコンテナーに使用された GPU メモリの量 (バイト)。 |
|nodeGpuAllocatable |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |Kubernetes で使用できるノード内の GPU の数。 |
|nodeGpuCapacity |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |ノード内の GPU の合計数。 |

## <a name="gpu-performance-charts"></a>GPU パフォーマンス グラフ 

Container insights には、すべてのクラスターに対する GPU ブックとして、前の表で示したメトリックの事前構成済みのグラフが含まれています。 Container insights で使用できるブックの説明については、「[Container insights のブック](../insights/container-insights-reports.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- GPU が有効なノードを含む AKS クラスターをデプロイする方法については、「[Azure Kubernetes Service (AKS) でコンピューティングを集中的に使用するワークロードに GPU を使用する](../../aks/gpu-cluster.md)」を参照してください。

- [Microsoft Azure での GPU 最適化 VM SKU](../../virtual-machines/sizes-gpu.md) についてさらに学習します。

- [Kubernetes での GPU のサポート](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)を確認し、クラスター内の 1 つまたは複数のノードでの GPU の管理に関する Kubernetes の実験的サポートについてさらに学習します。
