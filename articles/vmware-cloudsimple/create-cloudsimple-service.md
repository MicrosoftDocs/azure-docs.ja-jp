---
title: Azure VMware Solution by CloudSimple - CloudSimple サービスの作成
description: Azure portal で CloudSimple サービスを作成する方法について説明します。 開始する前に、必要な構成を確認してください。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 53f2d0fc9f73985bd70792c8c3b7607eb4c560fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896296"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Azure VMware Solution by CloudSimple サービスの作成

Azure VMware Solution by CloudSimple を開始するには、Azure portal で Azure VMware Solution by CloudSimple サービスを作成します。

## <a name="before-you-begin"></a>開始する前に

ゲートウェイ サブネットに /28 CIDR ブロックを割り当てます。 CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットは、エッジ ネットワーク サービスを作成するときに使用され、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。 CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
    ![CloudSimple サービスを検索する](media/create-cloudsimple-service-search.png)
3. **CloudSimple サービス** を選択します。
4. 新しいサービスを作成するには、**Add** (追加) をクリックします。
    ![CloudSimple サービスを追加する](media/create-cloudsimple-service-add.png)
5. CloudSimple サービスを作成するサブスクリプションを選択します。
6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、**[Create New]** (新規作成) をクリックします。
7. サービスを識別する名前を入力します。
8. サービス ゲートウェイの CIDR を入力します。 オンプレミス サブネット、オンプレミス サブネット、Azure サブネット、計画上の CloudSimple サブネットのいずれとも重複しない /28 サブネットを指定します。 サービスを作成した後は、CIDR を変更できません。

    ![CloudSimple サービスの作成](media/create-cloudsimple-service.png)
9. **[OK]** をクリックします。

サービスが作成され、サービスの一覧に追加されます。

## <a name="next-steps"></a>次の手順

* [ノードをプロビジョニングする](create-nodes.md)方法を学習する
* [プライベート クラウドを作成する](create-private-cloud.md)方法を確認する
* [プライベート クラウド環境を構成する](quickstart-create-private-cloud.md)方法を確認する
