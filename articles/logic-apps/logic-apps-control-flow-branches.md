---
title: ワークフローのアクションに対して並列分岐を作成または結合する
description: Azure Logic Apps の独立したワークフロー アクションに対して、並列実行の分岐を作成またはマージする方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91269235"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Azure Logic Apps のワークフロー アクションに対して並列分岐を作成または結合する

既定では、ロジック アプリ ワークフロー内のアクションは順番に実行されます。 独立したアクションを同時に実行するには、[並列分岐](#parallel-branches)を作成してから、フローで後から[それらの分岐を結合](#join-branches)します。 

> [!TIP] 
> 配列を受け取るトリガーがあり、各配列項目のワークフローを実行する場合は、[**SplitOn** トリガー プロパティ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)を使用してその配列を "*バッチ解除*" できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>並列分岐を追加する

独立したステップを同時に実行するには、既存のステップの次に並列分岐を追加できます。 

![並列でステップを実行する](media/logic-apps-control-flow-branches/parallel.png)

ロジック アプリはすべての分岐が終了するまで待機してから、ワークフローを続行します。 並列分岐が実行するのは、`runAfter` プロパティの値が、完了した親ステップの状態と一致する場合のみです。 たとえば、`branchAction1` と `branchAction2` の両方は、`parentAction` が `Succeeded` 状態で完了した場合のみ実行するように設定されています。

> [!NOTE]
> 開始する前に、並列分岐を追加できるステップがロジック アプリに存在していることが必要です。

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。

1. 並列分岐を追加するステップにポインターを移動します。 表示される **プラス** 記号 ( **+** ) を選択し、 **[並列分岐の追加]** を選択します。 

   ![並列分岐を追加する](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. 検索ボックスで目的のアクションを探して選択します。

   ![Logic App Designer の [アクションを選択する] ウィンドウのスクリーンショット。](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   これで、次の例のように、選択したアクションが並列分岐に表示されます。

   ![目的のアクションを探して選択する](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. 各並列分岐に必要なステップを追加します。 分岐に別のアクションを追加するには、順次アクションを追加するアクションの下にポインターを移動します。 表示される **プラス**( **+** ) 記号を選択し、 **[アクションの追加]** を選択します。

   ![並列分岐に順次アクションを追加する](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. 検索ボックスで目的のアクションを探して選択します。

   ![Logic App Designer の [アクションを選択する] ウィンドウと検索ボックスのスクリーンショット。](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   これで、次の例のように、選択したアクションが現在の分岐に表示されます。

   ![順次アクションを探して選択する](media/logic-apps-control-flow-branches/added-sequential-action.png)

分岐を 1 つに戻すには、[並列分岐を結合](#join-branches)します。 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>並列分岐定義 (JSON)

コード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で並列構造を定義できます。

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>並列分岐を結合する

並列分岐の結合は、すべての分岐の下に 1 つのステップを追加するだけです。 このステップは、すべての並列分岐の実行が完了してから実行します。

![並列分岐を結合する](media/logic-apps-control-flow-branches/join.png)

1. [Azure ポータル](https://portal.azure.com)のロジック アプリ デザイナーでロジック アプリを選択して開きます。 

1. 結合する並列分岐で **[新しいステップ]** を選択します。 

   ![結合するステップを追加する](media/logic-apps-control-flow-branches/add-join-step.png)

1. 検索ボックスで、分岐に結合するステップとして必要なアクションを探して選択します。

   ![並列分岐に結合するアクションを探して選択する](media/logic-apps-control-flow-branches/join-steps.png)

   これで並列分岐は結合されました。

   ![結合された分岐](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>結合の定義 (JSON)

コード ビューで操作している場合、代わりに次の例のようにロジック アプリの JSON 定義で結合構造を定義できます。

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [グループ化されたアクションの状態に基づいてステップを実行する (スコープ)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
