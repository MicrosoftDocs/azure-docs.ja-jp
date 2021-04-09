---
title: ネットワーク アクセス制御
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Azure SQL Database と Azure Synapse Analytics のネットワーク アクセスを管理および制御する方法の概要。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460009"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database と Azure Synapse Analytics のネットワーク アクセスの制御

Azure SQL Database と Azure Synapse Analytics の論理 Azure SQL Server を [Azure portal](single-database-create-quickstart.md) から作成するとき、結果は *yourservername.database.windows.net* という形式のパブリック エンドポイントになります。

次のネットワーク アクセスの制御を使用して、パブリック エンドポイントを介したデータベースへのアクセスを選択的に許可できます。

- Azure サービスを許可する:オンに設定すると、Azure 境界内の他のリソース (たとえば、Azure Virtual Machine) が SQL Database にアクセスできるようになります
- IP ファイアウォール規則:この機能を使用すると、特定の IP アドレスからの (たとえば、オンプレミスのマシンからの) 接続を明示的に許可できます

また、次の方法で[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)からデータベースへのプライベート アクセスを許可することもできます。

- 仮想ネットワーク ファイアウォール規則: この機能を使用して、Azure 境界内の特定の仮想ネットワークからのトラフィックを許可します
- Private Link:この機能を使用して、特定の仮想ネットワーク内に[論理 SQL サーバー](logical-servers.md)のプライベート エンドポイントを作成します

> [!IMPORTANT]
> この記事は、**SQL Managed Instance** には適用され "*ません*"。 ネットワーク構成の詳細については、[Azure SQL Managed Instance への接続](../managed-instance/connect-application-instance.md)に関するページを参照してください。

これらのアクセス制御とその機能の概要については、以下のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure サービスを許可する

[Azure portal で](single-database-create-quickstart.md)論理 SQL サーバーを新しく作成するときには、この設定は既定で **オフ** になります。 この設定は、パブリック サービス エンドポイントを使用した接続が許可されている場合に表示されます。

この設定は、次のように、論理 SQL サーバーを作成した後にファイアウォール ウィンドウで変更することもできます。
  
![サーバーのファイアウォール管理のスクリーンショット][2]

**[オン]** に設定すると、サーバーにより、Azure 境界内のすべてのリソース (自分のサブスクリプションの一部であることも、そうでないこともあります) からの通信が許可されます。

多くの場合、 **[オン]** 設定は、ほとんどのお客様が望むよりも許容範囲が広くなっています。 この設定を **[オフ]** に設定し、より制限の厳しい IP ファイアウォール規則または仮想ネットワーク ファイアウォール規則に置き換えてもかまいません。 

ただし、その場合、仮想ネットワークの一部ではない Azure の仮想マシンで実行され、そのため Azure IP アドレスを介してデータベースに接続される次の機能に影響があります。

### <a name="import-export-service"></a>Import Export Service

**[Azure サービスへのアクセスを許可]** を **[オフ]** に設定すると、インポートまたはエクスポート サービスは機能しません。 ただし、[Azure VM から sqlpackage.exe を手動で実行するか、DACFx API を使用してコード内で直接エクスポートを実行することにより](./database-import-export-azure-services-off.md)、この問題を回避することができます。

### <a name="data-sync"></a>データ同期

**[Azure サービスにサーバーへのアクセスを許可する]** を **[オフ]** に設定してデータ同期機能を使用するには、**ハブ** データベースをホストしているリージョンの **SQL サービス タグ** から [IP アドレスを追加する](firewall-create-server-level-portal-quickstart.md)、個々のファイアウォール規則エントリを作成する必要があります。
これらのサーバーレベルのファイアウォール規則を、**ハブ** と **メンバー** の両方のデータベース (異なるリージョンに存在する可能性がある) をホストするサーバーに追加します。

次の PowerShell スクリプトを使用して、米国西部リージョンの SQL サービス タグに対応する IP アドレスを生成します。

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Location パラメーターを指定しても、Get-AzNetworkServiceTag は SQL サービス タグのグローバル範囲を返します。 それを必ずフィルターに掛けて、同期グループによって使用されるハブ データベースをホストするリージョンを見つけます

