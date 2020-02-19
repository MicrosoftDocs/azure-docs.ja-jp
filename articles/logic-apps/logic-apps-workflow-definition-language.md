---
title: ワークフロー定義言語スキーマのリファレンス
description: Azure Logic Apps のワークフローを記述するワークフロー定義言語の JSON スキーマと構文のリファレンス ガイド
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428659"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps でのワークフロー定義言語のスキーマ リファレンス ガイド

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) でロジック アプリを作成するとき、ロジック アプリには基になるワークフロー定義があり、ロジック アプリで実行される実際のロジックが記述されています。 そのワークフロー定義は、[JSON](https://www.json.org/) を使って作成し、ワークフロー定義言語スキーマによって検証される構造に従います。 このリファレンスでは、この構造に関する概要と、ワークフロー定義で属性がスキーマによってどのように定義されるかを説明します。

## <a name="workflow-definition-structure"></a>ワークフロー定義の構造

ワークフロー定義には、常に、ロジック アプリをインスタンス化するためのトリガーと、トリガーの発動後に実行される 1 つ以上のアクションが含まれます。

ワークフロー定義の高レベルな構造を次に示します。

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attribute | 必須 | 説明 |
|-----------|----------|-------------|
| `definition` | はい | ワークフロー定義の開始要素 |
| `$schema` | ワークフロー定義を外部参照する場合のみ | ワークフロー定義言語のバージョンが記述されている JSON スキーマ ファイルの場所。次の場所にあります。 <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | いいえ | ワークフローの実行時に実行される 1 つまたは複数のアクションの定義。 詳細については、「[トリガーとアクション](#triggers-actions)」を参照してください。 <p><p>アクションの最大個数:250 |
| `contentVersion` | いいえ | ワークフロー定義のバージョン番号。既定値は "1.0.0.0" です。 ワークフローを展開するときに正しい定義であることを識別して確認できるように、使用する値を指定します。 |
| `outputs` | いいえ | ワークフローの実行から返される出力の定義。 詳細については、「[出力](#outputs)」を参照してください。 <p><p>出力の最大個数:10 |
| `parameters` | いいえ | ロジック アプリの実行時に使用する値を渡す 1 つ以上のパラメーターの定義。 詳しくは、「[パラメーター](#parameters)」をご覧ください。 <p><p>パラメーターの最大個数:50 |
| `staticResults` | いいえ | 静的な結果がこれらのアクションで有効になっている場合に、アクションによってモック出力として返される 1 つまたは複数の静的な結果の定義。 各アクションの定義で、`runtimeConfiguration.staticResult.name` 属性は `staticResults` 内部の対応する定義を参照します。 詳細については、「[静的な結果](#static-results)」を参照してください。 |
| `triggers` | いいえ | ワークフローをインスタンス化する 1 つまたは複数のトリガーの定義。 複数のトリガーを定義できます。ワークフロー定義言語しか利用できず、Logic Apps デザイナーを使って視覚的に作成することはできません。 詳細については、「[トリガーとアクション](#triggers-actions)」を参照してください。 <p><p>トリガーの最大個数:10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>トリガーとアクション

ワークフロー定義の `triggers` および `actions` セクションでは、ワークフローの実行中に発生する呼び出しを定義します。 これらのセクションの構文と詳細については、「[ワークフローのトリガーとアクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。

<a name="parameters"></a>

## <a name="parameters"></a>パラメーター

通常、デプロイのライフサイクルには、開発、テスト、ステージング、運用の異なる環境があります。 異なる環境にロジック アプリをデプロイする場合、デプロイのニーズに基づいて、接続文字列などの異なる値を使用することがあります。 また、値をハードコーディングせずにロジック アプリ全体で再利用する場合や、値を頻繁に変更する場合もあります。 ワークフロー定義の `parameters` セクションでは、ロジック アプリが実行時に使用する値のパラメーターを定義または編集できます。 これらのパラメーターをワークフロー定義の別の場所で参照するには、事前に定義しておく必要があります。

パラメーターの定義の一般的な構造を次に示します。

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Attribute | 必須 | 種類 | 説明 |
|-----------|----------|------|-------------|
| <*parameter-name*> | はい | String | 定義するパラメーターの名前 |
| <*parameter-type*> | はい | int、float、string、bool、array、object、securestring、secureobject <p><p>**注**:すべてのパスワード、キー、およびシークレットで、`securestring` 型または `secureobject` 型を使用します。`GET` 操作では、これらの型は返されません。 パラメーターのセキュリティ保護の詳細については、「[パラメーターの入力へのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)」を参照してください。 | パラメーターの型 |
| <*default-parameter-value*> | はい | `type` と同じ | ワークフローのインスタンス化時に値が指定されていない場合に使用する、既定のパラメーター値。 `defaultValue` 属性は、ロジック アプリ デザイナーがパラメーターを正しく表示できるようにする場合は必須ですが、空の値を指定することもできます。 |
| <*array-with-permitted-parameter-values*> | いいえ | Array | パラメーターが受け取ることのできる値の配列 |
| <*parameter-description*> | いいえ | JSON オブジェクト | パラメーターの説明など、その他のパラメーターの詳細 |
||||

次に、ワークフロー定義に [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を作成し、デプロイ時に必要な値を受け入れるテンプレート パラメーターを定義し、必要に応じてテンプレートまたはワークフロー定義のパラメーターを参照してハードコーディングされた値を置き換え、デプロイ時に使用する値を別の[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)に格納します。 そうすることで、ロジック アプリを更新して再デプロイしなくても、パラメーター ファイルを使用してこれらの値をより簡単に変更できます。 ユーザー名、パスワード、シークレットなどの、機密情報やセキュリティ保護が必要な情報の場合、Azure Key Vault に値を格納し、パラメーター ファイルでキー コンテナーから値を取得することができます。 テンプレートおよびワークフロー定義レベルでのパラメーターの定義の詳細と例については、「[Overview:Automate deployment for logic apps with Azure Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)」 (概要: Azure Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する) を参照してください。

<a name="static-results"></a>

## <a name="static-results"></a>静的な結果

`staticResults` 属性では、アクションの静的な結果の設定が有効になっている場合に、アクションが返すアクションのモック `outputs` および `status` を定義します。 アクションの定義で、`runtimeConfiguration.staticResult.name` 属性は `staticResults` 内部の静的な結果の定義の名前を参照します。 [静的な結果を設定してモック データでロジック アプリをテストする](../logic-apps/test-logic-apps-mock-data-static-results.md)方法をご確認ください。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attribute | 必須 | 種類 | 説明 |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | はい | String | アクションの定義が `runtimeConfiguration.staticResult` オブジェクトを介して参照できる、静的な結果の定義の名前。 詳細については、「[ランタイム構成の設定](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)」を参照してください。 <p>任意の一意の名前を使用できます。 既定では、この一意の名前に数値が追加されます。この数値は必要に応じてインクリメントされます。 |
| <*output-attributes-and-values-returned*> | はい | 場合により異なる | これらの属性の要件は、さまざまな条件によって異なります。 たとえば、`status` が `Succeeded` の場合、`outputs` 属性には、アクションによってモック出力として返される属性と値が含まれます。 `status` が `Failed` の場合は、`outputs` 属性には `errors` 属性が含まれます。これは、エラー情報が格納された、1 つ以上のエラー `message` オブジェクトの配列です。 |
| <*header-values*> | いいえ | JSON | アクションによって返されるヘッダー値 |
| <*status-code-returned*> | はい | String | アクションによって返される状態コード |
| <*action-status*> | はい | String | アクションの状態 (例: `Succeeded` または `Failed`) |
|||||

たとえば、この HTTP アクション定義では、`runtimeConfiguration.staticResult.name` 属性は、アクションのモック出力が定義されている `staticResults` 属性内部の `HTTP0` を参照します。 `runtimeConfiguration.staticResult.staticResultOptions` 属性は、静的な結果の設定が HTTP アクションで `Enabled` であることを指定します。

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP アクションは `staticResults` 内部の `HTTP0` 定義の出力を返します。 この例の状態コードのモック出力は `OK` です。 ヘッダー値のモック出力は `"Content-Type": "application/JSON"` です。 アクションの状態のモック出力は `Succeeded` です。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>式

JSON では、デザイン時に存在するリテラル値を使用できます。次はその例です。

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

実行時まで存在しない値を使うこともできます。 これらの値を表すには、実行時に評価される "*式*" を使うことができます。 式は、1 つ以上の[関数](#functions)、[演算子](#operators)、変数、明示的な値、または定数を含むことができるシーケンスです。 ワークフローの定義では、式の前にアットマーク (\@) を付けることによって、JSON 文字列値の任意の場所で式を使うことができます。 JSON 値を表す式を評価するときは、\@ 文字を削除することによって式の本体が抽出され、常に別の JSON 値になります。

たとえば、前に定義した `customerName` プロパティでは、式の中で [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 関数を使ってプロパティの値を取得し、その値を `accountName` プロパティに割り当てることができます。

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

"*文字列の補間*" により、\@ 文字と中かっこ ({}) によってラップされている文字列内で複数の式を使用することもできます。 構文は次のとおりです。

```json
@{ "<expression1>", "<expression2>" }
```

結果は常に文字列であり、この機能は `concat()` 関数と似ています。次に例を示します。 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

\@ 文字で始まるリテラル文字列がある場合は、エスケープ文字として別の \@ 文字を \@ の前に付けます (\@\@)。

式の評価方法の例を次に示します。

| JSON 値 | 結果 |
|------------|--------|
| "Sophia Owen" | 次の文字が返されます:'Sophia Owen' |
| "array[1]" | 文字 'array[1]' を返します |
| "\@\@" | 1 文字の '\@' を返します |
| " \@" | 2 文字の ' \@' を返します |
|||

次の例では、"myBirthMonth" を "January" に、"myAge" を数値 42 に定義してあるものとします。

```json
"myBirthMonth": "January",
"myAge": 42
```

式の評価方法の例を次に示します。

| JSON 式 | 結果 |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | 次の文字列が返されます:"January" |
| "\@{parameters('myBirthMonth')}" | 次の文字列が返されます:"January" |
| "\@parameters('myAge')" | 次の数値が返されます:42 |
| "\@{parameters('myAge')}" | 次の数値が文字列として返されます:"42" |
| "My age is \@{parameters('myAge')}" | 次の文字列が返されます:"My age is 42" |
| "\@concat('My age is ', string(parameters('myAge')))" | 次の文字列が返されます:"My age is 42" |
| "My age is \@\@{parameters('myAge')}" | 式を含む次の文字列が返されます:"My age is \@{parameters('myAge')}` |
|||

Logic Apps デザイナーで視覚的に作業しているときは、式ビルダーなどで式を作成できます。

![Logic Apps デザイナー > 式ビルダー](./media/logic-apps-workflow-definition-language/expression-builder.png)

完了すると、ワークフロー定義内の対応するプロパティに式が表示されます。次の例では `searchQuery` プロパティです。

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>出力

`outputs` セクションでは、実行完了時にワークフローが返すことのできるデータを定義します。 たとえば、各実行から特定の状態または値を追跡するには、ワークフローの出力がそのデータを返すように指定します。

> [!NOTE]
> サービスの REST API からの受信要求に応答するときは、`outputs` を使わないでください。 代わりに、`Response` アクションの種類を使います。 詳しくは、「[ワークフローのトリガーとアクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。

出力の定義の一般的な構造を次に示します。

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attribute | 必須 | 種類 | 説明 |
|-----------|----------|------|-------------|
| <*key-name*> | はい | String | 出力戻り値のキーの名前 |
| <*key-type*> | はい | int、float、string、securestring、bool、array、JSON オブジェクト | 出力戻り値の型 |
| <*key-value*> | はい | <*key-type*> と同じ | 出力の戻り値 |
|||||

ワークフローの実行からの出力を取得するには、Azure portal でロジック アプリの実行履歴と詳細を確認するか、または [Workflow REST API](https://docs.microsoft.com/rest/api/logic/workflows) を使います。 Power BI などの外部システムに出力を渡してダッシュボードを作成することもできます。

<a name="operators"></a>

## <a name="operators"></a>オペレーター

[式](#expressions)と[関数](#functions)では、演算子はプロパティや配列内の値の参照などの特定のタスクを実行します。

| 演算子 | タスク |
|----------|------|
| ' | 入力として、または式や関数の中で文字列リテラルを使うには、単一引用符で文字列のみをラップします (例: `'<myString>'`)。 二重引用符を ("") を使用しないでください。式全体を囲む JSON の書式設定と競合します。 次に例を示します。 <p>**正しい**: length('Hello') </br>**正しくない**: length("Hello") <p>配列または数値を渡すとき、句読点をラップする必要はありません。 次に例を示します。 <p>**正しい**: length([1, 2, 3]) </br>**正しくない**: length("[1, 2, 3]") |
| [] | 配列内の特定の位置 (インデックス) にある値を参照するには、角かっこを使います。 たとえば、配列内の 2 番目の項目を取得するには次のようにします。 <p>`myArray[1]` |
| 。 | オブジェクト内のプロパティを参照するには、ドット演算子を使用します。 たとえば、`customer` JSON オブジェクトの `name` プロパティを取得するには、次のようにします。 <p>`"@parameters('customer').name"` |
| ? | 実行時エラーを発生させずにオブジェクト内の null プロパティを参照するには、疑問符演算子を使います。 たとえば、次の式を使うと、トリガーからの null 出力を処理できます。 <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>関数

一部の式では、ワークフロー定義の実行開始時にはまだ存在していない可能性のある値が、実行時のアクションから取得されます。 このような値を式で参照または使用するには、ワークフロー定義言語が提供する "[*関数*](../logic-apps/workflow-definition-language-functions-reference.md)" を使用できます。

## <a name="next-steps"></a>次のステップ

* [ワークフロー定義言語のアクションとトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md)について学習する
* [ワークフローの REST API](https://docs.microsoft.com/rest/api/logic/workflows) を使用してプログラムによってロジック アプリを作成および管理する方法を学習する
