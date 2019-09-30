---
title: Bing Search への接続 - Azure Logic Apps
description: Bing Search REST API と Azure Logic Apps を使用してニュースを検索します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 61004ed75a1935ada21b5c620a909fb5289aebb8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051004"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Bing Search と Azure Logic Apps を使用してニュースを検索する

この記事では、Bing Search コネクタを使用してロジック アプリの中から Bing Search によってニュース、ビデオ、その他の項目を検索する方法を示します。 このようにして、検索結果を処理するためのタスクとワークフローを自動化するロジック アプリを作成して、他のアクションがその項目を利用できるように設定できます。 

たとえば、検索条件に基づいてニュース項目を検索し、その項目が Twitter のフィードにツイートとして投稿されるように指定できます。

Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。
コネクタ固有の技術情報については、[Bing Search コネクタ リファレンス](https://docs.microsoft.com/connectors/bingsearch/)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* [Cognitive Services アカウント](../cognitive-services/cognitive-services-apis-create-account.md)

* [Bing Search API キー](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)。ロジック アプリから Bing Search APIs へのアクセスを提供します

* イベント ハブにアクセスするためのロジック アプリ。 Bing Search トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing Search トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

1. Azure Portal または Visual Studio で、Logic Apps デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

2. 検索ボックスに、フィルターとして「Bing search」と入力します。 トリガーの一覧から、目的のトリガーを選択します。

   この例では、次のトリガーを使用します。**Bing Search - 新しいニュース記事**

   ![Bing Search トリガーを検索する](./media/connectors-create-api-bing-search/add-trigger.png)

3. 接続の詳細の入力を求められたら、[Bing Search 接続を今すぐ作成](#create-connection)します。
接続が既に存在する場合は、トリガーに必要な情報を指定します。

   この例では、Bing Search から一致するニュース記事を返すための条件を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | Search Query | はい | <*search-words*> | 使用する検索キーワードを入力します。 |
   | Market | はい | <*locale*> | 検索ロケール。 既定値は "en-US" ですが、他の値を選択できます。 |
   | Safe Search | はい | <*search-level*> | 成人向けコンテンツを除外するためのフィルター レベル。 既定値は "中" ですが、他のレベルを選択します。 |
   | Count | いいえ | <*results-count*> | 返される結果数を指定します。 既定値は 20 ですが、他の値を指定できます。 実際に返される結果の数は、指定した数より少ないことがあります。 |
   | Offset | いいえ | <*skip-value*> | 結果が返される前にスキップする結果の数 |
   |||||

   例:

   ![トリガーの設定](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. トリガーが結果をチェックする間隔と頻度を選択します。

5. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

6. トリガーの結果を使用して実行するタスクの 1 つまたは複数のアクションをロジック アプリに追加する操作に進みます。

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing Search アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例では、ロジック アプリは、指定した条件に一致するニュース記事を返す Bing Search トリガーを使用して起動されます。

1. Azure Portal または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。 この例では、Azure Portal を使用します。

2. トリガーまたはアクションで、 **[新しいステップ]**  >  **[アクションの追加]** を選択します。

   この例では、次のトリガーを使用します。

   **Bing Search - 新しいニュース記事**

   ![アクションを追加する](./media/connectors-create-api-bing-search/add-action.png)

   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 
   表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

3. 検索ボックスに、フィルターとして「Bing search」と入力します。
アクションの一覧から、目的のアクションを選択します。

   この例では、次のアクションを使用します。

   **Bing Search - クエリごとにニュースを表示する**

   ![Bing Search アクションを検索する](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. 接続の詳細の入力を求められたら、[Bing Search 接続を今すぐ作成](#create-connection)します。 接続が既に存在する場合は、アクションに必要な情報を指定します。

   この例では、トリガーの結果のサブセットを返すための条件を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | Search Query | はい | <*search-expression*> | トリガー結果にクエリを実行する式を入力します。 動的コンテンツ リストのフィールドから選択することも、式ビルダーを使用して式を作成することもできます。 |
   | Market | はい | <*locale*> | 検索ロケール。 既定値は "en-US" ですが、他の値を選択できます。 |
   | Safe Search | はい | <*search-level*> | 成人向けコンテンツを除外するためのフィルター レベル。 既定値は "中" ですが、他のレベルを選択します。 |
   | Count | いいえ | <*results-count*> | 返される結果数を指定します。 既定値は 20 ですが、他の値を指定できます。 実際に返される結果の数は、指定した数より少ないことがあります。 |
   | Offset | いいえ | <*skip-value*> | 結果が返される前にスキップする結果の数 |
   |||||

   たとえば、カテゴリ名に "tech" が含まれる結果が返されるようにする必要があるとします。

   1. **[検索クエリ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 
   そのリストから **[式]** を選択して、式ビルダーを表示します。 

      ![Bing Search トリガー](./media/connectors-create-api-bing-search/bing-search-action.png)

      これで式の作成を始められます。

   2. 関数一覧から **contains()** 関数を選択すると、その関数が式ボックスに表示されます。 **[動的なコンテンツ]** をクリックして、フィールドの一覧を再表示します。ここでは、カーソルがかっこの中にあることを確認してください。

      ![関数を選択する](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. フィールドの一覧から **[カテゴリ]** を選択すると、そのカテゴリはパラメーターに変換されます。 
   最初のパラメーターの後にコンマを追加し、コンマの後に `'tech'` という単語を追加します。 

      ![フィールドを選択する](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. 終了したら、 **[OK]** を選択します。

      **[検索クエリ]** ボックスに、次の形式で式が表示されます。

      ![完成した式](./media/connectors-create-api-bing-search/resolved-expression.png)

      コード ビューでは、この式は次の形式で表示されます。

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Bing Search に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 接続情報の入力を求められたら、次の詳細を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | 接続名 | はい | <*connection-name*> | 作成する接続の名前 |
   | API Version | はい | <*API-version*> | 既定では、Bing Search API バージョンは現在のバージョンに設定されています。 必要に応じて、以前のバージョンを選択できます。 |
   | API キー | はい | <*API-key*> | 前に取得した Bing Search API キー。 キーがない場合は、ご自身の [API キーを今すぐ](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)取得します。 |  
   |||||  

   例:

   ![接続を作成する](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. 完了したら **[作成]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/bingsearch/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
