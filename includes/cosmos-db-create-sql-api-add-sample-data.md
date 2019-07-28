---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: snehagunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 127d67cc3b5dcd0ddd585470821eb1baa08c2388
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181721"
---
これで、データ エクスプローラーを使用して、新しいコレクションにデータを追加できます。

1. データ エクスプローラーで新しいデータベースが [コレクション] ウィンドウに表示されます。 **[タスク]** データベースを展開し、 **[項目]** コレクションを展開して、 **[ドキュメント]** をクリックし、 **[新しいドキュメント]** をクリックします。 

   ![Azure Portal のデータ エクスプローラーで新しいドキュメントを作成する](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. ここで、次の構造のドキュメントをコレクションに追加します。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. json を **[ドキュメント]** タブに追加したら、 **[保存]** をクリックします。

    ![json データをコピーし、Azure Portal のデータ エクスプローラーで [保存] をクリックします。](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  もう 1 つドキュメントを作成して保存します。`id` プロパティには一意の値を挿入し、その他のプロパティについては適宜変更してください。 Azure Cosmos DB では、データにスキーマを課さないため、新しいドキュメントは必要な任意の構造にすることができます。