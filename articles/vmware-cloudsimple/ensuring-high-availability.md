---
title: Azure 上で VMware を実行する場合のアプリケーション高可用性の確保
description: CloudSimple プライベート クラウド内で実行されているアプリケーションの一般的なアプリケーション エラー シナリオに対処するための CloudSimple 高可用性機能について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895752"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Azure 上で VMware を実行する場合のアプリケーション高可用性の確保

CloudSimple ソリューションでは、Azure 環境内の VMware 上で実行されているアプリケーションの高可用性が実現されます。 次の表に、障害のシナリオと、関連する高可用性機能を示します。

| 障害のシナリオ | アプリケーションが保護されているか? | プラットフォーム HA 機能 | VMware HA 機能 | Azure HA 機能 |
------------ | ------------- | ------------ | ------------ | ------------- |
| ディスク障害 | YES | 障害が発生したノードの高速置換 | [vSAN の既定のストレージ ポリシーについて](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| ファン障害 | YES | 冗長性のあるファン、障害が発生したノードの高速置換 |  |  |
| NIC の障害 | YES | 冗長性のある NIC、障害が発生したノードの高速置換
| ホストの電源障害 | YES | 冗長電源 |  |  |
| ESXi ホスト障害 | YES | 障害が発生したノードの高速置換 | [VMware vSphere の高可用性](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM の障害 | YES | [ロード バランサー](load-balancers.md)  | [VMware vSphere の高可用性](https://www.vmware.com/products/vsphere/high-availability.html) | ステートレス VMware VM 用の Azure Load Balancer |
| リーフ スイッチ ポートの障害 | YES | 冗長性のある NIC |  |  |
| リーフ スイッチの障害 | YES | 冗長性のあるリーフ スイッチ |  |  |
| ラックの障害 | YES | 配置グループ |  |  |
| オンプレミスの DC へのネットワーク接続 | YES  | 冗長性のあるネットワーク サービス |  | 冗長性のある ER 回線 |
| Azure へのネットワーク接続 | YES | |  | 冗長性のある ER 回線 |
| データセンターの障害 | YES |  |  | 可用性ゾーン |
| リージョンの障害 | YES  |  |  | Azure Azure リージョン |

Azure VMware Solution by CloudSimple には、以下の高可用性機能が用意されています。

## <a name="fast-replacement-of-failed-node"></a>障害が発生したノードの高速置換

CloudSimple コントロール プレーン ソフトウェアでは、VMware クラスターの正常性が継続的に監視され、ESXi ノードで障害が発生したときに、それが検出されます。 次に、すぐに利用可能なノードのプールから、影響を受ける VMware クラスターに新しい ESXi ホストが自動的に追加され、障害が発生したノードがクラスターから除外されます。 この機能により、vSAN と VMware HA によって提供されるクラスターの回復性が復元されるように、VMware クラスター内の予備容量が迅速に復元されます。

## <a name="placement-groups"></a>配置グループ

プライベート クラウドを作成するユーザーは、Azure リージョンと、選択したリージョン内の配置グループを選択できます。 配置グループは、複数のラックに分散されているが同じスパイン ネットワーク セグメント内にあるノードのセットです。 同じ配置グループ内のノードは、最大 2 つの追加のスイッチ ホップを使用して相互に接続できます。 配置グループは、常に 1 つの Azure 可用性ゾーン内にあり、複数のラックにまたがります。 CloudSimple コントロール プレーンでは、ベスト エフォートに基づいて、プライベート クラウドのノードが複数のラックに分散されます。 異なる配置グループ内のノードは、異なるラックに配置されることが保証されます。

## <a name="availability-zones"></a>可用性ゾーン

可用性ゾーンとは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内の特殊な物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 各リージョンに 1 つの可用性ゾーンがあります。 詳しくは、「[Azure の可用性ゾーンの概要](../availability-zones/az-overview.md)」をご覧ください。

## <a name="redundant-azure-expressroute-circuits"></a>冗長性のある Azure ExpressRoute 回線

ExpressRoute を使用した Azure vNet へのデータ センター接続には、高可用性ネットワーク接続リンクを提供するための冗長回線があります。

## <a name="redundant-networking-services"></a>冗長性のあるネットワーク サービス

プライベート クラウド用のすべての CloudSimple ネットワーク サービス (VLAN、ファイアウォール、パブリック IP アドレス、インターネット、VPN など) は、高可用性を実現し、サービス SLA をサポートできるように設計されています。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>ステートレス VMware VM 用の Azure Layer 7 Load Balancer

ユーザーは、Web 層の高可用性を実現するために、VMware 環境内で実行されているステートレス Web 層 VM の前に Azure Layer 7 Load Balancer を配置できます。

## <a name="azure-regions"></a>Azure Azure リージョン

Azure リージョンは、待ち時間で定義された境界内でデプロイされ、待ち時間の短い専用リージョン ネットワークを介して接続された一連のデータ センターです。 詳しくは、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions)」をご覧ください。
