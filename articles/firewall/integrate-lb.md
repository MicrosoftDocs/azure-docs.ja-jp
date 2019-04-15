---
title: Azure Firewall と Azure Standard Load Balancer を統合する
description: Azure Firewall と Azure Standard Load Balancer を統合する方法について説明します
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805246"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure Firewall と Azure Standard Load Balancer を統合する

Azure Firewall と Azure Standard Load Balancer (パブリックまたは内部) を仮想ネットワークに統合できます。 

内部ロード バランサーと Azure Firewall の統合は、設計が大幅に簡素化されるという点で推奨されます。 パブリック ロード バランサーが既にデプロイされていて、今後もその環境を維持したい場合は、そのパブリック ロード バランサーを使用できます。 ただし、パブリック ロード バランサーのシナリオでは、機能が無効になる可能性がある非対称ルーティングの問題を認識しておく必要があります。

Azure Load Balancer の詳細については、[Azure Load Balancer とは何か](../load-balancer/load-balancer-overview.md)に関する記事を参照してください。

## <a name="public-load-balancer"></a>パブリック ロード バランサー

パブリック ロード バランサーを使用する場合、ロード バランサーはパブリック フロントエンド IP アドレスを使用してデプロイされます。

### <a name="asymmetric-routing"></a>非対称ルーティング

非対称ルーティングとは、パケットが送信先に到達するために通過するパスと、送信元に戻るために通過するパスが異なるルーティングです。 この問題は、サブネットの既定のルートにファイアウォールのプライベート IP アドレスが含まれているときに、パブリック ロード バランサーを使用した場合に発生します。 この場合、ロード バランサーの受信トラフィックはパブリック IP アドレス経由で受信されますが、復路のパスはファイアウォールのプライベート IP アドレスを通過します。 ファイアウォールはステートフルであり、そのような確立済みのセッションを認識しないため、返されるパケットは破棄されます。

### <a name="fix-the-routing-issue"></a>ルーティングの問題を修正する

Azure Firewall をサブネットにデプロイするときの 1 つの手順は、AzureFirewallSubnet 上のファイアウォールのプライベート IP アドレスを通過するようにパケットを方向づけるサブネット用の既定のルートを作成することです。 詳細については、[チュートリアル: Deploy and configure Azure Firewall using the Azure portal (チュートリアル: Azure portal を使用して Azure Firewall のデプロイと構成を行う)](tutorial-firewall-deploy-portal.md#create-a-default-route)」を参照してください。

ロード バランサーのシナリオにファイアウォールを導入するときに、インターネット トラフィックがファイアウォールのパブリック IP アドレス経由で到着するように設定できます。 そこから、ファイアウォールによってファイアウォール規則が適用され、パケットがロードバランサーのパブリック IP アドレスに NAT されます。 ここで問題が発生します。 パケットはファイアウォールのパブリック IP アドレスに到着しますが、プライベート IP アドレス経由でファイアウォールに戻ります (既定のルートが使用されます)。
この問題を回避するには、ファイアウォールのパブリック IP アドレス用の追加のホスト ルートを作成します。 ファイアウォールのパブリック IP アドレスに到着するパケットは、インターネット経由でルーティングされます。 これにより、ファイアウォールのプライベート IP アドレスへの既定のルートの使用が回避されます。

![非対称ルーティング](media/integrate-lb/Firewall-LB-asymmetric.png)

たとえば、パブリック IP アドレスが 13.86.122.41 で、プライベート IP アドレスが 10.3.1.4 のファイアウォールのルートは次のようになります。

![ルート テーブル](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>内部ロード バランサー

内部ロード バランサーを使用する場合、ロード バランサーはプライベート フロントエンド IP アドレスでデプロイされます。

このシナリオでは、非対称ルーティングの問題はありません。 受信パケットはファイアウォールのパブリック IP アドレスに到着し、ロード バランサーのプライベート IP アドレスに変換された後、同じ復路のパスを使用してファイアウォールのプライベート IP アドレスに戻ります。

そのため、このシナリオはパブリック ロード バランサーのシナリオと同じようにデプロイできますが、ファイアウォールのパブリック IP アドレスのホスト ルートは必要ありません。

## <a name="additional-security"></a>追加のセキュリティ

負荷分散シナリオのセキュリティを強化するために、ネットワーク セキュリティ グループ (NSG) を使用できます。

たとえば、負荷分散される仮想マシンが配置されているバックエンドのサブネット上に NSG を作成できます。 ファイアウォールの IP アドレスまたはポートからの受信トラフィックが許可されます。

NSG について詳しくは、「[セキュリティ グループ](../virtual-network/security-overview.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。