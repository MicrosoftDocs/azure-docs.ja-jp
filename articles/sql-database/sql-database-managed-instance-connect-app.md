---
title: 'Azure SQL Database Managed Instance: アプリケーションの接続 | Microsoft Docs'
description: この記事では、Azure SQL Database Managed Instance にアプリケーションを接続する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: 5f4a1962f90d54001f315827c1243e929344e3d7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274004"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance にアプリケーションを接続する

現在、アプリケーションをホストする方法や場所については、複数の選択肢があります。

Azure App Service か、Azure の仮想ネットワーク (VNet) 統合オプション (Azure App Service Environment、仮想マシン、仮想マシン スケール セットなど) を使用して、アプリケーションをクラウドでホストすることもできますし、 ハイブリッド クラウドのアプローチを使用して、アプリケーションをオンプレミスに維持することもできます。

どの方法を選択した場合でも、アプリケーションをマネージド インスタンスに接続することは可能です。  

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>同じ VNet 内のアプリケーションを接続する

このシナリオは、最もシンプルです。 同じ VNet 内の仮想マシンは、異なるサブネット内にあっても、直接相互接続できます。 つまり、Azure Application Environment または仮想マシン内でアプリケーションを接続する場合は、接続文字列を適切に設定するだけでよいということです。  

## <a name="connect-an-application-inside-a-different-vnet"></a>異なる VNet 内のアプリケーションを接続する

このシナリオは少し複雑です。マネージド インスタンスが、自身の VNet 内でプライベート IP アドレスを持つためです。 接続するには、マネージド インスタンスがデプロイされている VNet にアプリケーションがアクセスできるようにする必要があります。 そのため、まずアプリケーションとマネージド インスタンス VNet 間の接続を作成する必要があります。 このシナリオを達成するために、各 Vnet が同じサブスクリプションに属する必要はありません。

Vnet を接続するには、次の 2 つのオプションがあります。

- [Azure Virtual Network ピアリング](../virtual-network/virtual-network-peering-overview.md)
- VNet 間 VPN ゲートウェイ ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

望ましいオプションはピアリングです。ピアリングでは Microsoft バックボーン ネットワークが使用されるため、接続の観点から言えば、ピアリングされた VNet 内の仮想マシンであっても、同じ VNet 内の仮想マシンであっても、待機時間に顕著な違いはありません。 VNet ピアリングは、同じリージョン内のネットワークに制限されます。  

> [!IMPORTANT]
> Managed Instance の VNet ピアリング シナリオは、[グローバル仮想ネットワーク ピアリングの制約](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)により、同じリージョン内のネットワークに制限されます。 詳細については、[Azure Virtual Networks のよく寄せられる質問](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事の関連セクションも参照してください。 

## <a name="connect-an-on-premises-application"></a>オンプレミス アプリケーションを接続する

マネージド インスタンスには、プライベート IP アドレスを介してのみアクセスできます。 オンプレミスからアクセスするには、アプリケーションとマネージド インスタンス VNet の間にサイト間接続を確立する必要があります。

オンプレミスを Azure VNet に接続するには、次の 2 つのオプションがあります。

- サイト間 VPN 接続 ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) 接続  

オンプレミスから Azure への接続を正常に確立しているが、マネージド インスタンスへの接続を確立できない場合は、ファイアウォールでリダイレクト用に SQL ポート 1433 および範囲 11000-11999 のポート上に送信接続が開かれているかどうかを確認してください。

## <a name="connect-an-application-on-the-developers-box"></a>開発者ボックス上のアプリケーションを接続する

マネージド インスタンスはプライベート IP アドレスを介してのみアクセスできるため、開発者ボックスからアクセスするには、まず開発者ボックスとマネージド インスタンス VNet の間に接続を確立する必要があります。 これを行うには、ネイティブ Azure 証明書認証を使用した VNet へのポイント対サイト接続を構成します。 詳細については、「[オンプレミス コンピューターから Azure SQL Database Managed Instance に接続するようにポイント対サイト接続を構成する](sql-database-managed-instance-configure-p2s.md)」を参照してください。

## <a name="connect-from-on-premises-with-vnet-peering"></a>VNet ピアリングを使用したオンプレミスからの接続

お客様によって実装されるもう 1 つのシナリオに、VPN ゲートウェイがマネージド インスタンスをホストしているのとは別の仮想ネットワークとサブスクリプションにインストールされているというがあります。 その後 2 つの仮想ネットワークはピアリングされます。 次のアーキテクチャ図では、これの実装例を示します。

