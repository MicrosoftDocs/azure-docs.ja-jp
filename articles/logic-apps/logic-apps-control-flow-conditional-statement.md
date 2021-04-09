---
title: 条件付きステートメントをワークフローに追加する
description: Azure Logic Apps のワークフロー内のアクションを制御する条件を作成する方法です
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657389"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Azure Logic Apps のワークフロー アクションを制御する条件付きステートメントを作成する

指定した条件が満たされた後でのみロジック アプリ内の特定のアクションを実行するには、"*条件付きステートメント*" を追加します。 この制御構造は、ワークフローのデータを特定の値またはフィールドと比較します。 その後、データが条件を満たしたかどうかによって実行するさまざまなアクションを指定できます。 条件は入れ子にすることができます。

たとえば、Web サイトの RSS フィードに新しい項目が出現したときに送信される電子メールが多すぎるロジック アプリがあるとします。 条件付きステートメントを追加して、新しい項目に特定の文字列が含まれる場合のみ電子メールを送信することができます。 

> [!TIP]
> いくつかの特定の値に基づいてさまざまなステップを実行する場合は、[*switch ステートメント*](../logic-apps/logic-apps-control-flow-switch-statement.md)を使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* この記事の例に従うには、Outlook.com アカウント、または職場または学校アカウントを使用して、[このサンプルのロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

## <a name="add-condition"></a>条件を追加する

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。

1. 必要な場所に条件を追加します。 

   ステップの間に条件を追加するには、条件を追加する位置の矢印の上にポインターを移動します。 表示される **プラス記号** ( **+** ) を選択し、 **[アクションの追加]** を選択します。 次に例を示します。

   ![ステップの間にアクションを追加する](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   ワークフローの末尾に条件を追加するときは、ロジック アプリの一番下で **[新しいステップ]** > **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「条件」と入力します。 このアクションを選択: **[条件] - (コントロール)**

   ![条件を追加する](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. **[条件]** ボックスで、条件を作成します。 

   1. 左のボックスに、比較するデータまたはフィールドを指定します。

      左側のボックス内をクリックすると、動的コンテンツの一覧が表示され、ロジック アプリの前のステップからの出力を選択できます。 
      この例では、RSS フィードの概要を選択します。

      ![条件を作成する](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. 中央のボックスで、実行する操作を選択します。 
   この例では、 **[次の値を含む]** を選択します。 

   1. 右のボックスに、条件として値またはフィールドを指定します。 
   この例では、次の文字列を指定します。**Microsoft**

   完成した条件を次に示します。

   ![完成した条件](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   条件に別の行を追加するには、 **[追加]**  >  **[行の追加]** を選択します。 
   サブ条件のグループを追加するには、 **[追加]**  >  **[グループの追加]** を選択します。 
   既存の行をグループ化するには、グループ化する行のチェック ボックスをオンにし、いずれかの行の省略記号 [...] ボタンを選択して、 **[Make group]\(グループにする\)** を選択します。

1. **[If true]\(true の場合\)** と **[If false]\(false の場合\)** の下に、条件を満たすかどうかに基づいて実行するステップを追加します。 次に例を示します。

   ![[If true]\(true の場合\) パスと [If false]\(false の場合\) パスを使用した条件](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > 既存のアクションを **[If true]\(true の場合\)** パスと **[If false]\(false の場合\)** パスにドラッグできます。

1. ロジック アプリを保存します。

これで、このロジック アプリは、RSS フィードの新しい項目が条件を満たす場合のみメールを送信します。

## <a name="json-definition"></a>JSON の定義

条件ステートメントの背後にある上位レベルのコードの定義を次に示します。

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
