---
title: データベース移行シナリオの状態
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service によってサポートされる移行シナリオの状態について学習します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: fc2ab86a318086750b11780a7802aa3591065264
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463512"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service によってサポートされる移行シナリオの状態

Azure Database Migration Service は、オフライン (1 回限り) とオンライン (継続的同期) 両方のさまざまな移行シナリオ (ソース/ターゲットのペア) をサポートするように設計されています。 Azure Database Migration Service が対応するシナリオの範囲は、徐々に広がっています。 定期的に新しいシナリオが追加されています。 この記事では、Azure Database Migration Service で現在サポートされている移行シナリオと、各シナリオの状態 (プライベート プレビュー、パブリック プレビュー、または一般提供) を示します。

## <a name="offline-versus-online-migrations"></a>オフライン移行とオンライン移行

Azure Database Migration Service では、オフラインまたはオンラインの移行を行えます。 "*オフライン*" 移行では、移行開始と同時にアプリケーションのダウンタイムが始まります。 ダウンタイムを、移行が完了して新しい環境に切り替えるために必要な時間に抑えるには、"*オンライン*" 移行を使用します。 オフライン移行をテストして、ダウンタイムが許容可能かどうかを判断することをお勧めします。できない場合は、オンライン移行を行います。

## <a name="migration-scenario-status"></a>データベース移行シナリオの状態

Azure Database Migration Service によってサポートされる移行シナリオの状態は、時間と共に変化します。 一般に、シナリオは最初 **プライベート プレビュー** でリリースされます。 プライベート プレビューに参加する場合、お客様は [DMS プレビュー サイト](https://aka.ms/dms-preview)を通じて申請を送信する必要があります。 プライベート プレビューの後、シナリオの状態は **パブリック プレビュー** に変わります。 Azure Database Migration Service のユーザーは、ユーザー インターフェイスから直接、パブリック プレビューの移行シナリオを試すことができます。 サインアップは必要ありません。  ただし、パブリック プレビューの移行シナリオはすべてのリージョンで使用できるわけではなく、最終リリース前にさらに変更が行われる可能性があります。 パブリック プレビューの後、シナリオの状態は **一般提供** に変わります。 一般提供 (GA) は最終的なリリースの状態です。機能が完成しており、すべてのユーザーが利用できます。

## <a name="migration-scenario-support"></a>移行シナリオのサポート

次の表では、Azure Database Migration Service を使用する場合にサポートされる移行シナリオを示します。

> [!NOTE]
> 以下の一覧でサポートされるようになっているシナリオが、ユーザー インターフェイスに表示されない場合は、追加情報について [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) エイリアスにお問い合わせください。

> [!IMPORTANT]
> Azure Database Migration Service で現在サポートされているプライベート プレビューのすべてのシナリオを確認するには、[DMS プレビュー サイト](https://aka.ms/dms-preview)を参照してください。

### <a name="offline-one-time-migration-support"></a>オフライン (1 回限り) の移行のサポート

次の表では、オフライン移行に対する Azure Database Migration Service のサポートを示します。

| 移行先  | source | サポート | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | X |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL - 単一サーバー** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL - Hyperscale (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>オンライン (継続的同期) の移行のサポート

次の表では、オンライン移行に対する Azure Database Migration Service のサポートを示します。

| 移行先  | source | サポート | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL VM** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL - 単一サーバー** | PostgreSQL | ✔ | GA |
|   | Azure DB for PostgreSQL - 単一サーバー | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | パブリックプ レビュー (2021 年 5 月 1 以降、非推奨となる予定) |
| **Azure DB for PostgreSQL - Hyperscale (Citus)** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |

> [!IMPORTANT]
> 2021 年 5 月 1 以降、「Oracle から Azure Database for PostgreSQL へ」の移行シナリオ (現在プレビュー段階) は使用できなくなります。 引き続き別のツール (Ora2pg など) を使用してサポートを提供し、Oracle から PostgreSQL への移行に最適な移行エクスペリエンスを提供します。 移行のベスト プラクティスについては、「[Oracle から Azure Database for PostgreSQL への移行ガイド](https://aka.ms/OracletoPGguide)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure Database Migration Service の概要と、リージョンごとの利用可能性については、「[Azure Database Migration Service とは](dms-overview.md)」という記事を参照してください。
