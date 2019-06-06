---
title: Azure Kubernetes Service (AKS) のクォータ、SKU、およびリージョンの可用性
description: Azure Kubernetes Service (AKS) の既定のクォータ、制限されたノード VM SKU サイズ、およびリージョンの可用性について説明します。
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 04/09/2019
ms.date: 05/13/2019
ms.author: v-yeche
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072865"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性

すべての Azure サービスには、リソースと機能に対する特定の既定の制限とクォータが含まれます。 ノード サイズについては、特定の仮想マシン (VM) の SKU が使用を制限されています。

この記事では、Azure Kubernetes Service (AKS) リソースに対する既定のリソースの制限と、Azure リージョンでの AKS サービスの可用性について詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>プロビジョニングされるインフラストラクチャ

その他のすべてのネットワーク、コンピューティング、およびストレージの制限が、プロビジョニングされるインフラストラクチャに適用されます。 関連する制限については、[Azure サブスクリプションとサービスの制限](../azure-subscription-service-limits.md)に関するページを参照してください。

## <a name="restricted-vm-sizes"></a>制限されている VM サイズ

AKS クラスター内の各ノードには、vCPU やメモリなどの一定量のコンピューティング リソースが含まれています。 AKS ノード内のコンピューティング リソースが不十分な場合、ポッドが正しく実行できないことがあります。 必要な *kube システム*のポッドおよびアプリケーションのスケジュールを確実に設定できるように、AKS で次の VM SKU を使用できます。

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM の種類とそのコンピューティング リソースの詳細については、[Azure での仮想マシンのサイズ][vm-skus]に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

クラスターをデプロイおよび実行できる場所の最新のリストについては、[AKS リージョンの可用性][region-availability]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

特定の既定の制限とクォータを増やすことができます。 このような増加に対応する 1 つまたは複数のリソースの増加を要求する場合は、[Azure サポート リクエスト][azure-support]を送信してください (**[問題の種類]** で [クォータ] を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://support.azure.cn/zh-cn/support/support-azure/
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
