---
title: インライン コードを使用してコード スニペットを追加および実行する
description: Azure Logic Apps で作成する自動化されたタスクおよびワークフロー用のインライン コード アクションを使用して、コード スニペットを作成および実行する方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179607"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Azure Logic Apps 内でインライン コードを使用してコード スニペットを追加および実行する

ロジック アプリ内でコードの一部を実行するときは、組み込みのインライン コード アクションをロジック アプリのワークフロー内にステップとして追加します。 このアクションが最もうまく機能するのは、実行するコードが次のシナリオに適合しているときです。

* JavaScript 内で実行する。 追加の言語がまもなく提供される。

* 実行の完了に要する時間が 5 秒以内。

* 処理するデータのサイズが最大 50 MB。

* [**変数** アクション](../logic-apps/logic-apps-create-variables-store-values.md)の操作は必要なし (未サポート)。

* 使用する Node.js のバージョンが 8.11.1 である。 詳細については、「[標準ビルトイン オブジェクト](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)」を参照してください。

  > [!NOTE]
  > `require()` 関数は、JavaScript を実行するインライン コード アクションではサポートされていません。

このアクションによりコード スニペットが実行され、そのスニペットの出力が `Result` という名前のトークンとして返されます。 ロジック アプリのワークフローの後続のアクションで、このトークンを使用できます。 コードの関数を作成する別のシナリオでは、ロジック アプリ内で[代わりに Azure Functions を使用して関数を作成して呼び出して](../logic-apps/logic-apps-azure-functions.md)みてください。

この記事では、職場または学校アカウントに新しい電子メールが届いたときに、サンプルのロジック アプリがトリガーされます。 このコード スニペットでは、電子メール本文に表示されているすべての電子メール アドレスが抽出されて、返されます。

