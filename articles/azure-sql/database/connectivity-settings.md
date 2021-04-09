---
title: Azure SQL Database と Azure Synapse Analytics の接続設定
description: この記事では、トランスポート層セキュリティ (TLS) のバージョンの選択と、Azure SQL Database および Azure Synapse Analytics のプロキシとリダイレクトの設定について説明します。
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 84fdca96f2ce42c608e7def98f6a3400964cfe46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101691928"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL の接続設定
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

この記事では、Azure SQL Database と Azure Synapse Analytics のサーバーへの接続を制御する設定について説明します。 これらの設定は、サーバーに関連付けられているすべての SQL Database および Azure Synapse Analytics データベースに適用されます。

> [!IMPORTANT]
> この記事は、Azure SQL Managed Instance には適用されません。

接続設定は、次のスクリーンショットに示すように、 **[ファイアウォールと仮想ネットワーク]** ブレードからアクセスできます。

 ![接続設定ウィンドウのスクリーンショット。][1]

> [!NOTE]
> これらの設定は、適用後すぐに有効になります。 各設定の要件を満たしていない場合、顧客の接続が失われるおそれがあります。

## <a name="deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否

**[パブリック ネットワーク アクセスの拒否]** が **[はい]** に設定されている場合、プライベート エンドポイント経由の接続のみが許可されます。 この設定が **[いいえ]** (既定) に設定されている場合、[ネットワーク アクセスの概要](network-access-controls-overview.md)に記載されているように、お客様はパブリック エンドポイント (IP ベースのファイアウォール規則または仮想ネットワーク ベースのファイアウォール規則を使用) またはプライベート エンドポイント (Azure Private Link を使用) のいずれかを使用して接続できます。

 ![[パブリック ネットワーク アクセスの拒否] を [はい] に設定した場合と、[パブリック ネットワーク アクセスの拒否] を [いいえ] に設定した場合の接続を示す図。][2]

論理サーバーにプライベート エンドポイントが存在しない状態で **[パブリック ネットワーク アクセスの拒否]** を **[はい]** に設定しようとすると失敗し、次のようなエラー メッセージが表示されます。  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> プライベート エンドポイントを使用して既に構成されている論理サーバー上で仮想ネットワークのファイアウォール規則を定義するには、 **[パブリック ネットワーク アクセスの拒否]** を **[いいえ]** に設定します。

**[パブリック ネットワーク アクセスの拒否]** が **[はい]** に設定されている場合、プライベート エンドポイント経由の接続のみが許可されます。 パブリック エンドポイント経由のすべての接続が拒否され、次のようなエラー メッセージが表示されます。  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

**[パブリック ネットワーク アクセスの拒否]** を **[はい]** に設定後、ファイアウォール規則を追加したり、更新したりすると、すべて拒否され、次のようなエラー メッセージが表示されます。

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell を使用してパブリック ネットワーク アクセスを変更する

> [!IMPORTANT]
> Azure SQL Database では、PowerShell Azure Resource Manager モジュールが引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、**パブリック ネットワーク アクセス** プロパティをサーバー レベルで `Get` および `Set` する方法を示しています。

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>CLI を使用してパブリック ネットワーク アクセスを変更する

> [!IMPORTANT]
> このセクションのすべてのスクリプトには [Azure CLI](/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>Bash シェルでの Azure CLI

次の CLI スクリプトは、Bash シェルでの **パブリック ネットワーク アクセス** 設定の変更方法を示しています。

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>TLS の最小バージョン 

最小[トランスポート層セキュリティ (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) バージョン設定を使用すると、顧客が自身の SQL データベースで使用する TLS のバージョンを選択することができます。

現在、TLS 1.0、1.1、1.2 がサポートされています。 最小 TLS バージョンを設定すると、それより新しい TLS バージョンは確実にサポートされます。 たとえば、TLS バージョン 1.1 を選択した場合、TLS 1.1 と 1.2 を使用した接続のみが受け入れられ、TLS 1.0 を使用した接続は拒否されます。 アプリケーションでサポートされているかどうかをテストした後、最小 TLS バージョンを 1.2 に設定することをお勧めします。 このバージョンには、以前のバージョンの脆弱性に対する修正プログラムが含まれており、Azure SQL Database でサポートされている TLS の最上位バージョンです。

> [!IMPORTANT]
> 最小 TLS バージョンの既定値は、すべてのバージョンを許可することです。 TLS のバージョンを適用した後で既定値に戻すことはできません。

以前のバージョンの TLS に依存するアプリケーションを使用しているお客様には、アプリケーションの要件に従って最小 TLS バージョンを設定することをお勧めします。 暗号化されていない接続を使用して接続するアプリケーションに依存しているお客様は、最少 TLS バージョンを設定しないことをお勧めします。

詳細については、「[SQL Database の接続に関する TLS の考慮事項](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)」を参照してください。

最小 TLS バージョンを設定すると、サーバーの最小 TLS バージョンより低い TLS バージョンを使用しているお客様からのログイン試行は、次のエラーで失敗します。

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>PowerShell を使用して最小 TLS バージョンを設定する

> [!IMPORTANT]
> Azure SQL Database では、PowerShell Azure Resource Manager モジュールが引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、**最少 TLS バージョン** プロパティを論理サーバー レベルで `Get` および `Set` する方法を示しています。

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Azure CLI を使用して最小 TLS バージョンを設定する

> [!IMPORTANT]
> このセクションのすべてのスクリプトには [Azure CLI](/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>Bash シェルでの Azure CLI

次の CLI スクリプトは、Bash シェルでの **最少 TLS バージョン** の変更方法を示しています。

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>接続ポリシーを変更する

お客様が Azure SQL Database に接続する方法は、[接続ポリシー](connectivity-architecture.md#connection-policy)によって決まります。

## <a name="change-the-connection-policy-via-powershell"></a>PowerShell を使用して接続ポリシーを変更する

> [!IMPORTANT]
> Azure SQL Database では、PowerShell Azure Resource Manager モジュールが引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、PowerShell を使用して接続ポリシーを変更する方法を示しています。

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Azure CLI を使用して接続ポリシーを変更する

> [!IMPORTANT]
> このセクションのすべてのスクリプトには [Azure CLI](/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>Bash シェルでの Azure CLI

次の CLI スクリプトは、Bash シェルでの接続ポリシーの変更方法を示しています。

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

次の CLI スクリプトは、(Azure CLI がインストールされている場合に) Windows コマンド プロンプトから接続ポリシーを変更する方法を示しています。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>次のステップ

- Azure SQL Database で接続がどのように機能するかに関する概要については、[接続のアーキテクチャ](connectivity-architecture.md)に関するページを参照してください。
- サーバーの接続ポリシーの変更方法については、「[conn-policy](/cli/azure/sql/server/conn-policy)」を参照してください。

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
