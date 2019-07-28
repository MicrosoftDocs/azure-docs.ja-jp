---
title: SAP HANA on Azure (L インスタンス) 向け追加のネットワーク要件 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) 向け追加のネットワーク要件。
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 953ee5d40a3a4c49d7cc01de804ae5c76ceedc7a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709795"
---
# <a name="additional-network-requirements-for-large-instances"></a>L インスタンス向け追加のネットワーク要件

SAP HANA on Azure の L インスタンスのデプロイの一環として、追加のネットワーク要件があることがあります。

## <a name="add-more-ip-addresses-or-subnets"></a>IP アドレスまたはサブネットの追加

IP アドレスまたはサブネットを追加するには、Azure portal、PowerShell、Azure CLI のいずれかを使用します。

この場合、集約した範囲を新たに作成するのではなく、新しい IP アドレス範囲を新しい範囲として仮想ネットワーク アドレス空間に追加します。 この変更を Microsoft に送信します。 これにより、ご利用のクライアントでその新しい IP アドレス範囲から、HANA L インスタンス ユニットに接続できます。 追加する新しい仮想ネットワーク アドレス空間を取得するために、Azure サポート要求を開くことができます。 確認通知を受信したら、次の手順を実行します。

Azure portal から追加のサブネットを作成するには、[Azure portal を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)に関する記事をご覧ください。 PowerShell からサブネットを作成するには、[PowerShell を使用した仮想ネットワークの作成](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)に関する記事をご覧ください。

## <a name="add-virtual-networks"></a>仮想ネットワークの追加

最初に 1 つ以上の Azure 仮想ネットワークを接続した後、SAP HANA on Azure (L インスタンス) にアクセスする追加の Azure 仮想ネットワークを接続することができます。 まず、Azure サポート要求を送信します。 その要求に、特定の Azure デプロイを識別する特定の情報を含めます。 さらに、IP アドレス空間範囲または Azure 仮想ネットワーク アドレス空間範囲も含めます。 その後、SAP HANA on Microsoft サービス管理から、追加の仮想ネットワークおよび Azure ExpressRoute を接続するために必要な情報が提供されます。 ExpressRoute 回線を HANA L インスタンスに接続するためには、すべての仮想ネットワークに一意の承認キーが必要です。

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅の増加

SAP HANA on Microsoft サービス管理にお問い合わせください。 SAP HANA on Azure (L インスタンス) の ExpressRoute 回線の帯域幅を増やすよう推奨された場合は、Azure サポート要求を作成します (1 つの回線につき、帯域幅を最大 10 Gbps 増やすよう要求することができます)。作業が完了すると通知が届きます。その他の作業は不要で、これで Azure が高速化されます。

## <a name="add-an-additional-expressroute-circuit"></a>ExpressRoute 回線の追加

SAP HANA on Microsoft サービス管理にお問い合わせください。 ExpressRoute 回線を追加するように勧められた場合は、Azure サポート要求 (新しい回線に接続するための承認情報を取得する要求を含む) を作成します。 要求を行う前に、仮想ネットワークで使用するアドレス空間を定義する必要があります。 それにより、SAP HANA on Microsoft サービス管理から承認を提供できます。

新しい回線が作成され、SAP HANA on Microsoft サービス管理の構成が完了した後、作業の続行に必要な情報が記載された通知が届きます。 既に同じ Azure リージョン内の別の SAP HANA on Azure (L インスタンス) ExpressRoute 回線に接続されている Azure 仮想ネットワークは、この追加の回線には接続できません。

## <a name="delete-a-subnet"></a>サブネットの削除

仮想ネットワーク サブネットを削除するには、Azure portal、PowerShell、または Azure CLI を使用できます。 Azure 仮想ネットワークの IP アドレス範囲またはアドレス空間の範囲が集約されている場合、Microsoft のフォローアップはありません (ただし、削除したサブネットを含む BGP ルート アドレス空間は依然として仮想ネットワークから伝播されていることに注意してください)。Azure 仮想ネットワークのアドレス範囲またはアドレス空間を複数の IP アドレス範囲として定義しており、そのいずれかが削除したサブネットに割り当てられていることがあります。 それを仮想ネットワーク アドレス空間から削除してください。 次に、SAP HANA on Microsoft サービス管理に、SAP HANA on Azure (L インスタンス) が通信を許可されている範囲から、それを削除するように通知します。

詳細については、「[サブネットの削除](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)」を参照してください。

## <a name="delete-a-virtual-network"></a>仮想ネットワークの削除

詳細については、「[仮想ネットワークの削除](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)」を参照してください。

SAP HANA on Microsoft サービス管理により、SAP HANA on Azure (L インスタンス) の ExpressRoute 回線に対する既存の承認が削除されます。 さらに、HANA L インスタンスとの通信用の Azure 仮想ネットワーク IP アドレス範囲またはアドレス空間も削除されます。

仮想ネットワークの削除後、Azure サポート要求を開き、削除する IP アドレス空間範囲を指定します。

確実にすべて削除するため、ExpressRoute 接続、仮想ネットワーク ゲートウェイ、仮想ネットワーク ゲートウェイのパブリック IP、および仮想ネットワークを削除します。

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute 回線の削除

SAP HANA on Azure (L インスタンス) の追加の ExpressRoute 回線を削除するには、SAP HANA on Microsoft サービス管理で Azure サポート要求を開きます。 回線の削除を要求します。 Azure サブスクリプション内では、必要に応じて仮想ネットワークを削除または保持することができます。 ただし、HANA L インスタンスの ExpressRoute 回線と、そのリンク先仮想ネットワーク ゲートウェイとを結ぶ接続は削除する必要があります。

## <a name="next-steps"></a>次の手順

- [SAP HANA on Azure (L インスタンス) のインストールと構成の方法](hana-installation.md)
