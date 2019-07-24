---
title: ロジック アプリにクエリ アクションを追加する | Microsoft Docs
description: 配列のフィルター処理などのアクションを実行するためのクエリ アクションの概要です。
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794881"
---
# <a name="get-started-with-the-query-action"></a>クエリ アクションの概要
クエリ アクションを使用し、バッチと配列を操作して次のワークフローを実現できます。

* データベースの優先度の高いすべてのレコードを対象とするタスクを作成する。
* 電子メールのすべての PDF 添付ファイルを Azure BLOB に保存する。

ロジック アプリでクエリ アクションの使用を開始するには、 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事をご覧ください。

## <a name="use-the-query-action"></a>クエリ アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 
アクションの詳細については[こちら](../connectors/apis-list.md)を参照してください。  

現在、クエリ アクションでは、"配列のフィルター処理" という操作だけがデザイナーで公開されています。 このクエリ アクションでは、配列を照会し、フィルター処理された一連の結果を返すことができます。

ロジック アプリにクエリ アクションを追加する方法を次に示します。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]** を選択します。
3. アクションの検索ボックスに「**フィルター**」と入力して、**[配列のフィルター処理]** アクションを表示します。
   
    ![クエリ アクションを選択する](./media/connectors-native-query/using-action-1.png)
4. フィルター処理する配列を選択します  (次のスクリーン ショットは、Twitter 検索結果の配列を示しています)。
5. 各項目について評価する条件を作成します  (次のスクリーン ショットは、フォロワー数が 100 を超えるユーザーのツイートをフィルター処理したものです)。
   
    ![クエリ アクションを完了する](./media/connectors-native-query/using-action-2.png)
   
    フィルター要件を満たす結果だけが含まれた新しい配列が出力されます。
6. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存されて発行 (アクティブ化) されます。

\* HTTP エンドポイントを呼び出し、JSON 応答を受信する場合は、"_JSON の解析_" アクションを使用して JSON 応答を解析します。 この手順を実行しない場合、"_配列のフィルター処理_" は本文のみを参照し、JSON ペイロードの構造を認識しません。

## <a name="query-action"></a>クエリ アクション
ここでは、このコネクタでサポートされているアクションの詳細について説明します。 このコネクタには、使用可能なアクションが 1 つあります。

| Action | 説明 |
| --- | --- |
| 配列のフィルター処理 |配列内の各項目について条件を評価し、結果を返します。 |

## <a name="action-details"></a>アクションの詳細
このクエリ アクションには、使用可能なアクションが 1 つ用意されています。 次の表に、アクションの必須および省略可能な入力フィールドと、各アクションの使用に伴う出力の詳細を示します。

### <a name="filter-array"></a>配列のフィルター処理
HTTP 送信要求を実行するアクションの入力フィールドを次に示します。
\* は、必須フィールドであることを示しています。

| Display name | プロパティ名 | 説明 |
| --- | --- | --- |
| From* |from |フィルター処理する配列 |
| Condition* |各値の説明: |各項目について評価する条件 |

<br>

### <a name="output-details"></a>出力の詳細
HTTP 応答の出力の詳細を次に示します。

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| Filtered array |array |フィルター処理された各結果を表すオブジェクトを含む配列 |

## <a name="next-steps"></a>次の手順
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。

