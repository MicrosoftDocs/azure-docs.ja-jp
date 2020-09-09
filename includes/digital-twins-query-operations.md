---
author: baanders
description: Azure Digital Twins クエリ操作のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 6dfddd9179569075abdbd94b6c1afa719db9ba1e
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905595"
---
## <a name="query-language-features"></a>クエリ言語の機能

Azure Digital Twins では、ツイン グラフに対する広範なクエリ機能が提供されます。 クエリは、多くの同等の機能を備えている [IoT Hub クエリ言語](../articles/iot-hub/iot-hub-devguide-query-language.md)に似たクエリ言語で、SQL に似た構文を使用して記述します。

> [!NOTE]
> すべての Azure Digital Twins クエリ操作では、大文字と小文字が区別されます。

Azure Digital Twins クエリ言語で使用できる操作は次のとおりです。

次を指定してデジタル ツインを取得します。
* モデル (`IS_OF_MODEL` 演算子を使用)
* プロパティ ([タグのプロパティ](../articles/digital-twins/how-to-use-tags.md)を含む)
* インターフェイス
* relationships
  - リレーションシップのプロパティ

クエリは、次の操作を使用してさらに強化できます。
* 複数のリレーションシップの種類に対してツインを取得します (`JOIN` クエリ)。 
  - プレビュー期間中は、最大 5 レベルの `JOIN` が許可されます。
* 上位のクエリ結果のみを選択します (`Select TOP` 演算子)
* スカラー関数を使用します: `IS_BOOL`、`IS_DEFINED`、`IS_NULL`、`IS_NUMBER`、`IS_OBJECT`、`IS_PRIMITIVE`、`IS_STRING`、`STARTSWITH`、`ENDSWITH`。
* クエリ比較演算子を使用します: `IN`/`NIN`、`=`、`!=`、`<`、`>`、`<=`、`>=`。
* `IS_OF_MODEL`、スカラー関数、および比較演算子の任意の組み合わせ (`AND`、`OR`、`NOT` 演算子) を使用します。
* 継続を使用します: クエリ オブジェクトは、ページ サイズ (最大 100) を使用してインスタンス化されます。 API の後続の呼び出しに継続トークンを指定することで、デジタル ツインを一度に 1 ページずつ取得できます。