---
title: SAP HA シナリオでの Azure VM および Standard ILB 用のパブリック エンドポイント接続
description: SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 6f2268ae9a86978e9266ea0e35411727b238d4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671641"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続

この記事では、パブリック エンドポイントへの送信接続を有効にする構成について説明します。 構成は、主に、SUSE/RHEL に対する Pacemaker での高可用性のコンテキストにおけるものです。  

高可用性ソリューションの Azure フェンス エージェントで Pacemaker を使用している場合、VM には Azure 管理 API への送信接続が必要です。 この記事では、シナリオに最適なオプションを選択できるように、複数のオプションを示します。  

## <a name="overview"></a>概要

クラスタリングを使用して SAP ソリューションに対する高可用性を実装する場合、必要なコンポーネントの 1 つは [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) です。 Azure には、Standard と Basic の 2 つのロード バランサー SKU が用意されています。

Standard Azure Load Balancer には、Basic ロード バランサーより優れた点がいくつかあります。 たとえば、Azure 可用性ゾーンをまたがって機能し、トラブルシューティングが容易になる強化された監視とログ記録の機能を備え、待機時間が短縮されます。 "HA ポート" 機能は、すべてのポートが対象です。つまり、すべてのポートを個別に指定する必要がなくなります。  

Azure Load Balancer の Basic SKU と Standard SKU には、いくつかの重要な違いがあります。 そのうちの 1 つは、パブリック エンドポイントへの送信トラフィックの処理です。 Basic SKU と Standard SKU の Load Balancerの詳細な比較については、「[Load Balancer の SKU の比較](../../../load-balancer/load-balancer-overview.md)」をご覧ください。  
 
パブリック IP アドレスを持たない VM が、内部 (パブリック IP アドレスがない) Standard Azure Load Balancer のバックエンド プール内に配置されている場合、追加の構成を行わない限り、パブリック エンドポイントへの送信接続はありません。  

VM にパブリック IP アドレスが割り当てられている場合、または VM がパブリック IP アドレスを持つロード バランサーのバックエンド プールに含まれる場合は、パブリック エンドポイントへの送信接続があります。  

SAP システムには、機密性の高いビジネス データが含まれることがよくあります。 SAP システムをホストしている VM にパブリック IP アドレス経由でアクセスすることが許可されることはほとんどありません。 同時に、VM からパブリック エンドポイントへの送信接続を必要とするシナリオもあります。  

