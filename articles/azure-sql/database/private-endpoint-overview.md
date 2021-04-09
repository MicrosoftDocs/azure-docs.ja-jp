---
title: Azure Private Link
description: プライベート エンドポイント機能の概要。
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 506982a22fd53dfb4f8f3d94735c11fc491469eb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097280"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database と Azure Synapse Analytics に対する Azure Private Link
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Private Link を使用すると、**プライベート エンドポイント** を経由して Azure 内のさまざまな PaaS サービスに接続できます。 Private Link 機能をサポートしている PaaS サービスの一覧については、「[Private Link のドキュメント](../../private-link/index.yml)」ページを参照してください。 プライベート エンドポイントは、特定の [VNet](../../virtual-network/virtual-networks-overview.md) およびサブネット内のプライベート IP アドレスです。

> [!IMPORTANT]
> この記事は、Azure SQL Database と Azure Synapse Analytics の両方に適用されます。 単純にするために、"データベース" という言葉で Azure SQL Database と Azure Synapse Analytics の両方のデータベースを表すことにします。 同様に、"サーバー" という言葉は、Azure SQL Database と Azure Synapse Analytics をホストする[論理 SQL サーバー](logical-servers.md)を表しています。 この記事は、**Azure SQL Managed Instance** には適用され "*ません*"。

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Azure SQL Database に Private Link を設定する方法 

