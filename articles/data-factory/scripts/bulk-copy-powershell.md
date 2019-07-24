---
title: 'PowerShell スクリプト: Azure Data Factory を使ってデータを一括コピーする | Microsoft Docs'
description: この PowerShell スクリプトでは、Azure Data Factory を使い、ソース データ ストアからコピー先データ ストアにデータを一括コピーする方法について説明します。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: d2db5bced78a00c8acabc150752fe65e9515dff1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849365"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell スクリプト - Azure Data Factory を使って複数のテーブルを一括コピーする

このサンプルの PowerShell スクリプトは、Azure SQL Database の複数のテーブルから Azure SQL Data Warehouse にデータをコピーします。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

このサンプルを実行するための前提条件については、[チュートリアルの一括コピー](../tutorial-bulk-copy.md#prerequisites)に関する説明を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

> [!IMPORTANT]
> このスクリプトは、Data Factory エンティティ (リンクされたサービス、データセット、およびパイプライン) を定義する JSON ファイルをハード ドライブ上の c:\ フォルダー内に作成します。

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

このサンプル スクリプトを実行した後、次のコマンドを使用して、リソース グループとそれに関連付けられたすべてのリソースを削除できます。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
リソース グループからデータ ファクトリを削除するには、次のコマンドを実行します。 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | データ ファクトリを作成します。 |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | データ ファクトリ内にリンクされたサービスを作成します。 リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | データ ファクトリ内にデータセットを作成します。 データセットは、パイプライン内のアクティビティの入出力を表します。 | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | データ ファクトリ内にパイプラインを作成します。 パイプラインには、特定の操作を実行する 1 つ以上のアクティビティが含まれています。 このパイプラインでは、コピー アクティビティが Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | パイプラインの実行を作成します。 つまり、パイプラインを実行します。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | パイプライン内のアクティビティの実行 (アクティビティ実行) に関する詳細情報を取得します。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Factory のその他の PowerShell サンプル スクリプトについては、[Azure Data Factory の PowerShell スクリプト](../samples-powershell.md)に関する記事を参照してください。
