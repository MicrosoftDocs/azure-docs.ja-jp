---
title: 'Azure SQL Database の DTU ベースのリソース制限: 単一データベース | Microsoft Docs'
description: このページでは、Azure SQL Database の単一データベースに対するいくつかの一般的な DTU ベースのリソース制限について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: b3514eca8db5d5b68b3e5784ee95e8583813945c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008972"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>DTU ベースの購入モデルを使用した単一データベースに対するリソース制限

この記事では、DTU ベースの購入モデルを使用した、Azure SQL Database の単一データベースに対する詳細なリソース制限について説明します。

エラスティック プールに対する DTU ベースの購入モデルのリソース制限については、[エラスティック プールに対する DTU ベースのリソース制限](sql-database-dtu-resource-limits-elastic-pools.md)に関するページを参照してください。 [単一データベースに対する仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)および[エラスティック プールに対する仮想コアベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関するページを参照してください。 さまざまな購入モデルについて詳しくは、[購入モデルとサービス レベル](sql-database-purchase-models.md)に関する記事をご覧ください。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>単一データベース:ストレージ サイズとコンピューティング サイズ

次の表では、各サービス レベルおよびコンピューティング サイズにおいて単一データベースで使用可能なリソースを示します。 [Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)、または [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) を使って、単一のデータベースにサービス レベル、コンピューティング サイズ、ストレージ量を設定できます。

> [!IMPORTANT]
> スケーリングのガイダンスと考慮事項については、[単一データベースのスケーリング](sql-database-single-database-scale.md)に関するページを参照してください

### <a name="basic-service-tier"></a>Basic サービス レベル

| **コンピューティング サイズ** | **Basic** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 付属ストレージ (GB) | 2 |
| 選択可能な最大ストレージ容量 (GB) | 2 |
| 最大インメモリ OLTP ストレージ容量 (GB) |該当なし |
| 最大同時実行ワーカー (要求) 数 | 30 |
| 最大同時セッション数 | 300 |
|||

### <a name="standard-service-tier"></a>Standard サービス レベル

| **コンピューティング サイズ** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU 数 | 10 | 20 | 50 | 100 |
| 付属ストレージ (GB) | 250 | 250 | 250 | 250 |
| 選択可能な最大ストレージ容量 (GB) | 250 | 250 | 250 | 250、500、750、1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | 該当なし | 該当なし | 該当なし |
| 最大同時実行ワーカー (要求) 数| 60 | 90 | 120 | 200 |
| 最大同時セッション数 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard サービス レベル (続き)

| **コンピューティング サイズ** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 付属ストレージ (GB) | 250 | 250 | 250 | 250 | 250 |
| 選択可能な最大ストレージ容量 (GB) | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 | 250、500、750、1024 |
| 最大インメモリ OLTP ストレージ容量 (GB) | 該当なし | 該当なし | 該当なし | 該当なし |該当なし |
| 最大同時実行ワーカー (要求) 数| 400 | 800 | 1600 | 3200 |6000 |
| 最大同時セッション数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium サービス レベル

| **コンピューティング サイズ** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1,000 | 1750 | 4000 |
| 付属ストレージ (GB) | 500 | 500 | 500 | 500 | 4,096* | 4,096* |
| 選択可能な最大ストレージ容量 (GB) | 500、750、1024 | 500、750、1024 | 500、750、1024 | 500、750、1024 | 4,096* | 4,096* |
| 最大インメモリ OLTP ストレージ容量 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大同時実行ワーカー (要求) 数| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大同時セッション数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 1,024 GB から最大 4,096 GB (256 GB 単位)

> [!IMPORTANT]
> 現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、米国中西部、米国 DoD の各リージョンと、米国政府中部を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。  詳しくは、[P11-P15 の現在の制限事項](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)に関するページをご覧ください。  
> [!NOTE]
> `tempdb` の制限については、[tempdb の制限](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- 単一データベースに対する仮想コア リソースの制限については、[仮想コア ベースの購入モデルを使用した単一データベースに対するリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページを参照してください
- エラスティック プールに対する仮想コア リソースの制限については、「[仮想コア ベースの購入モデルを使用したエラスティック プールに対するリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)」を参照してください
- エラスティック プールに対する DTU リソースの制限については、「[DTU ベースの購入モデルを使用したエラスティック プールのリソース制限](sql-database-dtu-resource-limits-elastic-pools.md)」を参照してください
- マネージド インスタンスに対するリソース制限については、[マネージド インスタンスのリソース制限](sql-database-managed-instance-resource-limits.md)に関するページを参照してください。
- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
- データベース サーバーでのリソース制限については、サーバーおよびサブスクリプション レベルの制限に関する情報が記載された、[SQL Database サーバー上のリソース制限の概要](sql-database-resource-limits-database-server.md)に関するページを参照してください。
