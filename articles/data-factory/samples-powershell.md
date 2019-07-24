---
title: Azure Data Factory の Azure PowerShell サンプル | Microsoft Docs
description: Azure PowerShell サンプル - データ ファクトリの作成と管理で役立つスクリプト。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: c822ebdea2bab6f4c6c05cf2d4a4b822d6e02f3f
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575683"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure Data Factory の Azure PowerShell サンプル

次の表には、Azure Data Factory の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

| |  |
|---|---|
|**データをコピーする**||
|[Azure Blob Storage で BLOB をフォルダーから別のフォルダーにコピーする](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure Blob Storage 内のフォルダーから同じ Blob Storage 内の別のフォルダーにデータをコピーします。 |
|[Copy data from on-premises SQL Server to Azure Blob Storage (オンプレミスの SQL Server から Azure Blob Storage にデータをコピーする)](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーします。 |
|[一括コピー](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトは、Azure SQL Database の複数のテーブルから Azure SQL Data Warehouse にデータをコピーします。 |
|[増分コピー](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトは、最初にソース データ ストアからシンク データ ストアにデータをすべてコピーした後で、新しいレコードまたは更新されたレコードだけをソースからシンクに読み込みます。 |
|**データを変換する**||
|[Spark クラスターを使用してデータを変換する](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Spark クラスターでプログラムを実行することでデータを変換します。 |
|**SSIS パッケージを Azure にリフトしてシフトする**||
|[Azure-SSIS 統合ランタイムを作成する](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure で SQL Server Integration Services (SSIS) パッケージを実行する Azure-SSIS 統合ランタイムをプロビジョニングします。 |



