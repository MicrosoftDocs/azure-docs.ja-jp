---
title: Azure portal を使用した VM へのポートの開放
description: Azure portal を使用して VM へのポートを開き、エンドポイントを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550452"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure Portal を使用して仮想マシンへのポートを開く方法
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. VM のリソース グループを検索して選択し、 **[追加]** を選択して、 **[ネットワーク セキュリティ グループ]** を検索して選択します。

1. **［作成］** を選択します

    **[ネットワーク セキュリティ グループの作成]** ウィンドウが開きます。

    ![ネットワーク セキュリティ グループの作成](./media/nsg-quickstart-portal/create-nsg.png)

1. ネットワーク セキュリティ グループの名前を入力します。 

1. リソース グループを選択するか作成して、場所を選択します。

1. **[作成]** を選択し、ネットワーク セキュリティ グループを作成します。

## <a name="create-an-inbound-security-rule"></a>受信セキュリティ規則の作成

1. 新しいネットワーク セキュリティ グループを選択します。 

1. 左側のメニューの **[受信セキュリティ規則]** を選択してから、 **[追加]** を選択します。

    ![[詳細設定] ページを開く](./media/nsg-quickstart-portal/advanced.png)

1. **[受信セキュリティ規則を追加する]** ページで、ページの上部にある **[基本]** から **[詳細設定]** に切り替えます。 

1. **[サービス]** ボックスの一覧で共通のサービスを選択します (たとえば、 **[HTTP]** )。 使用する特定のポートを指定する場合、 **[カスタム]** を選択することもできます。 

1. 必要に応じて、 **[優先度]** または **[名前]** を変更します。 優先度は、ルールが適用される順序に影響します。数値が小さいほど、ルールが早く適用されます。

1. **[追加]** を選択し、規則を作成します。

## <a name="associate-your-network-security-group-with-a-subnet"></a>ネットワーク セキュリティ グループとサブネットの関連付け

最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。 この例では、ネットワーク セキュリティ グループをサブネットに関連付けます。 

1. 左側のメニューから **[サブネット]** を選択し、 **[Associate]\(関連付け\)** を選択します。

1. 仮想ネットワークを選択し、適切なサブネットを選択します。

    ![ネットワーク セキュリティ グループと仮想ネットワークの関連付け](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. 終了したら、 **[OK]** を選択します。

## <a name="additional-information"></a>関連情報

[この記事の手順は、Azure PowerShell を使用して実行する](nsg-quickstart-powershell.md)こともできます。

この記事で説明しているコマンドを使用すると、VM にフローするトラフィックをすばやく取得できます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳しくは、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../../virtual-network/tutorial-filter-network-traffic.md)」をご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置することを考慮してください。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure 内で Windows 仮想マシンの負荷分散を行って高可用性アプリケーションを作成する](tutorial-load-balancer.md)方法に関するチュートリアルをご覧ください。

## <a name="next-steps"></a>次のステップ
この記事では、ネットワーク セキュリティ グループの作成、ポート 80 で HTTP トラフィックを許可する受信規則の作成、その規則とサブネットの関連付けを行いました。 

より精密な環境の作成については、次の記事で確認できます。
- [Azure リソース マネージャーの概要](../../azure-resource-manager/management/overview.md)
- [セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)
