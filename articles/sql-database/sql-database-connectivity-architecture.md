---
title: Azure SQL Database と SQL Data Warehouse の接続アーキテクチャ | Microsoft Docs
description: このドキュメントでは、Azure 内外からのデータベース接続のための Azure SQL の接続アーキテクチャを説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 8441e64981b7157e91a56124a08c0aa02a9b1db0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537933"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL の接続アーキテクチャ

この記事では Azure SQL Database および SQL Data Warehouse の接続アーキテクチャのほか、さまざまなコンポーネントがどのように機能し、トラフィックが Azure SQL インスタンスに送信されるか説明します。 これらの接続コンポーネントが機能し、Azure 内外からクライアントが接続する Azure SQL Database または SQL Data Warehouse にネットワーク トラフィックが送信されます。 この記事では、接続方法を変更するためのスクリプト サンプルと、既定の接続設定の変更に関連する考慮事項も提供します。

## <a name="connectivity-architecture"></a>接続のアーキテクチャ

次の図は、Azure SQL Database の接続アーキテクチャの概要です。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-overview.png)

次の手順では、Azure SQL データベースへの接続を確立する方法について説明します。

- クライアントはゲートウェイに接続します。ゲートウェイにはパブリック IP アドレスが与えられており、ポート 1433 で待ち受けます。
- 有効な接続ポリシーによっては、ゲートウェイがトラフィックを正しいデータベース クラスターにリダイレクトまたはプロキシします。
- データベース クラスター内では、トラフィックは適切な Azure SQL データベースに転送されます。

## <a name="connection-policy"></a>接続ポリシー

Azure SQL Database は、SQL Database サーバーの接続ポリシー設定について次の 3 つのオプションをサポートしています。

