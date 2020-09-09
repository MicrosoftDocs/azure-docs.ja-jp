---
title: Contoso 移行シリーズ | Microsoft Docs
description: Azure への移行に関する Contoso の移行シナリオの例へのリンクです。
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107633"
---
# <a name="contoso-migration-series"></a>Contoso 移行シリーズ


架空の組織 Contoso がそのオンプレミスのインフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) クラウドに移行する方法を示す、一連の記事を用意しました。 

このシリーズには、インフラストラクチャの移行を設定する方法や、さまざまな種類の移行を実行する方法を示したシナリオが含まれています。 進むにつれて、シナリオは複雑になってきています。 この記事では、Contoso 社が移行を処理する方法を示しますが、一般的な指示とポインターは全体を通じて提供されます。

## <a name="migration-articles"></a>移行に関する記事

シリーズの記事を次の表にまとめています。  

- 各移行シナリオは、少しずつ異なるビジネス要件の影響を受けます。これによって移行戦略が決まります。
- 各デプロイ シナリオでは、ビジネス ドライバーと目標、提案されたアーキテクチャ、移行を実行するための手順、クリーンアップの推奨事項、移行完了後の次の手順についての情報を提供します。


**記事** | **詳細** 
--- | --- 
[記事 1:概要](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 
[記事 2: Azure インフラストラクチャをデプロイする](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso がそのオンプレミスのインフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズのすべての記事で同じインフラストラクチャを使用します。 
[記事 3:Azure への移行の対象となるオンプレミスのリソースの評価](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。
[記事 4: Azure VM および SQL Managed Instance でのアプリのリホスト](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Migrate](./migrate-services-overview.md) を使用してアプリのフロントエンド VM を移行します。 Contoso は、[Azure Database Migration Service](../dms/dms-overview.md) を使用して、アプリ データベースを SQL Managed Instance に移行します。
[記事 5:Azure VM でのアプリのリホスト](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso が Azure Migrate サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 
[記事 6:Azure VM および SQL Server AlwaysOn 可用性グループでのアプリのリホスト](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso が SmartHotel360 アプリを移行します。 Contoso は、Azure Migrate を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 
[記事 7:Linux アプリの Azure VM への再ホスト](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso が Azure Migrate を使用して、Azure VM への Linux osTicket アプリのリフトアンドシフト移行を完了します。
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso が Azure Migrate を使用して Linux osTicket アプリを Azure VM に移行します。 Azure Database Migration Service (MySQL Workbench を使用した別のオプションを含む) を使用して、アプリ データベースを Azure Database for MySQL に移行します。
[記事 9:Azure Web App と Azure SQL Database 内でアプリをリファクタリングする](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行し、また、Azure Database Migration Service を使用してアプリ データベースを Azure SQL Database に移行します。
[記事 10:Azure App Service と SQL Managed Instance を使用して Windows アプリをリファクタリングする](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso がオンプレミスの Windows ベース アプリを Azure Web アプリに移行し、また、Azure Database Migration Service を使用してアプリ データベースを Azure SQL Database Managed Instance に移行する方法を示します。
[記事 11:Azure Web アプリと Azure Database for MySQL での Linux アプリのリファクター](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。これは、継続的デリバリーのために GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 
[記事 12:Azure DevOps サービスでの Team Foundation Server をリファクタリングします](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。
[記事 13:Azure でのアプリのリビルド](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。
[記事 14:Azure への移行のスケーリング](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。



## <a name="next-steps"></a>次のステップ

- クラウド移行について[学習します](/azure/architecture/cloud-adoption/migrate/)。
- [データベース移行ガイド](https://datamigration.microsoft.com/)の他のシナリオ (ソース/ターゲットのペア) に対する移行戦略を学習します。
