---
title: Azure 仮想ネットワークでサブネットの委任を追加または削除する
titlesuffix: Azure Virtual Network
description: Azure のサービスの委任されたサブネットを追加または削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175945"
---
# <a name="add-or-remove-a-subnet-delegation"></a>サブネットの委任を追加または削除する

サブネット委任では、サービスのデプロイ時に一意の ID を利用してサブネットにサービス固有のリソースを作成するための明示的なアクセス許可がサービスに与えられます。 この記事では、Azure サービスの委任されたサブネットを追加または削除する方法について説明します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-the-virtual-network"></a>仮想ネットワークの作成

このセクションでは、仮想ネットワークと、後で Azure サービスに委任するサブネットを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
1. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 名前 | 「*MyVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.0.0.0/16*」と入力します。 |
    | Subscription | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | Location | **[EastUS]** を選択します。|
    | サブネット - 名前 | 「*mySubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.0.0.0/24*」と入力します。 |
    |||
1. 残りは既定値のままにして、 **[作成]** を選択します。

## <a name="permissons"></a>アクセス許可

Azure サービスに委任するサブネットを作成しなかった場合は、次のアクセス許可が必要です: `Microsoft.Network/virtualNetworks/subnets/write`。

組み込みの[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールには、必要なアクセス許可も含まれています。

## <a name="delegate-a-subnet-to-an-azure-service"></a>サブネットを Azure サービスに委任する

このセクションでは、前のセクションで作成したサブネットを Azure サービスに委任します。

1. ポータルの検索バーに「*myVirtualNetwork*」と入力します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 検索結果で、 *[myVirtualNetwork]* を選択します。
3. **[設定]** で **[サブネット]** を選択し、次に **[mySubnet]** を選択します。
4. *[mySubnet]* ページで、 **[Subnet delegation]\(サブネットの委任\)** 一覧に対し、 **[Delegate subnet to a service]\(サブネットをサービスに委任\)** に一覧表示されているサービスから選択します (たとえば、**Microsoft.DBforPostgreSQL/serversv2**)。  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Azure サービスからサブネットの委任を削除する

1. ポータルの検索バーに「*myVirtualNetwork*」と入力します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 検索結果で、 *[myVirtualNetwork]* を選択します。
3. **[設定]** で **[サブネット]** を選択し、次に **[mySubnet]** を選択します。
4. *[mySubnet]* ページで、 **[Subnet delegation]\(サブネットの委任\)** 一覧に対し、 **[Delegate subnet to a service]\(サブネットをサービスに委任\)** に一覧表示されているサービスから **[None]\(なし\)** を選択します。 

## <a name="next-steps"></a>次の手順
- [Azure でサブネットを管理する](virtual-network-manage-subnet.md)方法を確認します。
