---
title: Azure Database Migration Service を使用するための前提条件の概要 | Microsoft Docs
description: データベースの移行に Azure Database Migration Service を使用するための前提条件の概要について説明します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304255"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Database Migration Service を使用するための前提条件の概要

データベースを移行するときに Azure Database Migration Service を円滑に動作させるには、いくつかの前提条件があります。 いくつかの前提条件は、サービスでサポートされているすべてのシナリオ (ソースとターゲットのペア) に適用されますが、その他の前提条件は特定のシナリオに固有のものです。

Azure Database Migration Service の使用に関連する前提条件は、以降のセクションに記載されています。

## <a name="prerequisites-common-across-migration-scenarios"></a>移行の複数のシナリオに共通の前提条件

サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。

* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。
* VNet のネットワーク セキュリティ グループ (NSG) の規則によって、443、53、9354、445、12000 の各通信ポートがブロックされていないことを確認します。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。

    > [!IMPORTANT]
    > Azure Database Migration Service のインスタンスを作成するには、通常は同じリソース グループ内にない VNet 設定にアクセスする必要があります。 そのため、DMS のインスタンスを作成するユーザーには、サブスクリプション レベルでのアクセス許可が必要です。 必要なロール (ロールは必要に応じて割り当て可能) を作成するには、次のスクリプトを実行します。
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server から Azure SQL Database への移行の前提条件

すべての移行シナリオに共通する Azure Database Migration Service の前提条件の他に、特定のシナリオだけに適用される前提条件もあります。

Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を実行する場合は、すべての移行シナリオに共通する前提条件の他に、次の追加の前提条件にも対応してください。

* Azure SQL データベース インスタンスを作成します。詳細な手順については、「[Azure Portal で Azure SQL データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)」を参照してください。
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールします。
* Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
* 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
* Azure SQL Database サーバーのサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNET のサブネット範囲を指定します。
* ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
* ターゲットの Azure SQL データベース インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。

   > [!NOTE]
   > Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を行うために必要な前提条件の完全な一覧については、チュートリアルの「[SQL Server を Azure SQL Database に移行する](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)」を参照してください。
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>SQL Server から Azure SQL Database Managed Instance への移行の前提条件

* Azure SQL Database マネージド インスタンスを作成します。手順の詳細については、[Azure portal で Azure SQL Database マネージド インスタンスを作成する方法](https://aka.ms/sqldbmi)に関する記事を参照してください。
* Azure Database Migration Service の IP アドレスまたはサブネット範囲でポート 445 の SMB トラフィックを許可するようにファイアウォールを開きます。
* Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
* 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
* ソース SQL Server への接続と、ターゲットのマネージド インスタンスに使用するログインが sysadmin サーバー ロールのメンバーであることを確認してください。
* Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
* 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
* 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。
* Blob コンテナーを作成し、「[ストレージ エクスプローラー (プレビュー) を使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)」の記事に記載されている手順を使用して、その SAS URI を取得します。 SAS URI を作成するときには、必ず、[ポリシー] ウィンドウですべての権限 (読み取り、書き込み、削除、一覧表示) を選択してください。

   > [!NOTE]
   > Azure Database Migration Service を使用して SQL Server から Azure SQL Database Managed Instance への移行を行うために必要な前提条件の完全な一覧については、チュートリアルの「[SQL Server を Azure SQL Database Managed Instance に移行する](https://aka.ms/migratetomiusingdms)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事をご覧ください。