![ロジック アプリの例を示すスクリーンショット](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* コード スニペットを追加するロジック アプリ (トリガーを含む)。 ロジック アプリがない場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

   このトピックの例では、Office 365 Outlook の **[新しいメールが届いたとき]** というトリガーを使用します。

* ロジック アプリにリンクされた[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

  * ユース ケースまたはシナリオに適した統合アカウントを使用していることを確認します。

    たとえば、[Free レベル](../logic-apps/logic-apps-pricing.md#integration-accounts)の統合アカウントは、運用環境のシナリオではなく探索的なシナリオとワークロードのみを対象としており、用途とスループットが制限されており、サービス レベル アグリーメント (SLA) ではサポートされていません。 他のレベルではコストが発生しますが、SLA のサポートが含まれ、スループットが向上し、上限も高くなります。 統合アカウントの[レベル](../logic-apps/logic-apps-pricing.md#integration-accounts)、[価格](https://azure.microsoft.com/pricing/details/logic-apps/)、および[制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)の詳細を確認してください。

   * 統合アカウントを使用しない場合は、[Azure Logic Apps プレビュー](logic-apps-overview-preview.md)を使用し、リソースの種類 **[Logic App (プレビュー)]** からロジック アプリを作成することができます。

     Azure Logic Apps プレビューでは、**インライン コード** が **インライン コード操作** と呼ばれるようになったほか、次のような相違点もあります。

     * **JavaScript コードの実行** が、**Run in-line JavaScript\(インライン JavaScript の実行\)** になりました。

     * macOS または Linux を使用している場合、Visual Studio Code で Azure Logic Apps (プレビュー) 拡張機能を使用する際に、インライン コード操作アクションは現在使用できません。

     * インライン コード操作アクションには[更新された制限](logic-apps-overview-preview.md#inline-code-limits)があります。

     次のいずれかのオプションから開始できます。

     * [Azure portal を使用](create-stateful-stateless-workflows-azure-portal.md)して、リソースの種類 **[Logic App (プレビュー)]** からロジック アプリを作成します。

     * [Visual Studio Code と Azure Logic Apps (プレビュー) 拡張機能を使用](create-stateful-stateless-workflows-visual-studio-code.md)して、ロジック アプリのプロジェクトを作成します。

## <a name="add-inline-code"></a>インライン コードを追加する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. デザイナーを使用して、ロジック アプリのワークフローのどこにインライン コード アクションを追加するかを選択します。

   * ワークフローの最後にアクションを追加するには、 **[新しいステップ]** を選択します。

   * ステップ間にアクションを追加するには、それらのステップを接続している矢印の上にマウス ポインターを移動します。 表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

   この例では、Office 365 Outlook トリガーの下にインライン コード アクションが追加されます。

   ![トリガーの下に新しいステップを追加します。](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. **[アクションを選択してください]** で、検索ボックスに「`inline code`」と入力します。 アクションのリストで、 **[JavaScript コードの実行]** という名前のアクションを選択します。

   ![[JavaScript コードの実行] アクションを選択します。](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   このアクションがデザイナーに表示されます。アクションには、既定で `return` ステートメントを含むいくつかのサンプル コードが入っています。

   ![既定のサンプル コードを含むインライン コード アクション](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. **[コード]** ボックス内でサンプル コードを削除し、自分のコードを入力します。 メソッド内に配置するコードを作成します。ただし、メソッド シグネチャは含めません。

   認識済みのキーワードの入力を開始すると、オートコンプリートのリストが表示され、用意されているキーワードから選択できるようになります。例を次に示します。

   ![キーワードのオートコンプリート リスト](./media/logic-apps-add-run-inline-code/auto-complete.png)

   このサンプルのコード スニペットで最初に作成される変数には、入力テキストの一致パターンを指定する "*正規表現*" が格納されます。 次に作成される変数には、トリガーからの電子メール本文データが格納されます。

   ![変数の作成](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   トリガーや以前のアクションからの結果を簡単に参照できるようにするため、カーソルを **[コード]** ボックス内に置くと、動的コンテンツのリストが表示されます。 この例では、トリガーからの使用可能な結果がリストに表示されています (これには **[本文]** トークンが含まれており、選択できるようになっています)。

   **[本文]** トークンを選択すると、インライン コード アクションにより、トークンが、電子メールの `Body` プロパティ値を参照する `workflowContext` オブジェクトに解決されます。

   ![結果の選択](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   **[コード]** ボックス内で、スニペットは、読み取り専用の `workflowContext` オブジェクトを入力として使用できます。 このオブジェクト内のプロパティにより、コードからワークフロー内のトリガーや以前のアクションの結果にアクセスできます。 詳細については、後述の「[コードでトリガーとアクションの結果を参照する](#workflowcontext)」を参照してください。

   > [!NOTE]
   > コード スニペットで参照されるアクション名にドット (.) 演算子が使用されている場合は、それらのアクション名を [ **[アクション]** パラメーター](#add-parameters)に追加する必要があります。 これらの参照では、アクション名を大かっこ ([]) と引用符で囲む必要もあります。例を次に示します。
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   インライン コード アクションでは、`return` ステートメントは不要ですが、`return` ステートメントの結果は、後のアクションで **[結果]** トークンを通じて参照できます。 たとえば、コード スニペットは、正規表現で電子メール本文内の一致を検索する `match()` 関数を呼び出して結果を返します。 **[作成]** アクションは、 **[結果]** トークンを使用して、インライン コード アクションの結果を参照し、1 つの結果を作成します。

   ![完成したロジック アプリ](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完了したら、ロジック アプリを保存します。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>コードでトリガーとアクションの結果を参照する

`workflowContext` オブジェクトの構造を次に示します。この構造には、`actions`、`trigger`、および `workflow` の各サブプロパティが含まれています。

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

これらのサブプロパティの詳細を次の表に示します。

| プロパティ | Type | 説明 |
|----------|------|-------|
| `actions` | オブジェクト コレクション | コード スニペットを実行する前に実行したアクションの結果オブジェクトです。 各オブジェクトには、*key-value* ペアが含まれています。ここで、key はアクション名、value は `@actions('<action-name>')` を含む [actions() 関数](../logic-apps/workflow-definition-language-functions-reference.md#actions)の呼び出しに相当します。 アクションの名前で使用されるアクション名は、基盤となるワークフロー定義で使用されるアクション名と同じです。アクション名内のスペース (" ") がアンダー スコア (_) で置き換えられています。 このオブジェクトにより、現在実行されているワークフロー インスタンスからアクション プロパティの値にアクセスできます。 |
| `trigger` | Object | トリガーの結果オブジェクトで、 [trigger() 関数](../logic-apps/workflow-definition-language-functions-reference.md#trigger)の呼び出しと同じです。 このオブジェクトにより、現在実行されているワークフロー インスタンスからトリガー プロパティの値にアクセスできます。 |
| `workflow` | Object | ワークフロー オブジェクトで、[workflow() 関数](../logic-apps/workflow-definition-language-functions-reference.md#workflow)の呼び出しと同じです。 このオブジェクトにより、現在実行されているワークフロー インスタンスからワークフロー プロパティの値 (ワークフロー名や実行 ID など) にアクセスできます。 |
|||

このトピックの例の場合、`workflowContext` オブジェクトには、これらのプロパティが含まれており、コードからアクセスすることが可能です。

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>パラメーターの追加

場合によっては、コードが依存関係として参照するトリガーや特定のアクションの結果をインライン コード アクションに含めることを明示的に要求するために、 **[トリガー]** パラメーターや **[アクション]** パラメーターを追加することが必要となります。 このオプションは、参照する結果が実行時に見つからないシナリオで役立ちます。

> [!TIP]
> コードを再利用する予定の場合は、 **[コード]** ボックスを使用してプロパティへの参照を追加し、解決されたトークン参照がコードに含まれるようにします。トリガーやアクションを明示的な依存関係として追加しないでください。

たとえば、Office 365 Outlook コネクタの **[承認の電子メールを送信します]** アクションからの **SelectedOption** の結果を参照するコードがあるものとします。 作成時、Logic Apps エンジンは、トリガーやアクションの結果が参照されたかをコードを分析して調べ、それらの結果を自動的に含めます。 実行時、参照されるトリガーやアクションの結果が、指定された `workflowContext` オブジェクトで使用できないというエラーが発生した場合は、そのトリガーやアクションを明示的な依存関係として追加します。 この例では、 **[アクション]** パラメーターを追加して、 [承認の電子メールを送信します] アクションの結果を **インライン コード** アクションに明示的に含めることを指定します。

これらのパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

   ![パラメーターの追加](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | パラメーター | 説明 |
   |-----------|-------------|
   | **アクション** | 以前のアクションの結果を含めます。 「[アクションの結果を含める](#action-results)」を参照してください。 |
   | **トリガー** | トリガーの結果を含めます。 「[トリガーの結果を含める](#trigger-results)」を参照してください。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>トリガーの結果を含める

**[トリガー]** を選択した場合は、トリガーの結果を含めるかどうかを尋ねられます。

* **[トリガー]** リストで **[はい]** を選択します。

<a name="action-results"></a>

### <a name="include-action-results"></a>アクションの結果を含める

**[アクション]** を選択した場合は、追加するアクションを尋ねられます。 ただし、アクションの追加を開始する前に、ロジック アプリの基礎となるワークフロー定義に表示されるバージョンのアクション名が必要です。

* この機能では、変数、ループ、およびイテレーションのインデックスはサポートされません。

* ロジック アプリのワークフロー定義の名前では、スペースではなく、アンダー スコア (_) が使用されます。

* アクション名にドット演算子 (.) が使用されている場合は、次の例のようにそれらの演算子を含めてください。

  `My.Action.Name`

1. デザイナーのツールバーで **[コード ビュー]** を選択し、`actions` 属性内のアクション名を検索します。

   たとえば、`Send_approval_email_` は、 **[承認の電子メールを送信します]** アクションの JSON 名です。

   ![JSON のアクション名の検索](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. デザイナー ビューに戻るには、コード ビューのツールバー上で **[デザイナー]** を選択します。

1. 最初のアクションを追加するには、 **[Actions Item - 1]\(アクション項目 - 1\)** ボックスにアクションの JSON 名を入力します。

   ![最初のアクションの入力](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 別のアクションを追加するには、 **[新しい項目の追加]** を選択します。

## <a name="reference"></a>リファレンス

ワークフロー定義言語を使用したロジック アプリの基盤となるワークフロー定義での **[JavaScript コードの実行]** アクションの構造と構文の詳細については、このアクションの [リファレンス セクション](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Logic Apps のコネクタ](../connectors/apis-list.md)の詳細について学習します。
