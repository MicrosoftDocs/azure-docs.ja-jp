---
title: ファイルからデータベースへの一括コピー
description: ソリューション テンプレートを使用して、Azure Data Lake Storage Gen2 から Azure Synapse Analytics または Azure SQL Database にデータを一括コピーする方法について説明します。
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 468bd838237e076aacb9dee0ccacfdcc1ea940af
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629118"
---
# <a name="bulk-copy-from-files-to-database"></a>ファイルからデータベースへの一括コピー

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Lake Storage Gen2 から Azure Synapse Analytics または Azure SQL Database にデータを一括コピーするために使用できるソリューション テンプレートについて説明します。

## <a name="about-this-solution-template"></a>このソリューション テンプレートについて

このテンプレートは Azure Data Lake Storage Gen2 ソースからファイルを取得します。 次に、ソース内の各ファイルを反復処理し、そのファイルをコピー先データ ストアにコピーします。 

現在、このテンプレートでは、**DelimitedText** 形式でのデータのコピーのみがサポートされています。 他のデータ形式のファイルもソース データ ストアから取得することはできますが、コピー先データ ストアにコピーすることはできません。  

このテンプレートには、3 つのアクティビティが含まれています。
- **Get Metadata** アクティビティでは、Azure Data Lake Storage Gen2 からファイルを取得し、後続の *ForEach* アクティビティに渡します。
- **ForEach** アクティビティでは、*Get Metadata* アクティビティからファイルを取得し、*Copy* アクティビティに対して各ファイルを反復処理します。
- **Copy** アクティビティは *ForEach* アクティビティ内に存在し、各ファイルをソース データ ストアからコピー先データ ストアにコピーします。

このテンプレートでは、次の 2 つのパラメーターを定義します。
- *SourceContainer* は、Azure Data Lake Storage Gen2 内のデータのコピー元となるルート コンテナー パスです。 
- *SourceDirectory* は、Azure Data Lake Storage Gen2 内のデータのコピー元となるルート コンテナーの下のディレクトリ パスです。

## <a name="how-to-use-this-solution-template"></a>このソリューション テンプレートの使用方法

1. **[Bulk Copy from Files to Database]\(ファイルからデータベースへの一括コピー\)** テンプレートに移動します。 ソース Gen2 ストアへの **新しい**接続を作成します。 "GetMetadataDataset" と "SourceDataset" は、ソース ファイル ストアの同じ接続への参照であることに注意してください。

    ![ソース データ ストアへの新しい接続の作成](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. データのコピー先であるシンク データ ストアへの**新しい**接続を作成します。

    ![シンク データ ストアへの新しい接続の作成](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. **[このテンプレートを使用]** を選択します。

    ![このテンプレートを使用](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. 次の例のようなパイプラインが作成されます。

    ![パイプラインのレビュー](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > 前述の **手順 2** のデータのコピー先として **Azure Synapse Analytics (旧称 SQL DW)** を選択した場合、SQL Data Warehouse Polybase で必要とされる、ステージングのための Azure BLOB ストレージへの接続を入力する必要があります。 次のスクリーンショットに示すように、このテンプレートは BLOB ストレージの "*ストレージ パス*" を自動的に生成します。 パイプラインの実行後、コンテナーが作成されているかどうかを確認してください。
        
    ![Polybase 設定](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. **[デバッグ]** を選択し、 **[パラメーター]** で入力し、 **[完了]** を選択します。

    ![**[デバッグ]** をクリックします。](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. パイプラインの実行が正常に完了すると、次の例のような結果が表示されます。

    ![結果を確認する](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の概要](introduction.md)