### <a name="creation-process"></a>作成プロセス
プライベート エンドポイントは、Azure portal、PowerShell、または Azure CLI を使用して作成できます。
- [ポータル](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>承認プロセス
ネットワーク管理者がプライベート エンドポイント (PE) を作成すると、SQL 管理者は SQL Database へのプライベート エンドポイント接続 (PEC) を管理できます。

1. 次のスクリーンショットに示されている手順に従って、Azure portal でサーバー リソースに移動します

    - (1) 左側のウィンドウで、プライベート エンドポイント接続を選択
    - (2) すべてのプライベート エンドポイント接続 (PEC) の一覧を表示
    - (3) 作成された対応するプライベート エンドポイント (PE) ![すべての PEC のスクリーンショット][3]

1. リストから個々の PEC を選択します。
![選択された PEC のスクリーンショット][6]

1. SQL 管理者は、PEC の承認または拒否を選択できます。また、必要に応じて、短いテキスト応答を追加することもできます。
![PEC 承認のスクリーンショット][4]

1. 承認または拒否すると、リストには応答テキストとともに適切な状態が反映されます。
![承認後のすべての PEC のスクリーンショット][5]

## <a name="on-premises-connectivity-over-private-peering"></a>プライベート ピアリングを介したオンプレミス接続

お客様がオンプレミスのマシンからパブリック エンドポイントに接続する場合、[サーバーレベルのファイアウォール規則](firewall-create-server-level-portal-quickstart.md)を使用して、ご自分の IP アドレスを IP ベースのファイアウォールに追加する必要があります。 このモデルは、開発またはテストのワークロード用に個々のコンピューターへのアクセスを許可する場合には適していますが、運用環境で管理するのは困難です。

Private Link を使用すると、[ExpressRoute](../../expressroute/expressroute-introduction.md)、プライベート ピアリング、または VPN トンネリングを使用して、プライベート エンドポイントへのクロスプレミス アクセスを有効にすることができます。 その後、お客様はパブリック エンドポイント経由のすべてのアクセスを無効にし、IP ベースのファイアウォールを使用して任意の IP アドレスを許可しないようにすることができます。

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL Database での Private Link のユース ケース 

クライアントは、同じ仮想ネットワークから、同じリージョン内のピアリングされた仮想ネットワークから、またはリージョン間の仮想ネットワーク間接続を介して、プライベート エンドポイントに接続できます。 さらに、クライアントは、ExpressRoute、プライベート ピアリング、または VPN トンネリングを使用して、オンプレミスから接続できます。 一般的なユース ケースを示す簡略化された図を以下に示します。

 ![接続オプションの図][1]

さらに、仮想ネットワークで直接実行されているわけではないが、仮想ネットワークと統合されているサービス (App Service Web Apps や Functions など) も、データベースへのプライベート接続を実現できます。 このユース ケースの詳細については、[Azure SQL データベースへのプライベート接続を使用する Web アプリ](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app)のアーキテクチャ シナリオをご覧ください。

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>同じ仮想ネットワーク内の Azure VM から SQL Database への接続をテストする

このシナリオでは、Windows Server 2016 を実行する Azure Virtual Machine (VM) を作成しているものとします。 

1. [リモート デスクトップ (RDP) セッションを開始し、仮想マシンに接続します。](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) 
1. 次に、次のツールを使用して、VM がプライベート エンドポイント経由で SQL Database に接続されていることを確認する基本的な接続チェックを実行できます。
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Telnet を使用して接続を確認する

[Telnet クライアント](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29)は、接続をテストするために使用できる Windows の機能です。 Windows OS のバージョンによっては、この機能を明示的に有効にする必要がある場合があります。 

Telnet をインストールした後で、コマンド プロンプト ウィンドウを開きます。 Telnet コマンドを実行し、SQL Database 内のデータベースの IP アドレスとプライベート エンドポイントを指定します。

```
>telnet 10.1.1.5 1433
```

Telnet が正常に接続されると、コマンド ウィンドウに次の図のような空の画面が表示されます。

 ![Telnet の図][2]

### <a name="check-connectivity-using-psping"></a>Psping を使用して接続を確認する

[Psping](/sysinternals/downloads/psping) を次のように使用して、プライベート エンドポイント接続 (PEC) がポート 1433 での接続をリッスンしていることを確認することができます。

次のように論理 SQL サーバーの FQDN とポート 1433 を指定して、psping を実行します。

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

出力には、Psping が PEC に関連付けられているプライベート IP アドレスに ping を実行できることが示されます。

### <a name="check-connectivity-using-nmap"></a>Nmap を使用して接続を確認する

Nmap (ネットワーク マッパー) は、ネットワーク探索とセキュリティ監査に使用される無料のオープン ソース ツールです。 詳細とダウンロード リンクについては、 https://nmap.org にアクセスしてください。このツールを使用すると、プライベート エンドポイントがポート 1433 での接続をリッスンしていることを確認できます。

次のようにプライベート エンドポイントをホストするサブネットのアドレス範囲を指定して、Nmap を実行します。

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

結果には、1 つの IP アドレスが稼働していることが示されます。これは、プライベート エンドポイントの IP アドレスに対応します。

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して接続を確認する
> [!NOTE]
> クライアント (`<server>.database.windows.net`) の接続文字列で、サーバーの **完全修飾ドメイン名 (FQDN)** を使用します。 IP アドレスに対して直接、またはプライベート リンクの FQDN (`<server>.privatelink.database.windows.net`) を使用してログインを試みると、失敗します。 この動作は仕様によるものです。トラフィックは、プライベート エンドポイントによってリージョン内の SQL Gateway にルーティングされ、ログインに成功するためには、正しい FQDN を指定する必要があるためです。

ここに示す手順に従い、[SSMS を使用して SQL データベースに接続します](connect-query-ssms.md)。 SSMS を使用して SQL Database に接続したら、次のクエリを実行して、Azure VM のプライベート IP アドレスから接続していることを確認します。

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>データの流出防止

Azure SQL Database におけるデータの流出とは、データベース管理者などの承認されたユーザーが、あるシステムからデータを抽出して、組織外の別の場所またはシステムに移動できる場合です。 たとえば、ユーザーがサードパーティによって所有されているストレージ アカウントにデータを移動します。

SQL Database 内のデータベースに接続している Azure 仮想マシン内で SQL Server Management Studio (SSMS) を実行しているユーザーのシナリオについて考えます。 このデータベースは、米国西部のデータ センターにあります。 次の例では、ネットワーク アクセス制御を使用して SQL Database のパブリック エンドポイントでアクセスを制限する方法を示します。

1. [Allow Azure Services]\(Azure サービスを許可する\) を **[オフ]** に設定して、パブリック エンドポイント経由で SQL Database へのすべての Azure サービス トラフィックを無効にします。 サーバーおよびデータベース レベルのファイアウォール規則で IP アドレスが許可されていないことを確認してください。 詳細については、[Azure SQL Database および Azure Synapse Analytics のネットワーク アクセスの制御](network-access-controls-overview.md)に関するページを参照してください。
1. VM のプライベート IP アドレスを使用して、SQL Database 内のデータベースへのトラフィックのみを許可します。 詳細については、[サービス エンドポイント](vnet-service-endpoint-rule-overview.md)と[仮想ネットワークのファイアウォール規則](firewall-configure.md)に関する記事を参照してください。
1. Azure VM で、次のように[ネットワーク セキュリティ グループ (NSG)](../../virtual-network/manage-network-security-group.md) とサービス タグを使用して、送信接続の範囲を絞り込みます。
    - 米国西部にある SQL Database への接続のみを許可するように、サービス タグへのトラフィックを許可する NSG ルールを指定します (Service Tag = SQL.WestUs)
    - すべてのリージョンで SQL Database への接続を拒否するように、サービス タグへのトラフィックを拒否する NSG ルールを (**高い優先度** で) 指定します (Service Tag = SQL)

この設定が終了すると、Azure VM は米国西部リージョンにある SQL Database 内のデータベースにのみ接続できます。 ただし、接続は SQL Database 内の 1 つのデータベースに限定されません。 この VM は、サブスクリプションに含まれていないデータベースも含め、米国西部リージョンの任意のデータベースに引き続き接続できます。 上記のシナリオでは、データの流出が特定のリージョンに限定されていますが、完全には除外されていません。

Private Link を使用することで、お客様が NSG のようなネットワーク アクセス制御を設定してプライベート エンドポイントへのアクセスを制限できるようになりました。 その後、個々の Azure PaaS リソースが特定のプライベート エンドポイントにマップされます。 悪意のある内部関係者は、マップされた PaaS リソース (SQL Database 内のデータベースなど) にしかアクセスできず、その他のリソースにはアクセスできません。 

## <a name="limitations"></a>制限事項 
プライベート エンドポイントへの接続では、**プロキシ** のみが [接続ポリシー](connectivity-architecture.md#connection-policy)としてサポートされます。


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>ピアリングされた仮想ネットワーク内の Azure VM からの接続 

[仮想ネットワーク ピアリング](../../virtual-network/tutorial-connect-virtual-networks-powershell.md)を構成し、ピアリングされた仮想ネットワーク内の Azure VM から SQL Database への接続を確立します。

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>仮想ネットワーク内の Azure VM から仮想ネットワーク環境への接続

[仮想ネットワーク間 VPN ゲートウェイ接続](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)を構成し、別のリージョンまたはサブスクリプションの Azure VM から SQL Database 内のデータベースへの接続を確立します。

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>オンプレミス環境から VPN 経由の接続

オンプレミス環境から SQL Database 内のデータベースへの接続を確立するには、次のいずれかのオプションを選択して実装します。
- [ポイント対サイト接続](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [サイト間 VPN 接続](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute 回線](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Polybase と COPY ステートメントを使用した Azure Synapse Analytics から Azure Storage への接続

PolyBase と COPY ステートメントは、Azure Storage アカウントから Azure Synapse Analytics にデータを読み込むときによく使用されます。 データの読み込み元の Azure ストレージ アカウントで、アクセスがプライベート エンドポイント、サービス エンドポイント、または IP ベースのファイアウォールを介した一連の仮想ネットワーク サブネットだけに制限されている場合、PolyBase と COPY ステートメントからそのアカウントへの接続は切断されます。 仮想ネットワークに結び付けられた Azure Storage に接続する Azure Synapse Analytics でインポートとエクスポート両方のシナリオを有効にするには、[こちら](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)で示されている手順のようにします。 

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のセキュリティの概要については、「[データベースの保護](security-overview.md)」を参照してください。
- Azure SQL Database 接続の概要については、「[Azure SQL の接続アーキテクチャ](connectivity-architecture.md)」を参照してください
- [Azure SQL データベースへのプライベート接続を使用する Web アプリ](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app)のアーキテクチャ シナリオも確認できます。このシナリオでは、仮想ネットワークの外部にある Web アプリケーションをデータベースのプライベート エンドポイントに接続します。

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png