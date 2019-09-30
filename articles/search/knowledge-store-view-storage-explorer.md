---
title: Storage Explorer でナレッジ ストアを表示する - Azure Search
description: Azure portal の Storage Explorer で Azure Search のナレッジ ストアを表示して分析します。
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 9ec93bcd2309bb47b24983260c35726ac24b5e95
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265612"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer でナレッジ ストアを表示する

> [!Note]
> ナレッジ ストアはプレビュー段階であるため、運用環境では使用しないでください。 この機能は、[Azure Search REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供されています。 現時点で .NET SDK のサポートはありません。
>
この記事では、Azure portal の Storage Explorer を使用してナレッジ ストアに接続し、探索する方法を説明します。 このチュートリアルで使用されているナレッジ ストア サンプルを作成する方法については、「[Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

+ 「[Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)」の手順に従って、このチュートリアルに使用されているサンプル ナレッジ ストアを作成します。

+ ナレッジ ストアの作成に使用した Azure Storage アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Storage Explorer でのナレッジ ストアの表示、編集、クエリ

1. Azure portal で、ナレッジ ストアの作成に使用したストレージ アカウントを開きます。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[Storage Explorer]** をクリックします。

1. **[テーブル]** リストを展開し、ホテル レビュー サンプル データに対して**データ インポート** ウィザードを実行するときに作成した Azure テーブル プロジェクションを一覧表示します。

いずれかのテーブルを選択すると、エンリッチされたデータ (キー フレーズのセンチメント スコア、緯度と経度の位置データなど) が表示されます。

   ![Storage Explorer でテーブルを表示する](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer でテーブルを表示する")

テーブルの値のデータ型を変更したり、テーブル内の値を個別に変更したりするには、 **[編集]** をクリックします。 1 つのテーブル行について列のデータ型を変更すると、すべての行に適用されます。

   ![Storage Explorer でテーブルを編集する](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer でテーブルを編集する")

クエリを実行するには、コマンド バーの **[クエリ]** をクリックして条件を入力します。  

   ![Storage Explorer でテーブルにクエリを実行する](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer でテーブルにクエリを実行する")

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次の手順

このナレッジ ストアを Power BI に接続する方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)

REST API と Postman を使用してナレッジ ストアを作成する方法については、次の記事を参照してください。  

> [!div class="nextstepaction"]
> [REST でナレッジ ストアを作成する](knowledge-store-howto.md)
