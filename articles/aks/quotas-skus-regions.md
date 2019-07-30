---
title: Azure Kubernetes Service (AKS) のクォータ、SKU、およびリージョンの可用性
description: Azure Kubernetes Service (AKS) の既定のクォータ、制限されたノード VM SKU サイズ、およびリージョンの可用性について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614547"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。 特定の仮想マシン (VM) の SKU も使用が制限されています。

この記事では、Azure Kubernetes Service (AKS) リソースに対する既定のリソースの制限と、Azure リージョンでの AKS の可用性について詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>プロビジョニングされるインフラストラクチャ

その他のすべてのネットワーク、コンピューティング、およびストレージの制限が、プロビジョニングされるインフラストラクチャに適用されます。 関連する制限については、[Azure サブスクリプションとサービスの制限](../azure-subscription-service-limits.md)に関するページを参照してください。

> [!IMPORTANT]
> AKS クラスターをアップグレードすると、追加のリソースが一時的に使用されます。 これらのリソースには、仮想ネットワークのサブネット内で利用可能な IP アドレスや、仮想マシンの vCPU クォータが含まれます。 Windows Server コンテナーを使用する場合 (現在 AKS でプレビュー中)、ノードに最新の更新を適用するために推奨される唯一の方法は、アップグレード操作を実行することです。 クラスターのアップグレード プロセスが失敗した場合は、これらの一時的なリソースを処理するために使用できる IP アドレス空間または vCPU クォータがないことを示している可能性があります。 Windows Server ノードのアップグレード プロセスの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="restricted-vm-sizes"></a>制限されている VM サイズ

AKS クラスター内の各ノードには、vCPU やメモリなどの一定量のコンピューティング リソースが含まれています。 AKS ノード内のコンピューティング リソースが不十分な場合、ポッドが正しく実行できないことがあります。 必要な *kube システム*のポッドおよびアプリケーションのスケジュールを確実に設定するには、AKS で次の VM SKU を使用しないでください。

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

特定の既定の制限とクォータを増やすことができます。 リソースで増加がサポートされている場合は、[Azure サポート リクエスト][azure-support]を使用して増加を要求してください ( **[問題の種類]** で **[クォータ]** を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
