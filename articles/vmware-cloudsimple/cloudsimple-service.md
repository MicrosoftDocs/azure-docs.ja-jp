---
title: CloudSimple サービスによる VMware ソリューション
description: CloudSimple サービスの概要と概念について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595313"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple サービスの概要

CloudSimple サービスでは、CloudSimple による Azure VMware ソリューションを使用できます。 サービスを作成した後、ノードをプロビジョニングしたり、ノードを予約したり、プライベート クラウドを作成したりできます。 CloudSimple サービスは、それが提供されている各 Azure リージョンに作成することになります。 このサービスでは、Azure VMware Solution by CloudSimple のエッジ ネットワークが定義されます。 VPN、Azure ExpressRoute、プライベート クラウドへのインターネット接続などのサービスは、このエッジ ネットワークによってサポートされます。

## <a name="gateway-subnet"></a>ゲートウェイ サブネット

CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。 作成した後でゲートウェイ サブネットを削除することはできません。 ゲートウェイ サブネットは、サービスが削除されたときに削除されます。

## <a name="next-steps"></a>次の手順

* [Azure で CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)方法を学習します。
