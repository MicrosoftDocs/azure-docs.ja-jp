---
title: Azure Site Recovery を使用して Azure VM を別のリージョンに移動する
description: Azure Site Recovery を使用して、異なる Azure リージョン間で Azure VM を移動します。
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90007368"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure VM を別の Azure リージョンに移動する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure VM を別の Azure リージョンに移動する理由とその手順について概要を示します。 


## <a name="reasons-to-move-azure-vms"></a>Azure VM を移動する理由

ユーザーが VM を移動する理由としては、次のケースが考えられます。

- 既にデプロイ済みのリージョンがあるとき、既存のアプリケーションまたはサービスのエンド ユーザーにより近いリージョンが新たにサポートされた場合。 このシナリオでは、待機時間の短縮を図るために、既存の VM をそのまま新しいリージョンに移動したいと考えるでしょう。 サブスクリプションを統合したい場合や、ガバナンス/組織のルール上、移動が必要となる場合にも、同じ手法を用います。
- 単一インスタンス VM として、または可用性セットの一部として、VM がデプロイされた場合。 可用性 SLA を向上させたい場合は、VM を可用性ゾーンに移動します。

## <a name="move-vms-with-resource-mover"></a>Resource Mover を使用して VM を移動する

[Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md) を使用して、VM を別のリージョンに移動することができるようになりました。 Resource Mover は、現在、パブリック プレビュー段階にあり、以下を実現します。
- 単一のハブを使用してリージョン間でリソースを移動できます。
- 移動時間と複雑さを軽減できます。 必要なものをすべて 1 か所にまとめることができます。
- シンプルかつ一貫性のあるエクスペリエンスによって、さまざまな種類の Azure リソースを移動できます。
- 移動するリソース間の依存関係を簡単に識別できます。 これにより、関連リソースをまとめて移動することができるため、移動後にすべてのリソースがターゲット リージョンで期待どおりに動作します。
- 移動後にソース リージョンのリソースを削除する場合は、自動的にクリーンアップされます。
- テスト。 移動を試してみて、完全に移動することは望まない場合は破棄することができます。



## <a name="move-vms-with-site-recovery"></a>Site Recovery を使用して VM を移動する

Site Recovery を使用して VM を移動するには、次の手順を実行します。

1. 前提条件を確認します。
2. ソース VM を準備します。
3. ターゲット リージョンを準備します。
4. ターゲット リージョンにデータをコピーします。 Azure Site Recovery のレプリケーション テクノロジを使用して、ソース VM からターゲット リージョンにデータをコピーします。
5. 構成をテストします。 レプリケーションが完了したら、非運用ネットワークへのテスト フェールオーバーを実行して構成をテストします。
6. 移動を実行します。
7. ソース リージョンのリソースを破棄します。

> [!NOTE]
> これらの手順の詳細については、以下のセクションで説明します。
> [!IMPORTANT]
> 現在 Azure Site Recovery では、異なるリージョン間の VM の移動はサポートされますが、同じリージョン内での移動はサポートされません。

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多層デプロイの一般的なアーキテクチャ

このセクションでは、Azure における多層アプリケーションの最も一般的なデプロイ アーキテクチャについて説明します。 ここでは、パブリック IP を持つ 3 階層アプリケーションの例を取り上げています。 各階層 (Web、アプリケーション、データベース) は、それぞれ 2 つの VM を有しており、Azure ロード バランサーによって他の階層に接続されています。 データベース層の VM 間には SQL Server Always On レプリケーションが存在し、高可用性が確保されています。

* **単一インスタンスの VM を各種階層にデプロイ**: 階層内の各 VM は単一インスタンスの VM として構成され、ロード バランサーによって他の階層に接続されます。 この構成は最も簡単に採用できます。

     ![階層間での単一インスタンスの VM デプロイを移動するための選択](media/move-vm-overview/regular-deployment.png)

* **各階層の VM を可用性セットにデプロイ**: 階層内の各 VM は可用性セットに構成されます。 [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)を使うことで、Azure にデプロイする VM は、クラスター内で切り離された複数のハードウェア ノードに確実に分散されます。 これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。

     ![可用性セットへの VM のデプロイ](media/move-vm-overview/avset.png)

* **各階層の VM を Availability Zones にデプロイ**: 階層内の各 VM は [Availability Zones](../availability-zones/az-overview.md) に構成されます。 Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 たとえば、Azure リージョンの 3 つのゾーンに 3 つ以上の VM を作成する場合、VM は実際には 3 つの障害ドメインと 3 つの更新ドメインに分散されます。 Azure プラットフォームは更新ドメインへのこの分散を認識し、異なるゾーン内の VM が同時に更新されないようにします。

     ![可用性ゾーンのデプロイ](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>VM をそのままターゲット リージョンに移動する

前述の[アーキテクチャ](#typical-architectures-for-a-multi-tier-deployment)をベースに、ターゲット リージョンに VM をそのまま移動した後のデプロイの概観を次に示します。

* **単一インスタンスの VM を各種階層にデプロイ**
* **各階層の VM を可用性セットにデプロイ**
* **各階層の VM を Availability Zones にデプロイ**

## <a name="move-vms-to-increase-availability"></a>VM を移動して可用性を高める

* **単一インスタンスの VM を各種階層にデプロイ**

     ![階層への単一インスタンスの VM のデプロイ](media/move-vm-overview/single-zone.png)

* **各階層の VM を可用性セットにデプロイ**: Azure Site Recovery を使用して VM のレプリケーションを有効にすると、可用性セット内の VM を個別の Availability Zones に構成できます。 移動操作の完了後、可用性の SLA は 99.99% になります。

     ![可用性セットと Availability Zones への VM のデプロイ](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> 
> * [Azure VM を別のリージョンに移動する](azure-to-azure-tutorial-migrate.md)
> 
> * [Azure VM を Availability Zones に移動する](move-azure-vms-avset-azone.md)

