---
title: Azure Data Factory の Azure PowerShell サンプル
description: Azure PowerShell サンプル - データ ファクトリの作成と管理で役立つスクリプト。
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 21ab661dd19d76526cd97b75660b9749e1342e09
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783236"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure Data Factory の Azure PowerShell サンプル

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

次の表には、Azure Data Factory の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

| スクリプト | 説明  |
|---|---|
|**データをコピーする**||
|[Azure Blob Storage で BLOB をフォルダーから別のフォルダーにコピーする](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure Blob Storage 内のフォルダーから同じ Blob Storage 内の別のフォルダーにデータをコピーします。 |
|[SQL Server から Azure Blob Storage へのデータのコピー](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、SQL Server データベースから Azure Blob Storage にデータをコピーします。 |
|[一括コピー](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトでは、Azure SQL Database のデータベースの複数のテーブルから Azure Synapse Analytics にデータをコピーします。 |
|[増分コピー](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| このサンプルの PowerShell スクリプトは、最初にソース データ ストアからシンク データ ストアにデータをすべてコピーした後で、新しいレコードまたは更新されたレコードだけをソースからシンクに読み込みます。 |
|**データの変換**||
|[Spark クラスターを使用してデータを変換する](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Spark クラスターでプログラムを実行することでデータを変換します。 |
|**SSIS パッケージを Azure にリフトしてシフトする**||
|[Azure-SSIS 統合ランタイムを作成する](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| この PowerShell スクリプトは、Azure で SQL Server Integration Services (SSIS) パッケージを実行する Azure-SSIS 統合ランタイムをプロビジョニングします。 |



