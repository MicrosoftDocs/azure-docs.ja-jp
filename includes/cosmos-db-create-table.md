---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68854633"
---
Azure Portal でデータ エクスプローラー ツールを使ってデータベースとテーブルを作成できるようになりました。 

1. **[データ エクスプローラー]**  >  **[新しいテーブル]** の順に選択します。 
    
    **[テーブルの追加]** 領域が右端に表示されます。表示するには、右にスクロールする必要がある場合があります。

    ![Azure Portal でのデータ エクスプローラー](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. **[テーブルの追加]** ページで、新しいテーブルの設定を入力します。

    設定|推奨値|説明
    ---|---|---
    テーブル ID|sample-table|新しいテーブルの ID。 テーブル名の文字要件はデータベース ID と同じです。 データベース名は、1 - 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。
    スループット|400 RU|スループットを 400 要求ユニット (RU/秒) に変更します。 待ち時間を短縮する場合、後でスループットをスケールアップできます。

3. **[OK]** を選択します。

4. 新しいデータベースとテーブルがデータ エクスプローラーに表示されます。

   ![新しいデータベースとコレクションを示す Azure Portal のデータ エクスプローラー](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)