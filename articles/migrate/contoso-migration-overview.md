---
title: Contoso 移行シリーズ | Microsoft Docs
description: Contoso が自社のオンプレミスのデータセンターを Azure に移行するときに使用する移行戦略とシナリオの概要について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: 82585a6f1df369e299bd9df4d57f6106aad4ead7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696214"
---
# <a name="contoso-migration-series"></a>Contoso 移行シリーズ


架空の組織 Contoso がオンプレミスのインフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) クラウドに移行する方法を示す、一連の記事があります。 

このシリーズには、インフラストラクチャの移行を設定する方法や、さまざまな種類の移行を実施する方法を示した情報とシナリオが含まれています。 進むにつれて、シナリオは複雑になってきています。 これらの記事は Contoso 社が移行作業を遂行する方法を示すものですが、全体を通じて、一般読者向けのヒントや特定の手順も記載しています。

## <a name="migration-articles"></a>移行に関する記事

シリーズの記事を次の表にまとめています。  

- 各移行シナリオは、少しずつ異なるビジネス目標に沿って作成されています。このビジネス目標によって移行戦略が決まります。
- 各デプロイ シナリオでは、ビジネス ドライバーと目標、提案されたアーキテクチャ、移行を実施するための手順、クリーンアップの推奨事項、移行完了後の次の手順についての情報が記載されています。

**記事** | **詳細** 
--- | --- 
[記事 1:概要](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 
[記事 2: Azure インフラストラクチャをデプロイする](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 
[記事 3:Azure への移行についてオンプレミスのリソースを評価する](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。
[記事 5:Azure VM 上でアプリをリホストする](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 
[記事 6:Azure VM 上および SQL Server AlwaysOn 可用性グループ内でアプリをリホストする](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso が SmartHotel360 アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 
[記事 7:Linux アプリの Azure VM への再ホスト](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso が Site Recovery サービスを使用して、Azure VM への Linux osTicket アプリのリフトアンドシフト移行を完了します。
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。 
[記事 9:Azure Web App と Azure SQL Database 内でアプリをリファクタリングする](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。     
[記事 10:Azure Web アプリと Azure Database for MySQL 内で Linux アプリをリファクタリングする](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 
[記事 11:Azure DevOps サービスでの Team Foundation Server をリファクタリングします](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。
[記事 12:Azure コンテナーと Azure SQL Database 内でアプリを再構築する](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 
[記事 13:Azure でのアプリのリビルド](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。  
[記事 14:Azure への移行のスケーリング](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 


    

## <a name="next-steps"></a>次の手順

クラウド移行について[学習します](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/)。 

