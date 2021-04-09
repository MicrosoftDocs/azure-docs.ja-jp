---
title: Azure Kubernetes Service (AKS) でユーザー定義ルート (UDR) をカスタマイズする
description: Azure Kubernetes Service (AKS) でカスタム エグレス ルートを定義する方法について説明します
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182130"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>ユーザー定義ルートを使用してクラスターのエグレスをカスタマイズする

AKS クラスターからのエグレスは、特定のシナリオに合わせてカスタマイズできます。 AKS の既定では、Standard SKU ロード バランサーがプロビジョニングされ、エグレス用に設定および使用されます。 ただし、パブリック IP が許可されていない場合、またはエグレスに追加のホップが必要な場合、既定の設定ではすべてのシナリオの要件を満たせない可能性があります。

この記事では、クラスターのエグレス ルートをカスタマイズして、パブリック IP を禁止し、クラスターをネットワーク仮想アプライアンス (NVA) の背後に配置することを必須にするなどのカスタム ネットワーク シナリオをサポートする方法について説明します。

## <a name="prerequisites"></a>前提条件
* Azure CLI バージョン 2.0.81 以降
* `2020-01-01` 以降の API バージョン


## <a name="limitations"></a>制限事項
* クラスターの作成時にのみ OutboundType を定義できます。後で更新することはできません。
* `outboundType` を設定するには、`vm-set-type` を `VirtualMachineScaleSets`、`load-balancer-sku` を `Standard` に設定した AKS クラスターが必要です。
* `outboundType` の値を `UDR` に設定するには、クラスターの送信接続が有効なユーザー定義ルートが必要です。
* `outboundType` の値を `UDR` に設定すると、ロード バランサーにルーティングされるイングレス ソース IP がクラスターのエグレス方向の送信先アドレスと **一致しない** 可能性があります。

## <a name="overview-of-outbound-types-in-aks"></a>AKS の送信の種類の概要

AKS クラスターは、種類が `loadBalancer` または `userDefinedRouting` の一意の `outboundType` を使用してカスタマイズできます。

> [!IMPORTANT]
> 送信の種類は、クラスターのエグレス トラフィックにのみ影響します。 詳細については、[イングレス コントローラーの設定](ingress-basic.md)に関する記事を参照してください。

> [!NOTE]
> UDR と Kubernet ネットワークでは、独自の[ルート テーブル][byo-route-table]を使用できます。 クラスター ID (サービス プリンシパルまたはマネージド ID) に、カスタム ルート テーブルへの共同作成者のアクセス許可があることを確認します。

### <a name="outbound-type-of-loadbalancer"></a>loadBalancer の送信の種類

`loadBalancer` が設定されている場合、AKS によって次の設定が自動的に完了します。 ロード バランサーは、AKS に割り当てられたパブリック IP を経由したエグレスに使用されます。 `loadBalancer` の送信の種類は、種類 `loadBalancer` の Kubernetes サービスをサポートします。このサービスでは、AKS リソース プロバイダーによって作成されたロード バランサーからのエグレスが想定されます。

次の構成は、AKS によって行われます。
   * パブリック IP アドレスは、クラスターのエグレス用にプロビジョニングされます。
   * パブリック IP アドレスは、ロード バランサーのリソースに割り当てられます。
   * ロード バランサーのバックエンド プールは、クラスター内のエージェント ノードに設定されます。

既定で AKS クラスターにデプロイされるネットワーク トポロジを次に示します。これには `loadBalancer` の `outboundType` が使用されます。

![イングレス IP とエグレス IP を示す図 (トラフィックはイングレス IP からロード バランサーに送信され、内部クラスター間で送受信され、他のトラフィックはエグレス IP に送信され、インターネット、MCR、Azure の必須サービス、AKS コントロール プレーンに送信されます)。](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>userDefinedRouting の送信の種類

> [!NOTE]
> 送信の種類の使用は高度なネットワーク シナリオであり、適切なネットワーク構成が必要です。

`userDefinedRouting` を設定しても、エグレス パスは AKS によって自動的に構成されません。 エグレス セットアップは、ユーザーが行う必要があります。

AKS クラスターは、事前に構成されたサブネットを持つ既存の仮想ネットワークにデプロイする必要があります。これは、Standard Load Balancer (SLB) アーキテクチャを使用していない場合に、明示的なエグレスを確立する必要があるためです。 このため、このアーキテクチャでは、ファイアウォール、ゲートウェイ、プロキシなどのアプライアンスにエグレス トラフィックを明示的に送信するか、Standard Load Balancer またはアプライアンスに割り当てられたパブリック IP によってネットワーク アドレス変換 (NAT) を実行できるようにする必要があります。

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>UserDefinedRouting を使用したロード バランサーの作成

UDR の送信の種類を使用する AKS クラスターは、種類が "loadBalancer" の最初の Kubernetes サービスがデプロイされている場合にのみ、Standard Load Balancer (SLB) を受信します。 ロード バランサーは、"*受信*" 要求用のパブリック IP アドレスと、"*受信*" 要求用のバックエンド プールで構成されます。 受信規則は Azure クラウド プロバイダーによって構成されますが、送信の種類が UDR であるため、**送信パブリック IP アドレスも送信規則も構成されません**。 UDR は引き続きエグレス トラフィックの唯一のソースとなります。

Azure ロード バランサーでは、[規則が設定されるまでは料金が発生しません](https://azure.microsoft.com/pricing/details/load-balancer/)。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>UDR および Azure Firewall の送信の種類を使用してクラスターをデプロイする

送信の種類としてユーザー定義ルートを使用するクラスターのアプリケーションについて説明するために、仮想ネットワークがそのサブネット上にある Azure ファイアウォールにクラスターを構成できます。 この例については、[Azure ファイアウォールを使用したエグレス トラフィックの制限の例](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)を参照してください。

> [!IMPORTANT]
> UDR の送信の種類には、ルート テーブルに 0.0.0.0/0 へのルートと NVA の次のホップの宛先 (ネットワーク仮想アプライアンス) が存在している必要があります。
> ルート テーブルには、SNAT へのパブリック IP がないインターネットへの既定の 0.0.0.0/0 が既に存在します。このルートを追加しても、エグレスは提供されません。 AKS は、インターネットを指す 0.0.0.0.0/0 のルートを作成せず、NVA やゲートウェイなどを指すルートを作成したことを検証します。UDR の送信の種類を使用する場合、サービスの種類 **loadbalancer** が構成されていない限り、*受信要求* 用のロード バランサーのパブリック IP アドレスは作成されません。 UDR の送信の種類が設定されている場合、**送信要求** 用のパブリック IP アドレスが AKS によって作成されることはありません。

## <a name="next-steps"></a>次のステップ

[Azure ネットワークの UDR の概要](../virtual-network/virtual-networks-udr-overview.md)に関する記事を参照してください。

[ルート テーブルの作成、変更、削除の方法](../virtual-network/manage-route-table.md)に関する記事を参照してください。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
