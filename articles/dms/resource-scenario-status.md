---
title: データベース移行シナリオの状態 | Microsoft Docs
description: Azure Database Migration Service によってサポートされる移行シナリオの状態について学習します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473010"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service によってサポートされる移行シナリオの状態
Azure Database Migration Service は、オフライン (1 回限り) とオンライン (継続的同期) 両方のさまざまな移行シナリオ (ソース/ターゲットのペア) をサポートするように設計されています。 Azure Database Migration Service が対応するシナリオの範囲は、徐々に広がっています。 定期的に新しいシナリオが追加されています。 この記事では、Azure Database Migration Service で現在サポートされている移行シナリオと、各シナリオの状態 (プライベート プレビュー、パブリック プレビュー、または一般提供) を示します。

## <a name="offline-versus-online-migrations"></a>オフライン移行とオンライン移行
Azure Database Migration Service では、オフラインまたはオンラインの移行を行えます。 "*オフライン*" 移行では、移行開始と同時にアプリケーションのダウンタイムが始まります。 ダウンタイムを、移行が完了して新しい環境に切り替えるために必要な時間に抑えるには、"*オンライン*" 移行を使用します。 オフライン移行をテストして、ダウンタイムが許容可能かどうかを判断することをお勧めします。できない場合は、オンライン移行を行います。

## <a name="migration-scenario-status"></a>データベース移行シナリオの状態
Azure Database Migration Service によってサポートされる移行シナリオの状態は、時間と共に変化します。 一般に、シナリオは最初**プライベート プレビュー**でリリースされます。 プライベート プレビューに参加する場合、お客様は [DMS プレビュー サイト](https://aka.ms/dms-preview)を通じて申請を送信する必要があります。 プライベート プレビューの後、シナリオの状態は**パブリック プレビュー**に変わります。 Azure Database Migration Service のユーザーは、ユーザー インターフェイスから直接、パブリック プレビューの移行シナリオを試すことができます。 サインアップは必要ありません。  ただし、パブリック プレビューの移行シナリオはすべてのリージョンで使用できるわけではなく、最終リリース前にさらに変更が行われる可能性があります。 パブリック プレビューの後、シナリオの状態は**一般提供**に変わります。 一般提供 (GA) は最終的なリリースの状態です。機能が完成しており、すべてのユーザーが利用できます。

## <a name="migration-scenario-support"></a>移行シナリオのサポート
次の表では、Azure Database Migration Service を使用する場合にサポートされる移行シナリオを示します。

> [!NOTE]
> 以下の一覧でサポートされるようになっているシナリオが、ユーザー インターフェイスに表示されない場合は、[データ移行チーム](mailto:datamigrationteam@microsoft.com)に詳細をお問い合わせください。

> [!IMPORTANT]
> Azure Database Migration Service で現在サポートされているプライベート プレビューのすべてのシナリオを確認するには、[DMS プレビュー サイト](https://aka.ms/dms-preview)を参照してください。

### <a name="offline-one-time-migration-support"></a>オフライン (1 回限り) の移行のサポート
次の表では、オフライン移行に対する Azure Database Migration Service のサポートを示します。

| ターゲット  | source | サポート | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | 一般公開 |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | 一般公開 |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | 一般公開 |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | パブリック プレビュー |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>オンライン (継続的同期) の移行のサポート
次の表では、オンライン移行に対する Azure Database Migration Service のサポートを示します。

| ターゲット  | source | サポート | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | 一般公開 |
|   | RDS SQL | ✔ | 一般公開 |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | 一般公開 |
|   | RDS SQL | ✔ | 一般公開 |
|   | Oracle | ✔ | プライベート プレビュー |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | パブリック プレビュー |
| **Azure DB for MySQL** | MySQL | ✔ | 一般公開 |
|   | RDS MySQL | ✔ | 一般公開 |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | 一般公開 |
|   | RDS PostgreSQL | ✔ | 一般公開 |
|   | Oracle | ✔ | プライベート プレビュー |

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事を参照してください。
