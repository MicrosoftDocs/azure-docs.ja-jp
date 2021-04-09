---
title: Azure Storage インベントリを使用して BLOB の数とサイズを計算する
description: コンテナーごとの BLOB の数と合計サイズを計算する方法について説明します。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637067"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Azure Storage インベントリを使用してコンテナーあたりの BLOB の数と合計サイズを計算する

この記事では、Azure Blob Storage インベントリ機能と Azure Synapse を使用して、コンテナーあたりの BLOB の数と合計サイズを計算します。 これらの値は、コンテナーごとに BLOB の使用を最適化するときに役立ちます。

この方法には、BLOB メタデータは含まれていません。 Azure Blob Storage インベントリ機能では、[List Blobs](/rest/api/storageservices/list-blobs) REST API と既定のパラメーターが使用されます。 そのため、この例はスナップショットや '$' コンテナーなどに対応していません。

> [!IMPORTANT]
> BLOB インベントリは現在、**プレビュー** の段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="enable-inventory-reports"></a>インベントリ レポートを有効にする

この方法の最初の手順は、ストレージ アカウントで[インベントリ レポートを有効にする](blob-inventory.md#enable-inventory-reports)ことです。 インベントリ レポートを有効にしてから、最初のレポートを生成するには、最大で 24 時間待機することが必要な場合があります。

分析するインベントリ レポートがある場合は、レポート CSV ファイルが置かれているコンテナーに対する BLOB 読み取りアクセス権を自分自身に付与します。

1. インベントリ CSV レポート ファイルが格納されているコンテナーに移動します。
1. **[アクセス制御 (IAM)]** 、 **[ロールの割り当ての追加]** の順に選択します。

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="[ロールの割り当ての追加] を選択する":::

1. **[ロール]** ドロップダウン リストから **[ストレージ BLOB データ閲覧者]** を選択します。

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="ドロップダウンから [ストレージ BLOB データ閲覧者] ロールを追加する":::

1. レポートを実行するのに使用しているアカウントの電子メール アドレスを **[選択]** フィールドに入力します。

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースを作成する

次に SQL クエリを実行してインベントリ結果を報告するための [Azure Synapse ワークスペースを作成](/azure/synapse-analytics/get-started-create-workspace)します。

## <a name="create-the-sql-query"></a>SQL クエリを作成する

Azure Synapse ワークスペースを作成したら、次の手順を行います。

1. [https://web.azuresynapse.net](https://web.azuresynapse.net) に移動します。
1. 左端にある **[開発]** タブを選択します。
1. 大きなプラス記号 (+) を選択して項目を追加します。
1. **SQL スクリプト** をインポートします。

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="SQL スクリプトを選択して新しいクエリを作成する ":::

## <a name="run-the-sql-query"></a>SQL クエリを実行する

1. 次の SQL クエリを Azure Synapse ワークスペースに追加して[インベントリ CSV ファイルを読み取ります](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file)。

    `bulk` パラメーターには、分析するインベントリ レポート CSV ファイルの URL を使用します。

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. 右側の [プロパティ] ウィンドウで、SQL クエリに名前を付けます。

1. Ctrl キーを押しながら S キーを押すか、 **[すべて発行]** ボタンを選択して、SQL クエリを発行します。

1. **[実行]** ボタンを選択して SQL クエリを実行します。 コンテナーごとの BLOB の数と合計サイズが **[結果]** ウィンドウに報告されます。

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="BLOB の数と合計サイズを計算するスクリプトを実行して得られた出力。":::

## <a name="next-steps"></a>次のステップ

- [Azure Storage BLOB インベントリを使用して BLOB データを管理する](blob-inventory.md)
- [Blob コンテナーの合計課金サイズを計算する](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
