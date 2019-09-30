---
title: データベース ワークロードの宛先として Azure SQL Database マネージド インスタンスを使用した SSIS の移行 | Microsoft Docs
description: データベース ワークロードの宛先として Azure SQL Database マネージド インスタンスを使用した SSIS の移行。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c1bb056909641ad0b8654e84f35c4fbd5b38db82
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968445"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>データベース ワークロードの宛先として Azure SQL Database マネージド インスタンスを使用した SSIS の移行

オンプレミスの SQL Server から Azure SQL Database マネージド インスタンスにデータベース ワークロードを移行する場合は、[Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) (DMS) と、[DMS を使用した Azure SQL Database マネージド インスタンスの移行のネットワーク トポロジ](https://docs.microsoft.com/azure/dms/resource-network-topologies)を理解している必要があります。

この記事では、SSIS カタログ (SSISDB) に格納されている SQL Server Integration Service (SSIS) パッケージと SSIS パッケージの実行をスケジュールする SQL Server エージェント ジョブの移行に焦点を当てています。

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS カタログ (SSISDB) を移行する

SSISDB の移行は、次の記事で説明されているように、DMS を使用して行うことができます。「[SSIS パッケージを Azure SQL Database マネージド インスタンスに移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)」。

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Azure SQL Database マネージド インスタンス エージェントへの SSIS ジョブ

Azure SQL Database マネージド インスタンスには、オンプレミスの SQL Server エージェントと同様に、ネイティブの第一級のスケジューラがあります。  SSIS ジョブの移行ツールはまだ使用できないため、スクリプト/手動コピーを使用して、オンプレミスの SQL Server エージェントから Azure SQL Database マネージド インスタンス エージェントに移行する必要があります。

## <a name="additional-resources"></a>その他のリソース

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [DMS を使用して Azure SQL Database マネージド インスタンスを移行するためのネットワーク トポロジ](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS パッケージを Azure SQL Database マネージド インスタンスに移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>次の手順

- [Azure での SSISDB への接続](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure にデプロイされた SSIS パッケージを実行する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
