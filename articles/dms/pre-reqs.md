---
title: Azure Database Migration Service を使用するための前提条件の概要 | Microsoft Docs
description: データベースの移行に Azure Database Migration Service を使用するための前提条件の概要について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 6e26ae7dd8637a71b9f4e2f6ea1ed6063cf0ec40
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181199"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Azure Database Migration Service を使用するための前提条件の概要
データベースを移行するときに Azure Database Migration Service を円滑に動作させるには、いくつかの前提条件があります。 いくつかの前提条件は、サービスでサポートされているすべてのシナリオ (ソースとターゲットのペア) に適用されますが、その他の前提条件は特定のシナリオに固有のものです。

Azure Database Migration Service の使用に関連する前提条件は、以降のセクションに記載されています。

## <a name="prerequisites-common-across-migration-scenarios"></a>移行の複数のシナリオに共通の前提条件
サポートされているすべての移行シナリオで共通の、Azure Database Migration Service の前提条件は、次のとおりです。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。
- Azure Virtual Network (VNET) のネットワーク セキュリティ グループの規則によって、通信ポート 443、53、9354、445、12000 がブロックされていないことを確認します。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>SQL Server から Azure SQL Database への移行の前提条件 
すべての移行シナリオに共通する Azure Database Migration Service の前提条件の他に、特定のシナリオだけに適用される前提条件もあります。

Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を実行する場合は、すべての移行シナリオに共通する前提条件の他に、次の追加の前提条件にも対応してください。

- Azure SQL データベース インスタンスを作成します。詳細な手順については、「[Azure Portal で Azure SQL データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)」を参照してください。
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールします。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- Azure SQL Database サーバーのサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNET のサブネット範囲を指定します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL データベース インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。

   > [!NOTE]
   > Azure Database Migration Service を使用して SQL Server から Azure SQL Database への移行を行うために必要な前提条件の完全な一覧については、チュートリアルの「[SQL Server を Azure SQL Database に移行する](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)」を参照してください。
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>SQL Server から Azure SQL Database Managed Instance への移行の前提条件
- Azure SQL Database マネージド インスタンスを作成します。手順の詳細については、「[Azure ポータルで Azure SQL Database マネージド インスタンスを作成する](https://aka.ms/sqldbmi)」を参照してください。
- Azure Database Migration Service の IP アドレスまたはサブネット範囲でポート 445 の SMB トラフィックを許可するようにファイアウォールを開きます。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース SQL Server への接続と、ターゲットのマネージド インスタンスに使用するログインが sysadmin サーバー ロールのメンバーであることを確認してください。
- Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
- 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
- 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。
- Blob コンテナーを作成し、「[ストレージ エクスプローラー (プレビュー) を使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)」の記事に記載されている手順を使用して、その SAS URI を取得します。 SAS URI を作成するときには、必ず、[ポリシー] ウィンドウですべての権限 (読み取り、書き込み、削除、一覧表示) を選択してください。

   > [!NOTE]
   > Azure Database Migration Service を使用して SQL Server から Azure SQL Database Managed Instance への移行を行うために必要な前提条件の完全な一覧については、チュートリアルの「[SQL Server を Azure SQL Database Managed Instance に移行する](https://aka.ms/migratetomiusingdms)」を参照してください。

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事をご覧ください。 