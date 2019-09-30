---
title: Azure VMware Solution by CloudSimple - サービス
description: CloudSimple サービスの概要と概念について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877659"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple サービスの概要

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。  このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。  CloudSimple サービスは、それが提供されている各 Azure リージョンに作成することになります。 このサービスでは、Azure VMware Solution by CloudSimple のエッジ ネットワークが定義されます。 VPN、ExpressRoute、プライベート クラウドへのインターネット接続などのサービスは、このエッジ ネットワークによってサポートされます。

## <a name="gateway-subnet"></a>ゲートウェイ サブネット

CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。  ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。  いったん作成したゲートウェイ サブネットは削除できません。  ゲートウェイ サブネットは、サービスが削除されたときに削除されます。

## <a name="next-steps"></a>次の手順

* [Azure で CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)方法を学習します。
