---
title: データを Azure SQL Database に移動する - Team Data Science Process
description: フラット ファイル (CSV 形式または TSV 形式) のデータまたは SQL Server に格納されているデータを、Azure SQL データベースに移動します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309532"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Azure Machine Learning 用にデータを Azure SQL Database に移動する

この記事では、フラット ファイル (CSV 形式または TSV 形式) のデータまたは SQL Server に格納されているデータを、Azure SQL データベースに移動するためのオプションについて説明します。 このクラウドへのデータ移動タスクは、Team Data Science Process の一部です。

Machine Learning 用に SQL Server にデータを移動するためのオプションについては、「[Azure Virtual Machine 上の SQL Server にデータを移動する](move-sql-server-virtual-machine.md)」をご覧ください。

次の表は、Azure SQL Database にデータを移動するためのオプションをまとめたものです。

| <b>ソース</b> | <b>移動先:Azure SQL Database</b> |
| --- | --- |
| <b>フラット ファイル (CSV または TSV 形式)</b> |[一括挿入 SQL クエリ](#bulk-insert-sql-query) |
| <b>オンプレミスの SQL Server</b> |1.[フラット ファイルへのエクスポート](#export-flat-file)<br> 2.[SQL Database 移行ウィザード](#insert-tables-bcp)<br> 3.[データベースのバックアップと復元](#db-migration)<br> 4.[Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>前提条件
ここに記載されている手順には次のものが必要です。

* **Azure サブスクリプション**。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* **Azure ストレージ アカウント**。 このチュートリアルでは、データの格納に Azure ストレージ アカウントを使用します。 Azure ストレージ アカウントがない場合は、「 [ストレージ アカウントの作成](../../storage/common/storage-account-create.md) 」を参照してください。 ストレージ アカウントを作成したら、ストレージへのアクセスに使用するアカウント キーを取得する必要があります。 「[ストレージ アカウント アクセス キーを管理する](../../storage/common/storage-account-keys-manage.md)」をご覧ください。
* **Azure SQL Database** へのアクセス権。 Azure SQL Database をセットアップする必要がある場合、Azure SQL Database の新しいインスタンスをプロビジョニングする方法については、 [Microsoft Azure SQL Database の概要](../../azure-sql/database/single-database-create-quickstart.md) に関する記事をご覧ください。
* **Azure PowerShell** がローカルにインストールされ構成されていること。 手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/)」を参照してください。

**Data**:移行プロセスは、[NYC タクシー データセット](https://chriswhong.com/open-data/foil_nyc_taxi/)を使用して説明されています。 NYC タクシー データセットには乗車データと料金についての情報が含まれています。このデータセットは Azure Blob Storage の[NYC タクシー データ](https://www.andresmh.com/nyctaxitrips/)で入手できます。 これらのファイルのサンプルと説明は、「 [NYC タクシー乗車データセットの説明](sql-walkthrough.md#dataset)」にあります。

ここで説明されている手順は、自身のデータに適用することも、NYC タクシー データセットを使用してこの手順に従って行うこともできます。 NYC タクシー データセットをご利用の SQL Server データベースにアップロードするには、「[SQL Server データベースにデータを一括インポートする](sql-walkthrough.md#dbload)」に記載されている手順に従います。

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> フラット ファイル ソースから Azure SQL Database へのデータの移動
フラット ファイル (CSV 形式または TSV 形式) のデータは、一括挿入 SQL クエリを使用して Azure SQL Database に移動できます。

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> 一括挿入 SQL クエリ
一括挿入 SQL クエリを使用する手順は、フラット ファイル ソースから Azure VM 上の SQL Server にデータを移動する指示と似ています。 詳細については、「 [一括挿入 SQL クエリ](move-sql-server-virtual-machine.md#insert-tables-bulkquery)」をご覧ください。

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> SQL Server から Azure SQL データベースへのデータの移動
ソース データが SQL Server に保存されている場合は、さまざまな方法で Azure SQL データベースにデータを移動できます。

1. [フラット ファイルへのエクスポート](#export-flat-file)
2. [SQL Database 移行ウィザード](#insert-tables-bcp)
3. [データベースのバックアップと復元](#db-migration)
4. [Azure Data Factory](#adf)

最初の 3 つの手順は、「[Azure Virtual Machine 上の SQL Server にデータを移動する](move-sql-server-virtual-machine.md)」の対応する手順と似ています。 以降の説明には、そのトピックの該当するセクションへのリンクが含まれています。

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>フラット ファイルへのエクスポート
フラット ファイルにエクスポートする手順は、「[フラット ファイルへのエクスポート](move-sql-server-virtual-machine.md#export-flat-file)」の指示と似ています。

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>SQL Database 移行ウィザード
SQL Database 移行ウィザードを使用する手順は、「[SQL Database 移行ウィザード](move-sql-server-virtual-machine.md#sql-migration)」の指示と似ています。

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>データベースのバックアップと復元
データベースのバックアップと復元を使用する手順は、「[データベースのバックアップと復元](move-sql-server-virtual-machine.md#sql-backup)」の一連の指示と似ています。

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Azure Data Factory (ADF) を使用して Azure SQL データベースにデータを移動する方法については、[Azure Data Factory を使用して SQL Server から SQL Azure にデータを移動する](move-sql-azure-adf.md)方法に関するトピックを参照してください。 このトピックでは、ADF を使用して SQL Server データベースから Azure Blob Storage 経由で Azure SQL データベースにデータを移動する方法が示されています。

ハイブリッドのオンプレミスとクラウドのソースを使用してデータを継続的に移行する必要がある場合は、ADF の使用を検討してください。  ADF は、データの変換が必要な場合や、移行中に新しいビジネス ロジックが必要な場合にも役立ちます。 ADF では、定期的にデータの移動を管理するシンプルな JSON スクリプトを使用して、ジョブのスケジュールと監視ができます。 ADF には他にも、複雑な操作のサポートなどの機能があります。