PowerShell スクリプトの出力は、クラスレス ドメイン間ルーティング (CIDR) 表記であることに注意してください。 これは、次のように [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) を使用して、開始と終了の IP アドレスの形式に変換する必要があります。

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

この追加 PowerShell スクリプトを使用し、すべての IP アドレスを CIDR から開始と終了の IP アドレスの形式に変換できます。

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

これで、これらを個別のファイアウォール規則として追加し、 **[Azure サービスにサーバーへのアクセスを許可する]** を [オフ] に設定することができます。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則

IP ベースのファイアウォールは、Azure の論理 SQL サーバーの機能であり、明示的にクライアント マシンの [IP アドレスを追加](firewall-create-server-level-portal-quickstart.md)するまで、サーバーへのすべてのアクセスを遮断します。

## <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォールの規則

サーバー ファイアウォールでは、IP 規則だけでなく、"*仮想ネットワーク規則*" を定義することもできます。  
詳細については、[Azure SQL Database の仮想ネットワーク サービス エンドポイントと規則](vnet-service-endpoint-rule-overview.md)に関する記事をお読みいただくか、この動画をご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure のネットワーク用語

仮想ネットワーク ファイアウォール規則について調べる際には、次の Azure のネットワーク用語に注意してください

**仮想ネットワーク:** ご自分の Azure サブスクリプションに仮想ネットワークを関連付けることができます

**サブネット:** 仮想ネットワークには **サブネット** が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 この記事では、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。

**仮想ネットワーク規則:** お使いのサーバーの仮想ネットワーク規則は、サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 SQL Database のデータベースの ACL に含まれるためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。 仮想ネットワーク規則は、サブネット上にあるどのノードからの通信も許可するように、お使いのサーバーに指示します。

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 規則と仮想ネットワーク ファイアウォールの規則

Azure SQL Database のファイアウォールでは、SQL Database への通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 ただし、Azure プライベート ネットワーク内の仮想マシン (VM) では、"*動的*" IP アドレスが構成されます。 VM を再起動したときに動的 IP アドレスが変化し、その結果 IP ベースのファイアウォール規則が無効になることがあります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

この制限は、VM の "*静的*" IP アドレスを取得することで回避できます。 詳細については、「[Azure portal を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)」を参照してください。 ただし、静的 IP の方法は管理が困難になる場合があり、大規模に実行した場合、コストがかかります。

仮想ネットワーク規則は、対象の VM を含む特定のサブネットからのアクセスを確立および管理するためのより簡単な代替手段です。

> [!NOTE]
> サブネット上に SQL Database を保持することは、まだできません。 サーバーが仮想ネットワーク内のサブネット上のノードになった場合、仮想ネットワークはお使いの SQL Database と通信できます。 この場合、仮想ネットワーク規則や IP ルールがなくても、VM は SQL Database と通信できます。

## <a name="private-link"></a>Private Link

プライベート リンクを使用すると、**プライベート エンドポイント** 経由でサーバーに接続できます。 プライベート エンドポイントは、特定の[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)およびサブネット内のプライベート IP アドレスです。

## <a name="next-steps"></a>次のステップ

- サーバーレベルの IP ファイアウォール規則の作成に関するクイックスタートについては、[SQL データベースのデータベース作成](single-database-create-quickstart.md)に関するページを参照してください。

- サーバーレベルの仮想ネットワーク ファイアウォール規則の作成のクイックスタートについては、[Azure SQL Database 用の仮想ネットワーク サービス エンドポイントと規則](vnet-service-endpoint-rule-overview.md)に関するページを参照してください。

- オープンソースまたはサードパーティ製のアプリケーションから SQL Database のデータベースに接続する方法のヘルプについては、[SQL Database のクライアント クイックスタート コード サンプル](/previous-versions/azure/ee336282(v=azure.100))に関するページを参照してください。

- 他に開くことが必要な可能性のあるポートの詳細については、**SQL Database の外部と内部** に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](adonet-v12-develop-direct-route-ports.md)に関する記事で確認してください。

- Azure SQL Database 接続の概要については、「[Azure SQL の接続アーキテクチャ](connectivity-architecture.md)」を参照してください

- Azure SQL Database のセキュリティの概要については、「[データベースの保護](security-overview.md)」を参照してください。

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