- **リダイレクト (推奨):** クライアントは、データベースをホストしているノードへの直接接続を確立します。 接続を有効にするには、ポート 1433 上の Azure SQL Database ゲートウェイの IP アドレスだけでなく、ネットワーク セキュリティ グループ (NSG) と[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して、11000 から 11999 のポートのリージョン内のすべての Azure IP アドレスに対する送信ファイアウォール ルールを、クライアントで許可する必要があります。 パケットはデータベースに直接送信されるため、待機時間とスループットのパフォーマンスが改善されます。
- **プロキシ:** このモードでは、すべての接続が Azure SQL Database ゲートウェイ経由でプロキシされます。 接続を有効にするには、Azure SQL Database ゲートウェイの IP アドレスのみ (通常はリージョンあたり 2 つの IP アドレス) を許可する送信ファイアウォール規則がクライアントに必要です。 このモードを選択すると、ワークロードの性質によっては待機時間が長くなり、スループットが低下する可能性があります。 最短の待機時間と最高のスループットを実現するために、`Proxy` 接続ポリシーではなく `Redirect` 接続ポリシーを強くお勧めします。
- **既定:** これは、明示的に接続ポリシーを `Proxy` または `Redirect` に変更しない限り、作成後のすべてのサーバーで有効になる接続ポリシーです。 有効なポリシーは、接続が Azure (`Redirect`) 内か Azure (`Proxy`) の外部かによって変わります。

## <a name="connectivity-from-within-azure"></a>Azure 内からの接続

Azure 内から接続する場合、接続には既定で `Redirect` の接続ポリシーが与えられます。 `Redirect` のポリシーとは、TCP セッションが Azure SQL データベースに対して確立された後に、宛先の仮想 IP を Azure SQL Database ゲートウェイの IP からクラスターの IP に変更して、正しいデータベース クラスターにリダイレクトすることを意味します。 その後、すべての後続パケットは Azure SQL Database ゲートウェイを迂回し、クラスターに直接送信されます。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 外からの接続

Azure 外から接続する場合、接続には既定で `Proxy` の接続ポリシーが与えられます。 `Proxy` のポリシーとは、TCP セッションが Azure SQL Database ゲートウェイ経由で確立し、すべての後続パケットがゲートウェイ経由で送信されることを意味します。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database ゲートウェイ IP アドレス

以下の表は、ゲートウェイの IP アドレスをリージョン別にまとめたものです。 Azure SQL Database に接続するには、そのリージョンの**すべての**ゲートウェイとの間でやり取りされるネットワーク トラフィックを許可する必要があります。

今後、以下の表に各リージョンにさらにゲートウェイを追加し、「使用が停止されたゲートウェイの IP アドレス」列に使用が停止されたゲートウェイを記載します。 使用停止プロセスの詳細については、次の記事を参照してください。「[Azure SQL Database traffic migration to newer Gateways](sql-database-gateway-migration.md)」(Azure SQL Database トラフィックの新しいゲートウェイへの移行)


| リージョン名          | ゲートウェイ IP アドレス | 使用が停止されたゲートウェイの </br> IP アドレス| 使用停止に関する備考 | 
| --- | --- | --- | --- |
| オーストラリア東部       | 13.75.149.87、40.79.161.1 | | |
| オーストラリア東南部 | 191.239.192.109、13.73.109.251 | | |
| ブラジル南部         | 104.41.11.5        |                 | |
| カナダ中部       | 40.85.224.249      |                 | |
| カナダ東部          | 40.86.226.166      |                 | |
| 米国中部           | 13.67.215.62、52.182.137.15 | 23.99.160.139 | 2019 年 9 月 1 日以降は接続なし |
| 中国東部 1         | 139.219.130.35     |                 | |
| 中国東部 2         | 40.73.82.1         |                 | |
| 中国北部 1        | 139.219.15.17      |                 | |
| 中国北部 2        | 40.73.50.0         |                 | |
| 東アジア            | 191.234.2.139、52.175.33.150 |       | |
| 米国東部 1            | 40.121.158.30、40.79.153.12 | 191.238.6.43 | 2019 年 9 月 1 日以降は接続なし |
| 米国東部 2            | 40.79.84.180、52.177.185.181、52.167.104.0 | 191.239.224.107    | 2019 年 9 月 1 日以降は接続なし |
| フランス中部       | 40.79.137.0、40.79.129.1 |           | |
| ドイツ中部      | 51.4.144.100       |                 | |
| ドイツ北東部   | 51.5.144.179       |                 | |
| インド中部        | 104.211.96.159     |                 | |
| インド南部          | 104.211.224.146    |                 | |
| インド西部           | 104.211.160.80     |                 | |
| 東日本           | 13.78.61.196、40.79.184.8、13.78.106.224 | 191.237.240.43 | 2019 年 9 月 1 日以降は接続なし |
| 西日本           | 104.214.148.156、40.74.100.192 | 191.238.68.11 | 2019 年 9 月 1 日以降は接続なし |
| 韓国中部        | 52.231.32.42       |                 | |
| 韓国南部          | 52.231.200.86      |                 | |
| 米国中北部     | 23.96.178.199      | 23.98.55.75     | 2019 年 9 月 1 日以降は接続なし |
| 北ヨーロッパ         | 40.113.93.91       | 191.235.193.75  | 2019 年 9 月 1 日以降は接続なし |
| 米国中南部     | 13.66.62.124       | 23.98.162.75    | 2019 年 9 月 1 日以降は接続なし |
| 東南アジア      | 104.43.15.0        | 23.100.117.95   | 2019 年 9 月 1 日以降は接続なし |
| 英国南部             | 51.140.184.11      |                 | |
| 英国西部              | 51.141.8.11        |                 | |
| 米国中西部      | 13.78.145.25       |                 | |
| 西ヨーロッパ          | 191.237.232.75、40.68.37.158 |       | |
| 米国西部 1            | 23.99.34.75、104.42.238.205 |        | |
| 米国西部 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Database 接続ポリシーを変更する

Azure SQL Database サーバーの Azure SQL Database 接続ポリシーを変更するには、[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) コマンドを使用します。

- 接続ポリシーを `Proxy` に設定すると、すべてのネットワーク パケットが Azure SQL Database ゲートウェイ経由で送信されます。 この設定の場合、Azure SQL Database ゲートウェイ IP のみに送信を許可する必要があります。 `Proxy` の設定を利用すると、`Redirect` の設定より待ち時間が長くなります。
- 接続ポリシーで `Redirect` を設定すると、すべてのネットワーク パケットがデータベース クラスターに直接送信されます。 この設定の場合、複数の IP への送信を許可する必要があります。

## <a name="script-to-change-connection-settings-via-powershell"></a>接続の設定を変更する PowerShell のスクリプト

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、接続ポリシーの変更方法を示しています。

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>接続の設定を変更する Azure CLI のスクリプト

> [!IMPORTANT]
> このスクリプトは [Azure CLI ](https://docs.microsoft.com/cli/azure/install-azure-cli) を必要とします。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI

> [!IMPORTANT]
> このスクリプトは [Azure CLI ](https://docs.microsoft.com/cli/azure/install-azure-cli) を必要とします。

次の CLI スクリプトは、bash シェルでの接続ポリシーの変更方法を示しています。

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows のコマンド プロンプトからの Azure CLI

> [!IMPORTANT]
> このスクリプトは [Azure CLI ](https://docs.microsoft.com/cli/azure/install-azure-cli) を必要とします。

次の CLI スクリプトは、(Azure CLI がインストールされている場合に) Windows コマンド プロンプトから接続ポリシーを変更する方法を示しています。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>次の手順

- Azure SQL Database サーバーの Azure SQL Database 接続ポリシーの変更方法については、「[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)」を参照してください。
- ADO.NET 4.5 以降のバージョンを使用するクライアントの Azure SQL Database 接続動作については、「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- 一般的なアプリケーション開発概要情報については、「[SQL Database アプリケーションの開発の概要](sql-database-develop-overview.md)」を参照してください。