![VNET ピアリング](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

基本のインフラストラクチャを構成したら、一部の設定を変更して、VPN Gateway がマネージド インスタンスをホストする仮想ネットワークの IP アドレスを参照できるようにします。 これを行うには、 **[Peering settings]\(ピアリング設定\)** の下で次の具体的な変更を実行します。

1. VPN ゲートウェイをホストする VNet で **[ピアリング]** 、マネージド インスタンスとピアリングされている VNet 接続に進み、 **[ゲートウェイ転送を許可する]** をクリックします。
2. マネージド インスタンスをホストする VNet で **[ピアリング]** 、VPN Gateway とピアリングされている VNet 接続に進み、 **[Use remote gateways]\(リモート ゲートウェイを使用\)** をクリックします。

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service でホストされたアプリケーションを接続する

マネージド インスタンスはプライベート IP アドレスを介してのみアクセスできるため、Azure App Service からアクセスするには、まずアプリケーションとマネージド インスタンス VNet の間に接続を確立する必要があります。 「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)」をご覧ください。  

トラブルシューティングについては、[Vnet とアプリケーションのトラブルシューティング](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)に関する記事をご覧ください。 接続を確立できない場合は、[ネットワーク構成の同期](sql-database-managed-instance-sync-network-configuration.md)を試してください。

Azure App Service をマネージド インスタンスに接続する場合の特殊なケースとして、マネージド インスタンス VNet にピアリングされたネットワークに Azure App Service を統合しているケースが考えられます。 その場合は、次の構成をセットアップする必要があります。

- マネージド インスタンス VNet でゲートウェイを使用しない  
- マネージド インスタンス VNet で、リモート ゲートウェイを使用するオプションを設定する
- ピアリングされた VNet で、[ゲートウェイ転送を許可する] オプションを設定する

次の図は、このシナリオを説明したものです。

![統合アプリケーション ピアリング](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>VNet 統合機能では、アプリと ExpressRoute ゲートウェイを使う VNet は統合されません。 ExpressRoute ゲートウェイが共存モードで構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続を通してリソースにアクセスする必要がある場合は、VNet で実行する App Service Environment を使用できます。

## <a name="troubleshooting-connectivity-issues"></a>接続の問題のトラブルシューティング

接続の問題のトラブルシューティングには、次を参照してください。

- 同じ VNet であるが、別のサブネット内の Azure 仮想マシンからマネージド インスタンスに接続できない場合は、VM サブネット上に、アクセスをブロックしている可能性のあるネットワーク セキュリティ グループが設定されているかどうかを確認します。さらに、SQL ポート 1433 および範囲 11000-11999 のポートは Azure 境界内でリダイレクト経由で接続するために必要であるため、これらのポート上に送信接続を開く必要があることに注意してください。
- VNet と関連付けられているルート テーブルで、BGP 伝達が**有効**に設定されていることを確認します。
- P2S VPN を使用している場合、Azure portal の構成に**イングレスとエグレス**の数が表示されることを確認します。 ゼロ以外の数であるということは、Azure がトラフィックをオンプレミスからまたはオンプレミスにルーティングしていることを示します。

   ![イングレス/エグレスの数](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- (VPN クライアントを実行している) クライアント コンピューターに、アクセスする必要のあるすべての VNet のルート エントリがあることを確認します。 ルートは、`%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` に格納されています。

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   この図のとおり、関連する各 VNet 用に 2 つのエントリと、ポータルに構成されている VPN エンドポイント用に 3 つ目のエントリがあります。

   ルートを確認するもう 1 つの方法は、次のコマンドの使用です。 出力からは、さまざまなサブネットへのルートが示されています。

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- VNet ピアリングを使用している場合、[ゲートウェイ転送を許可し、リモート ゲートウェイを使用する方法](#connect-from-on-premises-with-vnet-peering)に関する説明の設定手順に必ず従ってください。

## <a name="required-versions-of-drivers-and-tools"></a>ドライバーとツールの必要なバージョン

マネージド インスタンスに接続しようとする場合、ツールとドライバーの最小バージョンとして次をお勧めします。

| ドライバーとツール | バージョン |
| --- | --- |
|.NET Framework | 4.6.1 (または .NET Core) |
|ODBC ドライバー| v17 |
|PHP ドライバー| 5.2.0 |
|JDBC ドライバー| 6.4.0 |
|Node.js ドライバー| 2.1.1 |
|OLEDB ドライバー| 18.0.2.0 |
|SSMS| 18.0 [以降](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 以降 |

## <a name="next-steps"></a>次の手順

- マネージド インスタンスについては、「[What is a Managed Instance? (マネージド インスタンスとは)](sql-database-managed-instance.md)」をご覧ください。
- 新しいマネージド インスタンスの作成方法を紹介するチュートリアルが必要な場合、「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」を参照してください。