Azure のパブリック エンドポイントへのアクセスを必要とするシナリオの例を次に示します。  
- Azure Fence Agent には、**management.azure.com** と **login.microsoftonline.com** へのアクセスが必要になります  
- [Azure Backup](../../../backup/tutorial-backup-sap-hana-db.md#set-up-network-connectivity)
- [Azure Site Recovery](../../../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-for-urls)  
- オペレーティング システムに修正プログラムを適用するためのパブリック リポジトリの使用
- SAP アプリケーションのデータ フローで、パブリック エンドポイントへの送信接続が必要になる場合がある

SAP のデプロイでパブリック エンドポイントへの送信接続が必要ない場合は、追加の構成を実装する必要はありません。 パブリック エンドポイントからの受信接続も不要であるなら、高可用性シナリオ用に内部 Standard SKU Azure Load Balancer を作成するだけで十分です。  

> [!Note]
> パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。  
>VM にパブリック IP アドレスがあるか、または VM がパブリック IP アドレスを持つ Azure Load Balancer のバックエンド プールに既に存在する場合、VM は既にパブリック エンドポイントへの送信接続を持っています。


最初に、以下の記事をお読みください。

* Azure Standard Load Balancer
  * [Azure Standard Load Balancer の概要](../../../load-balancer/load-balancer-overview.md) - Azure Standard Load Balancer、重要な原則、概念、チュートリアルの包括的な概要 
  * [Azure のアウトバウンド接続](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) - Azure で送信接続を実現する方法に関するシナリオ
  * [Load Balancer のアウトバウンド規則](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) - Load Balancer のアウトバウンド規則の概念と、アウトバウンド規則を作成する方法について説明します
* Azure Firewall
  * [Azure Firewall の概要](../../../firewall/overview.md) - Azure Firewall の概要
  * [チュートリアル:Azure Firewall のデプロイと構成](../../../firewall/tutorial-firewall-deploy-portal.md) - Azure portal を使用して Azure Firewall を構成する方法についての説明
* [仮想ネットワーク - ユーザー定義規則](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) - Azure ルーティングの概念と規則  
* [セキュリティ グループのサービス タグ](../../../virtual-network/network-security-groups-overview.md#service-tags) - サービス タグを使用してネットワーク セキュリティ グループとファイアウォールの構成を簡略化する方法

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>オプション 1: インターネットへの送信接続のために追加する外部 Azure Standard Load Balancer

パブリック エンドポイントから VM への受信接続を許可せずに、パブリック エンドポイントへの送信接続を実現する 1 つのオプションは、パブリック IP アドレスを使用して 2 番目のロード バランサーを作成し、2 番目のロード バランサーのバックエンド プールに VM を追加して、[送信規則](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)だけを定義することです。  
VM からの発信呼び出しにアクセスできるパブリック エンドポイントを制御するには、[ネットワーク セキュリティ グループ](../../../virtual-network/network-security-groups-overview.md)を使用します。  
詳しくは、[アウトバウンド接続](../../../load-balancer/load-balancer-outbound-connections.md#scenarios)に関するドキュメントのシナリオ 2 をご覧ください。  
構成は次のようになります。  

![ネットワーク セキュリティ グループを使用してパブリック エンドポイントへの接続を制御する](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>重要な考慮事項

- 同じサブネット内の複数の VM 用にパブリック Load Balancer を 1 つ追加して、パブリック エンドポイントへの送信接続を実現し、コストを最適化することができます  
- VM からアクセスできるパブリック エンドポイントを制御するには、[ネットワーク セキュリティ グループ](../../../virtual-network/network-security-groups-overview.md)を使用します。 ネットワーク セキュリティ グループは、サブネットまたは各 VM に割り当てることができます。 可能な場合は、[サービス タグ](../../../virtual-network/network-security-groups-overview.md#service-tags)を使用して、セキュリティ規則の複雑さを軽減します。  
- パブリック IP アドレスとアウトバウンド規則を備えた Azure Standard Load Balancer を使うと、パブリック エンドポイントに直接アクセスできます。 企業のセキュリティ要件で、監査とログ記録のため、集中管理された企業ソリューションを使用してすべての送信トラフィックを通過させる必要がある場合は、このシナリオで要件を満たすことができない可能性があります。  

>[!TIP]
>可能な場合は、[サービス タグ](../../../virtual-network/network-security-groups-overview.md#service-tags)を使用して、ネットワーク セキュリティ グループの複雑さを軽減します。 

### <a name="deployment-steps"></a>デプロイメントの手順

1. Load Balancer の作成  
   1. [Azure portal](https://portal.azure.com) で、[すべてのリソース]、[追加] の順にクリックして、**Load Balancer** を検索します  
   1. **[作成]** をクリックします。 
   1. ロード バランサーの名前を「**MyPublicILB**」に設定します  
   1. 種類として **[パブリック]** を選択し、SKU として **[Standard]** を選択します  
   1. **[パブリック IP アドレスの作成]** を選択し、名前として「**MyPublicILBFrondEndIP**」と指定します  
   1. [可用性ゾーン] として、 **[ゾーン冗長]** を選択します  
   1. [確認と作成] をクリックして、[作成] をクリックします  
2. バックエンドプール **MyBackendPoolOfPublicILB** を作成し、VM を追加します。  
   1. 仮想ネットワークを選択します  
   1. VM とその IP アドレスを選択し、バックエンド プールにそれらを追加します  
3. [アウトバウンド規則を作成します](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration)。 現在、Azure portal からアウトバウンド規則を作成することはできません。 アウトバウンド規則は [Azure CLI](../../../cloud-shell/overview.md) で作成できます。  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. 特定のパブリック エンドポイントへのアクセスを制限するネットワーク セキュリティ グループの規則を作成します。 既存のネットワーク セキュリティ グループがある場合は、それを調整できます。 次の例では、Azure 管理 API に対してのみアクセスを有効にする方法を示します。 
   1. [ネットワーク セキュリティ グループ] に移動します
   1. [送信セキュリティ規則] をクリックします
   1. **インターネット** へのすべての送信アクセスを **拒否する** 規則を追加します。
   1. すべてのインターネット アクセスを拒否する規則の優先順位より低い優先順位での、**AzureCloud** に対するアクセスを **許可する** 規則を追加します。


   送信セキュリティ規則は次のようになります。 

   ![パブリック IP を使用した 2 番目の Load Balancer での送信接続](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Azure ネットワーク セキュリティ グループについて詳しくは、「[セキュリティ グループ](../../../virtual-network/network-security-groups-overview.md)」をご覧ください。 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>オプション 2:インターネットへの送信接続に対する Azure Firewall

パブリック エンドポイントから VM への受信接続を許可せずに、パブリック エンドポイントへの送信接続を実現するもう 1 つのオプションは、Azure Firewall を使用するものです。 Azure Firewall は、高可用性が組み込まれたマネージド サービスであり、複数の可用性ゾーンにまたがることができます。  
Azure Firewall 経由でトラフィックをルーティングするには、[ユーザー定義ルート](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)をデプロイし、VM と Azure ロード バランサーがデプロイされているサブネットに関連付け、Azure Firewall をポイントする必要もあります。  
Azure Firewall をデプロイする方法について詳しくは、[Azure Firewall のデプロイと構成](../../../firewall/tutorial-firewall-deploy-portal.md)に関するページをご覧ください。  

アーキテクチャは次のようになります。

![Azure Firewall での送信接続](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>重要な考慮事項

- Azure Firewall は、高可用性が組み込まれたクラウド ネイティブ サービスであり、ゾーン デプロイがサポートされています。
- AzureFirewallSubnet という名前のサブネットを追加する必要があります。 
- SAP VM が配置されている仮想ネットワークから、別の仮想ネットワーク内の VM、またはパブリック エンドポイントに対して、大規模なデータ セットを転送する場合、コスト効率のよいソリューションではない可能性があります。 そのような例の 1 つは、大きなバックアップを複数の仮想ネットワーク間でコピーする場合です。 詳しくは、Azure Firewall の価格を参照してください。  
- 企業のファイアウォール ソリューションが Azure Firewall ではなく、すべての送信トラフィックを一元化された企業ソリューションを通過させるというセキュリティ要件がある場合、このソリューションは実用的ではない可能性があります。  

>[!TIP]
>可能な場合は、[サービス タグ](../../../virtual-network/network-security-groups-overview.md#service-tags)を使用して、Azure Firewall の規則の複雑さを軽減します。  

### <a name="deployment-steps"></a>デプロイメントの手順

1. デプロイ手順では、仮想ネットワークとサブネットが VM に対して既に定義されているものとします。  
2. VM と Standard ロード バランサーがデプロイされているのと同じ仮想ネットワークに、サブネット **AzureFirewallSubnet** を作成します。  
   1. Azure portal で仮想ネットワークに移動します。[すべてのリソース] をクリックし、[仮想ネットワーク] を探してクリックして、[サブネット] を選択します。  
   1. [サブネットの追加] をクリックします。 名前として「**AzureFirewallSubnet**」と入力します。 適切なアドレス範囲を入力します。 保存します。  
3. Azure Firewall を作成します。  
   1. Azure portal で [すべてのリソース] を選択し、[追加]、[ファイアウォール]、[作成] の順にクリックします。 [リソース グループ] を選択します (仮想ネットワークと同じリソース グループを選択します)。  
   1. Azure Firewall リソースの名前を入力します。 たとえば、**MyAzureFirewall** などとします。  
   1. [リージョン] を選択し、VM がデプロイされている可用性ゾーンに合わせて、少なくとも 2 つの可用性ゾーンを選択します。  
   1. SAP VM と Azure Standard Load Balancer がデプロイされている仮想ネットワークを選択します。  
   1. パブリック IP アドレス: [作成] をクリックし、名前を入力します。 たとえば、**MyFirewallPublicIP** です。  
4. 指定したパブリック エンドポイントへの送信接続を許可する Azure ファイアウォール規則を作成します。 例では、Azure 管理 API パブリック エンドポイントへのアクセスを許可する方法が示されています。  
   1. [ルール]、[ネットワーク ルール コレクション] を選択し、[ネットワーク ルール コレクションの追加] をクリックします。  
   1. 名前:「**MyOutboundRule**」と入力し、優先順位を入力し、**許可** アクションを選択します。  
   1. サービス:**ToAzureAPI** という名前を指定します。  プロトコル: **[すべて]** を選択します。 送信元アドレス: VM と Standard Load Balancer がデプロイされているサブネットの範囲を入力します (例: **11.97.0.0/24**)。 宛先ポート: 「<b>*</b>」と入力します。  
   1. 保存
   1. Azure Firewall にまだいる間に、[概要] を選択します。 Azure Firewall のプライベート IP アドレスを記録します。  
5. Azure Firewall へのルートを作成します  
   1. Azure portal で [すべてのリソース] を選択し、[追加]、[ルート テーブル]、[作成] の順にクリックします。  
   1. 「MyRouteTable」という名前を入力し、サブスクリプション、リソース グループ、場所を選択します (仮想ネットワークとファイアウォールの場所に一致させます)。  
   1. 保存  

   ファイアウォール規則は次のようになります。![ファイアウォールがどのように見えるかを示す図。](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. VM のサブネットから **MyAzureFirewall** のプライベート IP アドレスへのユーザー定義ルートを作成します。
   1. [ルート テーブル] で、[ルート] をクリックします。 [追加] を選択します。 
   1. ルート名: ToMyAzureFirewall、アドレス プレフィックス: **0.0.0.0/0**。 次ホップの種類: 仮想アプライアンスを選択します。 次ホップ アドレス: 構成したファイアウォールのプライベート IP アドレスを入力します: **11.97.1.4**。  
   1. 保存

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>オプション 3:Azure 管理 API への Pacemaker 呼び出しに対するプロキシの使用

プロキシを使用して、Azure 管理 API のパブリック エンドポイントへの Pacemaker の呼び出しを許可することができます。  

### <a name="important-considerations"></a>重要な考慮事項

  - 企業プロキシが既に存在する場合は、それを通してパブリック エンドポイントに送信呼び出しをルーティングできます。 パブリック エンドポイントへの送信呼び出しは、企業の制御ポイントを経由します。  
  - プロキシ構成で、Azure 管理 API への送信接続が許可されていることを確認します: `https://management.azure.com` と `https://login.microsoftonline.com`  
  - VM からプロキシへのルートがあることを確認します  
  - プロキシでは、HTTP/HTTPS 呼び出しのみが処理されます。 別のプロトコル (RFC など) でパブリック エンドポイントへの送信呼び出しを行う必要がある場合は、代わりのソリューションが必要になります  
  - Pacemaker クラスターが不安定になるのを防ぐため、プロキシ ソリューションは高可用性である必要があります  
  - プロキシの場所によっては、Azure フェンス エージェントから Azure 管理 API への呼び出しで、追加の待機時間が発生する可能性があります。 企業プロキシがまだオンプレミスにあり、Pacemaker クラスターが Azure にある場合は、待機時間を測定し、このソリューションが適しているかどうかを検討します  
  - まだ高可用性の企業プロキシがない場合は、追加コストと複雑さが発生するため、このオプションはお勧めしません。 それにもかかわらず、Pacemaker から Azure 管理パブリック API への送信接続を許可するために、追加のプロキシ ソリューションをデプロイする場合は、プロキシを高可用性にし、VM からプロキシへの待機時間を短くしてください。  

### <a name="pacemaker-configuration-with-proxy"></a>プロキシを使用する Pacemaker の構成 

業界では、さまざまなプロキシ オプションを利用できます。 プロキシをデプロイする詳細な手順については、このドキュメントでは説明しません。 次の例では、プロキシが **MyProxyService** に応答し、ポート **MyProxyPort** をリッスンしているものとします。  
Pacemaker が Azure 管理 API と通信できるようにするには、すべてのクラスター ノードで次の手順を実行します。  

1. Pacemaker の構成ファイル /etc/sysconfig/pacemaker を編集し、次の行を追加します (すべてのクラスター ノードで)。

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. **すべての** クラスター ノード で Pacemaker サービスを再起動します。  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>その他のオプション

URL ベースのサードパーティ ファイアウォール プロキシ経由で送信トラフィックがルーティングされる場合:

- Azure フェンス エージェントを使用している場合、Azure 管理 API `https://management.azure.com` および `https://login.microsoftonline.com` への送信接続がファイアウォール構成で許可されるようにします。   
- 更新プログラムやパッチの適用に SUSE の Azure パブリック クラウド更新インフラストラクチャを使用している場合、「[Azure Public Cloud Update Infrastructure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)」 (Azure パブリック クラウド更新インフラストラクチャ 101) を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure の SUSE で Pacemaker を構成する方法を学習します](./high-availability-guide-suse-pacemaker.md)
* [Azure の Red Hat で Pacemaker を構成する方法を学習します](./high-availability-guide-rhel-pacemaker.md)