---
title: Azure VMware Solution by CloudSimple - サービス
description: この概要で、CloudSimple サービスについて説明します。 このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898744"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple サービスの概要

Azure VMware Solution by CloudSimple は、CloudSimple サービスを通じて利用できます。  このサービスを作成することで、ノードの購入、予約、プライベート クラウドの作成が可能になります。  CloudSimple サービスは、それが提供されている各 Azure リージョンに作成することになります。 このサービスでは、Azure VMware Solution by CloudSimple のエッジ ネットワークが定義されます。 VPN、ExpressRoute、プライベート クラウドへのインターネット接続などのサービスは、このエッジ ネットワークによってサポートされます。

## <a name="gateway-subnet"></a>ゲートウェイ サブネット

CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。  ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。  いったん作成したゲートウェイ サブネットは削除できません。  ゲートウェイ サブネットは、サービスが削除されたときに削除されます。

## <a name="next-steps"></a>次の手順

* [Azure で CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)方法を学習します。
