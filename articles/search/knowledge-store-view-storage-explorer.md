---
title: Storage Explorer でナレッジ ストアを表示する
titleSuffix: Azure Cognitive Search
description: Azure portal の Storage Explorer で Azure Cognitive Search のナレッジ ストアを表示して分析します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566023"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer でナレッジ ストアを表示する

この記事では、Azure portal の Storage Explorer を使用してナレッジ ストアに接続し、探索する方法を例を用いて説明します。

## <a name="prerequisites"></a>前提条件

+ 「[Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)」の手順に従って、このチュートリアルに使用されているサンプル ナレッジ ストアを作成します。

+ ナレッジ ストアの作成に使用した Azure Storage アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Storage Explorer でのナレッジ ストアの表示、編集、クエリ

1. Azure portal で、ナレッジ ストアの作成に使用した [Storage アカウントを開きます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[Storage Explorer]** をクリックします。

1. **[テーブル]** リストを展開し、ホテル レビュー サンプル データに対して **データ インポート** ウィザードを実行するときに作成した Azure テーブル プロジェクションを一覧表示します。

いずれかのテーブルを選択すると、エンリッチされたデータ (キー フレーズ、センチメント スコアなど) が表示されます。

   ![Storage Explorer でテーブルを表示する](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer でテーブルを表示する")

テーブルの値のデータ型を変更したり、テーブル内の値を個別に変更したりするには、 **[編集]** をクリックします。 1 つのテーブル行について列のデータ型を変更すると、すべての行に適用されます。

   ![Storage Explorer でテーブルを編集する](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer でテーブルを編集する")

クエリを実行するには、コマンド バーの **[クエリ]** をクリックして条件を入力します。  

   ![Storage Explorer のクエリ テーブル](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer のクエリ テーブル")

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次のステップ

このナレッジ ストアを Power BI に接続して詳細な分析を行うか、または REST API と Postman を使用してコーディングを進め、別のナレッジストアを作成します。

> [!div class="nextstepaction"]
> [Power BI を使用して接続する](knowledge-store-connect-power-bi.md)
> [REST でナレッジ ストアを作成する](knowledge-store-create-rest.md)
