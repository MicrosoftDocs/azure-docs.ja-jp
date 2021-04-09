---
title: Azure Monitor ブック - JSONPath で JSON データを変換する
description: Azure Monitor ブックで JSONPath を使用して、クエリを実行したエンドポイントから返された JSON データの結果を必要な形式に変換する方法。
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 142853f0d5ed787d0b7aaee1bb118a001f144227
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722927"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>JSONPath を使用してブック内の JSON データを変換する方法

ブックでは、多くのソースからデータのクエリを実行できます。 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) やカスタム エンドポイントなどの一部のエンドポイントは、JSON で結果を返すことができます。 クエリを実行したエンドポイントによって返される JSON データが必要な形式で構成されていない場合は、JSONPath を使用して結果を変換できます。

JSONPath は、XML 用の XPath に似た JSON 用のクエリ言語です。 XPath と同様に、JSONPath を使用すると、JSON 構造からデータの抽出とフィルター処理を行うことができます。

JSONPath 変換を使用すると、ブックの作成者は JSON をテーブル構造に変換できます。 その後、テーブルを使用して、[ブックの視覚化](./workbooks-overview.md#visualizations)をプロットできます。

## <a name="using-jsonpath"></a>JSONPath の使用

1. *[編集]* ツール バー項目をクリックして、ブックを編集モードに切り替えます。
2. *[追加]*  >  *[クエリの追加]* リンクを使用して、クエリ コントロールをブックに追加します。
3. データ ソースを *JSON* として選択します。
4. JSON エディターを使用して、次の JSON スニペットを入力します。
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

ストアの在庫の表現として、上記の JSON オブジェクトを指定しているとします。 このタスクでは、書名、作成者、価格を一覧することで、ストアの利用可能な書籍のテーブルを作成します。

1. *[結果の設定]* タブを選択し、結果の形式を *[JSON パス]* に切り替えます。
2. 次の JSON パスの設定を適用します。

    JSON パス テーブル: `$.store.books`。 このフィールドは、テーブルのルート パスを表します。 この場合、ストアの書籍の在庫に関心があります。 テーブル パスによって、JSON を書籍情報にフィルター処理します。

   | 列 ID | 列の JSON パス |
   |:-----------|:-----------------|
   | タイトル      | `$.title`        |
   | Author     | `$.author`       |
   | Price      | `$.price`        |

    列 ID は列ヘッダーになります。 列の JSON パス フィールドは、テーブルのルートから列の値までのパスを表します。

1. *[クエリの実行]* をクリックして上記の設定を適用します。

![ JSON データ ソースと JSON パスの結果形式を使用したクエリ項目の編集](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>次のステップ
- [ブックの概要](./workbooks-overview.md)
- [Azure Monitor ブックのグループ](workbooks-groups.md)