---
title: ISE のパブリック発信 IP アドレスを設定する
description: Azure Logic Apps で統合サービス環境 (ISE) に対して単一のパブリック発信 IP アドレスを設定する方法について学習します。
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211058"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Azure Logic Apps で 1 つまたは複数の統合サービス環境に対して単一の IP アドレスを設定する

Azure Logic Apps を使用する場合は、[Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内のリソースにアクセスする必要があるロジック アプリをホストするために、[*統合サービス環境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を設定できます。 IP 制限がある他のエンドポイントへのアクセスを必要とする ISE インスタンスが複数ある場合は、[Azure Firewall](../firewall/overview.md) または[ネットワーク仮想アプライアンス](../virtual-network/virtual-networks-overview.md#filter-network-traffic)を仮想ネットワークにデプロイし、そのファイアウォールまたはネットワーク仮想アプライアンスを経由して送信トラフィックをルーティングします。 その後、仮想ネットワーク内のすべての ISE インスタンスで、1 つの予測可能な静的パブリック IP アドレスを使用して、目的の送信先システムとの通信を行うことができます。 このようにすると、送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

このトピックでは Azure Firewall 経由で送信トラフィックをルーティングする方法について説明しますが、Azure Marketplace からのサードパーティのファイアウォールなどのネットワーク仮想アプライアンスにも、同様の概念を適用できます。 このトピックでは、複数の ISE インスタンスの設定に焦点を当てていますが、アクセスする必要がある IP アドレスの数をシナリオで制限する必要がある場合は、1 つの ISE に対してこの方法を使用することもできます。 ファイアウォールまたは仮想ネットワーク アプライアンスの追加コストが、シナリオにとって妥当かどうかを検討してください。 詳細については、「[Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* ISE と同じ仮想ネットワークで実行される Azure Firewall。 ファイアウォールがない場合は、まず、仮想ネットワークに `AzureFirewallSubnet` という名前の[サブネットを追加](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)します。 その後、仮想ネットワークに[ファイアウォールを作成してデプロイ](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)することができます。

* Azure [ルート テーブル](../virtual-network/manage-route-table.md)。 ない場合は、まず[ルート テーブルを作成](../virtual-network/manage-route-table.md#create-a-route-table)します。 ルーティングの詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

## <a name="set-up-route-table"></a>ルート テーブルを設定する

1. [Azure portal](https://portal.azure.com)で、ルート テーブルを選択します。次に例を示します。

   ![送信トラフィックの送信用ルールを含むテーブルを選択する](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. [新しいルートを追加する](../virtual-network/manage-route-table.md#create-a-route)には、ルート テーブル メニューで、 **[ルート]**  >  **[追加]** の順に選択します。

   ![送信トラフィックの送信用ルートを追加する](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. **[ルートの追加]** ウィンドウで、送信先システムへのすべての送信トラフィックは次の動作に従うというルールで、[新しいルートを設定](../virtual-network/manage-route-table.md#create-a-route)します。

   * [**仮想アプライアンス**](../virtual-network/virtual-networks-udr-overview.md#user-defined)を次ホップの種類として使用する。

   * 次ホップ アドレスとして、ファイアウォール インスタンスのプライベート IP アドレスに移動する。

     この IP アドレスを見つけるには、ファイアウォールのメニューで、 **[概要]** を選択し、 **[プライベート IP アドレス]** の下にあるアドレスを見つけます。次に例を示します。

     ![ファイアウォールのプライベート IP アドレスを検索する](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   このようなルールがどのように表示されるのか、次に例を示します。

   ![送信トラフィックの送信用ルールを設定する](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **ルート名** | <*一意のルート名*> | ルート テーブル内のルートの一意の名前 |
   | **アドレス プレフィックス** | <*送信先アドレス*> | 送信トラフィックの送信先システムのアドレス プレフィックス。 このアドレスに必ず、[クラスレス ドメイン間ルーティング (CIDR) 表記](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)を使用してください。 この例では、このアドレス プレフィックスは SFTP サーバー用です。これについては、「[ネットワーク ルールを設定する](#set-up-network-rule)」セクションで説明しています。 |
   | **次ホップの種類** | **仮想アプライアンス** | 送信トラフィックで使用される [ホップの種類](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **次ホップ アドレス** | <*ファイアウォール プライベート IP アドレス*> | ファイアウォールのプライベート IP アドレス |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>ネットワーク ルールを設定する

1. Azure portal で、ご利用のファイアウォールを探して選択します。 ファイアウォールのメニューで、 **[設定]** の下の **[ルール]** を選択します。 ルールのウィンドウで、 **[ネットワーク ルール コレクション]**  >  **[ネットワーク ルール コレクションの追加]** の順に選択します。

   ![ネットワーク ルール コレクションをファイアウォールに追加する](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. コレクションで、送信先システムへのトラフィックを許可するルールを追加します。

   たとえば、ISE で実行され、SFTP サーバーと通信する必要があるロジック アプリがあるとします。 `ISE_SFTP_Outbound` という名前のネットワーク ルールが含まれた、`LogicApp_ISE_SFTP_Outbound` という名前のネットワーク ルール コレクションを作成します。 このルールは、トラフィックが仮想ネットワークで ISE が実行されているサブネットの IP アドレスから、ファイアウォールのプライベート IP アドレスを使用して送信先 SFTP サーバーへ送られることを許可します。

   ![ファイアウォールのネットワーク ルールを設定する](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **ネットワーク ルール コレクションのプロパティ**

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **名前** | <*ネットワーク ルール コレクション名*> | ネットワーク ルール コレクションの名前 |
   | **優先順位** | <*優先順位のレベル*> | ルール コレクションを実行するために使用する優先順位。 詳細については、「[Azure Firewall の概念をいくつか教えてください。](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)」を参照してください。 |
   | **操作** | **許可** | このルールのために実行するアクションの種類 |
   |||

   **ネットワーク ルールのプロパティ**

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **名前** | <*ネットワーク ルール名*> | ネットワーク ルールの名前 |
   | **プロトコル** | <*接続プロトコル*> | 使用する接続プロトコル。 たとえば、NSG ルールを使用している場合は、 **[TCP]** だけではなく、 **[TCP]** と **[UDP]** の両方を選択します。 |
   | **ソース アドレス** | <*ISE サブネット アドレス*> | ISE が実行されているサブネットの IP アドレスと、ロジック アプリからのトラフィックの発生元 |
   | **送信先アドレス** | <*送信先 IP アドレス*> | 送信トラフィックの送信先システムの IP アドレス。 この例では、この IP アドレスは SFTP サーバー用です。 |
   | **送信先ポート** | <*送信先ポート*> | 送信先システムが受信通信に使用するポート |
   |||

   ネットワーク ルールの詳細については、次の記事を参照してください。

   * [ネットワーク ルールを構成する](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure Firewall ルール処理ロジック](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall に関する FAQ](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
