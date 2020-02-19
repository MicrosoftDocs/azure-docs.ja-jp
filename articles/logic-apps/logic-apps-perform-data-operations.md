---
title: データの操作の実行
description: Azure Logic Apps でデータの出力と形式に対する変換、管理、操作を行います
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666739"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Azure Logic Apps でデータの操作を実行する

この記事では、次のようなタスクのアクションを追加することにより、ロジック アプリのデータを操作する方法を示します。

* 配列からテーブルを作成します。
* 条件に基づいて他の配列から配列を作成します。
* JavaScript Object Notation (JSON) オブジェクトのプロパティからわかりやすいトークンを作成し、ワークフローでこれらのプロパティを簡単に使用できるようにします。

必要なアクションがここで見つからない場合は、Azure Logic Apps で提供されている多くの[データ操作関数](../logic-apps/workflow-definition-language-functions-reference.md)を参照してみてください。

使用できるデータ操作をまとめた次の表は、操作が使用するソース データの種類を基に整理されていますが、各説明はアルファベット順になっています。

**配列アクション** 

これらのアクションは、配列内のデータを操作するのに役立ちます。

| アクション | 説明 |
|--------|-------------|
| [**CSV テーブルの作成**](#create-csv-table-action) | 配列からコンマ区切り値 (CSV) テーブルを作成します。 |
| [**HTML テーブルの作成**](#create-html-table-action) | 配列から HTML テーブルを作成します。 |
| [**配列のフィルター処理**](#filter-array-action) | 指定したフィルターまたは条件に基づいて、配列から配列のサブセットを作成します。 |
| [**結合**](#join-action) | 配列内のすべての項目から 1 つの文字列を作成し、指定した文字で各項目を区切ります。 |
| [**選択**](#select-action) | 異なる配列のすべての項目に対して指定されているプロパティから配列を作成します。 |
||| 

**JSON アクション**

これらのアクションは、JavaScript Object Notation (JSON) 形式のデータを操作するのに役立ちます。

| アクション | 説明 |
|--------|-------------|
| [**作成**](#compose-action) | さまざまなデータ型を含むことができる複数の入力からメッセージまたは文字列を作成します。 その後は、同じ入力を繰り返し入力するのではなく、この文字列を 1 つの入力として使用できます。 たとえば、さまざまな入力から 1 つの JSON メッセージを作成できます。 |
| [**Parse JSON**](#parse-json-action) | ロジック アプリでプロパティを簡単に使用できるように、JSON コンテンツのプロパティに対するわかりやすいデータ トークンを作成します。 |
|||

さらに複雑な JSON 変換を作成する場合は、「[Liquid テンプレートを使用して高度な JSON 変換を実行する](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* データを操作するための操作が必要なロジック アプリ。

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイックスタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* ロジック アプリの最初のステップになる[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  データ操作はアクションとしてのみ使用できるので、これらのアクションを使用するには、その前に、トリガーでロジック アプリを開始し、目的の出力を作成するために必要な他のアクションを含める必要があります。

<a name="compose-action"></a>

## <a name="compose-action"></a>作成アクション

複数の入力から JSON オブジェクトなどの 1 つの出力を作成するには、**作成**アクションを使用できます。 入力では、整数、ブール値、配列、JSON オブジェクト、および Azure Logic Apps がサポートする他の任意のネイティブ型 (たとえば、バイナリや XML) など、さまざまな型を使用できます。 その後、**作成**アクションの後に続くアクションで出力を使用できます。 **作成**アクションを使用すると、ロジック アプリのワークフローを作成するときに同じ入力を繰り返さなくて済むようにもできます。

たとえば、ユーザーの名と姓を格納している文字列変数や、ユーザーの年齢を格納している整数変数など、複数の変数から JSON メッセージを作成できます。 ここでは、**作成**アクションは次の入力を受け取ります。

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

そして、次の出力を作成します。

`{"age":35,"fullName":"Owens,Sophie"}`

例を試すには、ロジック アプリ デザイナーを使用して以下の手順に従ってください。 または、コード ビュー エディターを使用する場合は、この記事の例の**作成**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - 作成](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび複数の**変数を初期化する**アクションを含むロジック アプリを使用します。 これらのアクションは、2 つの文字列変数と 1 つの整数変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["作成" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. 出力を作成するロジック アプリでは、次のいずれかの手順に従います。 

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["作成" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`compose`」と入力します。 アクションの一覧で、 **[作成]** アクションを選択します。

   !["作成" アクションの選択](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. **[入力]** ボックスで、出力を作成するための入力を指定します。

   この例では、 **[入力]** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   !["作成" アクションに使用する入力を選択する](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   完成した**作成**アクションの例を次に示します。 

   !["作成" アクションの完成例](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

基になるワークフロー定義でのこのアクションについて詳しくは、「[作成アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**作成**アクションで意図した結果が作成されるかどうかを確認するには、**作成**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**作成**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的コンテンツの一覧が開いたら、**作成**アクションで **[出力]** を選択します。

   この例では、**メールの送信**アクションを使用し、メールの本文と件名に **[出力]** フィールドが含まれます。

   !["作成" アクションの "出力" フィールド](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["作成" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV テーブルの作成アクション

JavaScript Object Notation (JSON) オブジェクトからのプロパティと値を配列に含むコンマ区切り値 (CSV) テーブルを作成するには、**CSV テーブルの作成**アクションを使用します。 その後、**CSV テーブルの作成**アクションに続くアクションで、結果のテーブルを使用できます。

コード ビュー エディターを使用する場合は、この記事の例の **CSV テーブルの作成**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - CSV テーブルの作成](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、JSON 形式のプロパティと値を含む配列が初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["CSV テーブルの作成" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. CSV テーブルを作成するロジック アプリでは、次のいずれかの手順に従います。 

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["CSV テーブルの作成" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`create csv table`」と入力します。 アクションの一覧から、**CSV テーブルの作成**アクションを選択します。

   !["CSV テーブルの作成" アクションを選択する](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. **[From]\(ソース\)** ボックスで、テーブルを作成するための配列または式を指定します。

   この例では、 **[From]\(ソース\)** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   ![CSV テーブルを作成するための配列出力を選択する](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > JSON オブジェクトのプロパティにわかりやすいトークンを作成し、プロパティを入力として選択できるようにするには、**CSV テーブルの作成**アクションを呼び出す前に [JSON の解析](#parse-json-action)を使用します。

   完成した **CSV テーブルの作成**アクションの例を次に示します。 

   !["CSV テーブルの作成" アクションの完成例](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="customize-table-format"></a>テーブルの形式をカスタマイズする

既定では、 **[列]** プロパティは、配列の項目に基づいてテーブルの列を自動的に作成するように設定されます。 カスタム ヘッダーと値を指定するには、次の手順のようにします。

1. **[列]** の一覧を開き、 **[カスタム]** を選択します。

1. **[ヘッダー]** プロパティで、代わりに使用するカスタム ヘッダー テキストを指定します。

1. **[値]** プロパティで、代わりに使用するカスタム値を指定します。

配列から値を返すには、**CSV テーブルの作成**アクションで [`item()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#item)を使用できます。 `For_each` ループでは、[`items()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#items)を使用できます。

たとえば、プロパティ値のみを含み、配列からのプロパティ名を含まないテーブル列が必要であるとします。 これらの値のみを返すには、以下に示すデザイナー ビューまたはコード ビューでの操作手順に従ってください。 この例で返される結果を次に示します。

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>デザイナー ビューでの操作

アクションで、 **[ヘッダー]** 列を空のままにします。 **[値]** 列の各行で、目的の各配列プロパティを逆参照します。 **[値]** の下の各行は、指定された配列プロパティのすべての値を返し、テーブル内の列になります。

1. **[値]** の下にある目的の各行で、編集ボックス内をクリックして、動的コンテンツの一覧が表示されるようにします。

1. 動的コンテンツ リストの **[式]** を選択します。

1. 式エディターで、必要な配列プロパティ値を指定する次の式を入力し、 **[OK]** を選択します。

   `item()?['<array-property-name>']`

   次に例を示します。

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["CSV テーブルの作成" の "説明" を逆参照する](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. 必要な配列プロパティごとに、前の手順を繰り返します。 完了すると、アクションは次の例のようになります。

   !["CSV テーブルの作成" の "item()" 関数](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. 式をより記述的なバージョンに解決するには、コード ビューに切り替え、再びデザイナー ビューに戻って、折りたたまれているアクションを再度開きます。

   これにより、**CSV テーブルの作成**アクションは次の例のようになります。

   !["CSV テーブルの作成" - 解決された式 (ヘッダーなし)](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>コード ビューでの操作

アクションの JSON 定義で、`columns` 配列内で `header` プロパティを空のストリングに設定します。 `value` プロパティごとに、目的の各配列プロパティを逆参照します。

1. デザイナーのツール バーで、 **[コード ビュー]** を選択します。

1. コード エディターでは、アクションの `columns` 配列で、必要な配列値の列ごとに、空の `header` プロパティとこの `value` 式を追加します。

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   次に例を示します。

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. デザイナー ビューに戻り、折りたたまれたアクションを再度開きます。

   **CSV テーブルの作成**アクションは次の例のようになり、式は、より記述的なバージョンに解決されました。

   !["CSV テーブルの作成" - 解決された式 (ヘッダーなし)](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

基になるワークフロー定義でのこのアクションについて詳しくは、「[テーブル アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**CSV テーブルの作成**アクションで意図した結果が作成されるかどうかを確認するには、**CSV テーブルの作成**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**CSV テーブルの作成**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的コンテンツの一覧が開いたら、**CSV テーブルの作成**アクションで **[出力]** を選択します。 

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文に **[出力]** フィールドが含まれます。

   !["CSV テーブルの作成" アクションの "出力" フィールド](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["CSV テーブルの作成" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML テーブルの作成アクション

JavaScript Object Notation (JSON) オブジェクトからのプロパティと値を配列に含む HTML テーブルを作成するには、**HTML テーブルの作成**アクションを使用します。 その後、**HTML テーブルの作成**アクションに続くアクションで、結果のテーブルを使用できます。

コード ビュー エディターを使用する場合は、この記事の例の **HTML テーブルの作成**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - HTML テーブルの作成](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、JSON 形式のプロパティと値を含む配列が初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["HTML テーブルの作成" 用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. HTML テーブルを作成するロジック アプリでは、次のいずれかの手順に従います。

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["HTML テーブルの作成" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`create html table`」と入力します。 アクションの一覧から、**HTML テーブルの作成**アクションを選択します。

   !["HTML テーブルの作成" アクションを選択する](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. **[From]\(ソース\)** ボックスで、テーブルを作成するための配列または式を指定します。

   この例では、 **[From]\(ソース\)** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   ![HTML テーブルを作成するための配列出力を選択する](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > JSON オブジェクトのプロパティにわかりやすいトークンを作成し、プロパティを入力として選択できるようにするには、**HTML テーブルの作成**アクションを呼び出す前に [JSON の解析](#parse-json-action)を使用します。

   完成した **HTML テーブルの作成**アクションの例を次に示します。

   !["HTML テーブルの作成" の完成例](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="customize-table-format"></a>テーブルの形式をカスタマイズする

既定では、 **[列]** プロパティは、配列の項目に基づいてテーブルの列を自動的に作成するように設定されます。 カスタム ヘッダーと値を指定するには、次の手順のようにします。

1. **[列]** の一覧を開き、 **[カスタム]** を選択します。

1. **[ヘッダー]** プロパティで、代わりに使用するカスタム ヘッダー テキストを指定します。

1. **[値]** プロパティで、代わりに使用するカスタム値を指定します。

配列から値を返すには、**HTML テーブルの作成**アクションで [`item()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#item)を使用できます。 `For_each` ループでは、[`items()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#items)を使用できます。

たとえば、プロパティ値のみを含み、配列からのプロパティ名を含まないテーブル列が必要であるとします。 これらの値のみを返すには、以下に示すデザイナー ビューまたはコード ビューでの操作手順に従ってください。 この例で返される結果を次に示します。

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>デザイナー ビューでの操作

アクションで、 **[ヘッダー]** 列を空のままにします。 **[値]** 列の各行で、目的の各配列プロパティを逆参照します。 **[値]** の下の各行は、指定されたプロパティのすべての値を返し、テーブル内の列になります。

1. **[値]** の下にある目的の各行で、編集ボックス内をクリックして、動的コンテンツの一覧が表示されるようにします。

1. 動的コンテンツ リストの **[式]** を選択します。

1. 式エディターで、必要な配列プロパティ値を指定する次の式を入力し、 **[OK]** を選択します。

   `item()?['<array-property-name>']`

   次に例を示します。

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["HTML テーブルの作成" アクションの逆参照プロパティ](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. 必要な配列プロパティごとに、前の手順を繰り返します。 完了すると、アクションは次の例のようになります。

   !["HTML テーブルの作成" の "item()" 関数](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. 式をより記述的なバージョンに解決するには、コード ビューに切り替え、再びデザイナー ビューに戻って、折りたたまれているアクションを再度開きます。

   これにより、**HTML テーブルの作成**アクションは次の例のようになります。

   !["HTML テーブルの作成" - 解決された式 (ヘッダーなし)](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>コード ビューでの操作

アクションの JSON 定義で、`columns` 配列内で `header` プロパティを空のストリングに設定します。 `value` プロパティごとに、目的の各配列プロパティを逆参照します。

1. デザイナーのツール バーで、 **[コード ビュー]** を選択します。

1. コード エディターでは、アクションの `columns` 配列で、必要な配列値の列ごとに、空の `header` プロパティとこの `value` 式を追加します。

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   次に例を示します。

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. デザイナー ビューに戻り、折りたたまれたアクションを再度開きます。

   **HTML テーブルの作成**アクションは次の例のようになり、式は、より記述的なバージョンに解決されました。

   !["HTML テーブルの作成" - 解決された式 (ヘッダーなし)](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

基になるワークフロー定義でのこのアクションについて詳しくは、「[テーブル アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**HTML テーブルの作成**アクションで意図した結果が作成されるかどうかを確認するには、**HTML テーブルの作成**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**HTML テーブルの作成**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的コンテンツの一覧が開いたら、**HTML テーブルの作成**アクションで **[出力]** を選択します。 

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文に **[出力]** フィールドが含まれます。

   !["HTML テーブルの作成" の "出力" フィールド](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > HTML テーブルの出力をメール アクションに含めるときは、メール アクションの詳細オプションで **[HTML です]** プロパティを **[はい]** に設定してください。 これにより、メール アクションは HTML テーブルを正しく書式設定します。

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["HTML テーブルの作成" の結果を含むメール](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>配列のフィルター処理アクション

既存の配列から特定の条件を満たす項目を含む小さい配列を作成するには、**配列のフィルター処理**アクションを使用します。 その後、**配列のフィルター処理**アクションに続くアクションで、フィルター処理された配列を使用できます。

> [!NOTE]
> 条件で使用するフィルター テキストでは、大文字と小文字が区別されます。 また、このアクションでは、配列内の項目の形式またはコンポーネントを変更することはできません。 
> 
> **配列のフィルター処理**アクションからの配列出力をアクションで使用する場合、入力として配列を受け入れるアクションを使用するか、または出力配列を別の互換性のある形式に変換する必要があります。
> 
> HTTP エンドポイントを呼び出し、JSON 応答を受け取る場合は、 **[JSON の解析]** アクションを使用して JSON 応答を処理します。 
> そうしない場合、 **[配列のフィルター処理]** アクションを使うと、JSON ペイロードの構造ではなく、応答本文のみを読み取ることができます。

コード ビュー エディターを使用する場合は、この記事の例の**配列のフィルター処理**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - 配列のフィルター処理](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、サンプルの整数を含む配列が初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   > [!NOTE]
   > この例では単純な整数の配列を使用しますが、このアクションはオブジェクトのプロパティと値に基づいてフィルター処理できる JSON オブジェクトの配列の場合に特に便利です。

   !["配列のフィルター処理" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. フィルター処理された配列を作成するロジック アプリでは、次のいずれかの手順に従います。 

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["配列のフィルター処理" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「`filter array`」と入力します。 アクションの一覧から、**配列のフィルター処理**アクションを選択します。

   !["配列のフィルター処理" アクションを選択する](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. **[From]\(ソース\)** ボックスで、フィルター処理する配列または式を指定します。

   この例では、 **[From]\(ソース\)** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   ![フィルター処理された配列を作成するための配列出力を選択する](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. 条件では、比較する配列項目を指定し、比較演算子を選択し、比較対象値を指定します。

   この例では、`item()` 関数を使用して配列の各項目にアクセスし、**配列のフィルター処理**アクションで値が 1 より大きい配列項目を検索します。

   !["配列のフィルター処理" アクションの完成例](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

基になるワークフロー定義でのこのアクションについて詳しくは、「[クエリ アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**配列のフィルター処理**アクションで意図した結果が作成されるかどうかを確認するには、**配列のフィルター処理**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**配列のフィルター処理**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的なコンテンツの一覧が表示されたら、 **[式]** を選択します。 **配列のフィルター処理**アクションから配列の出力を取得するには、**配列のフィルター処理**アクションの名前を含む次の式を入力します。

   `@actionBody('Filter_array')`

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文には **actionBody('Filter_array')** 式の出力が含まれます。

   !["配列のフィルター処理" アクションからのアクションの出力](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["配列のフィルター処理" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>結合アクション

配列のすべての項目を含み、それらの項目が特定の区切り文字で区切られた文字列を作成するには、**結合**アクションを使用します。 その後、**結合**アクションの後に続くアクションで文字列を使用できます。

コード ビュー エディターを使用する場合は、この記事の例の**参加**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - 参加](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、サンプルの整数を含む配列が初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["結合" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. 配列から文字列を作成するロジック アプリでは、次のいずれかの手順に従います。

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["結合" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「`join`」と入力します。 アクションの一覧から、次のアクションを選択します。**Join**

   !["結合" アクションを選択する](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. **[From]\(ソース\)** ボックスで、文字列として結合する項目を含む配列を指定します。

   この例では、 **[From]\(ソース\)** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。  

   ![文字列を作成するための配列出力を選択する](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. **[次を使用して結合]** ボックスに、各配列項目を区切るための文字を入力します。 

   この例では、区切り記号としてコロン (:) を使用します。

   ![区切り文字を指定する](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

基になるワークフロー定義でのこのアクションについて詳しくは、「[結合アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**結合**アクションで意図した結果が作成されるかどうかを確認するには、**結合**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**結合**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的コンテンツの一覧が開いたら、**結合**アクションで **[出力]** を選択します。 

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文に **[出力]** フィールドが含まれます。

   !["結合" アクションの "出力" フィールド](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["結合" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON の解析アクション

JavaScript Object Notation (JSON) のコンテンツのプロパティを参照したりプロパティにアクセスしたりするには、**JSON の解析**アクションを使用して、プロパティのわかりやすいフィールドまたはトークンを作成できます。 これにより、ロジック アプリの入力を指定するときに、動的コンテンツの一覧からそのプロパティを選択できます。 このアクションでは、JSON スキーマを指定するか、サンプルの JSON コンテンツまたはペイロードから JSON スキーマを生成することができます。

コード ビュー エディターを使用する場合は、この記事の例の **JSON の解析**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - JSON の解析](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、プロパティと値を含む JSON オブジェクトが初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["JSON の解析" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. JSON コンテンツを解析するロジック アプリでは、次のいずれかの手順に従います。

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["JSON の解析" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「`parse json`」と入力します。 アクションの一覧から、**JSON の解析**アクションを選択します。

   !["JSON の解析" アクションを選択する](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. **[内容]** ボックスで、解析する JSON コンテンツを指定します。

   この例では、 **[内容]** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   ![JSON の解析アクションの JSON オブジェクトを選択する](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. 解析している JSON コンテンツを記述する JSON スキーマを入力します。

   この例では、次のような JSON スキーマです。

   ![解析する JSON オブジェクトの JSON スキーマを指定する](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   スキーマがない場合は、解析している JSON コンテンツつまり "*ペイロード*" からスキーマを生成できます。 
   
   1. **[JSON の解析]** アクションで、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** で JSON コンテンツを指定し、 **[完了]** を選択します。

      ![スキーマを生成するための JSON コンテンツを入力する](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

基になるワークフロー定義でのこのアクションについて詳しくは、「[JSON の解析アクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**JSON の解析**アクションで意図した結果が作成されるかどうかを確認するには、**JSON の解析**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**JSON の解析**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的コンテンツの一覧が開くと、 **[JSON の解析]** アクションで、解析された JSON コンテンツからプロパティを選択できます。

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文に **FirstName**、**LastName**、**Email** フィールドが含まれます。

   !["メールの送信" アクションでの JSON プロパティ](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   完成したメール アクションを次に示します。

   ![メール アクションの完成例](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。 

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["JSON の解析" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>選択アクション

既存の配列内の値から構築された JSON オブジェクトを含む配列を作成するには、**選択**アクションを使用します。 たとえば、整数配列の値ごとに JSON オブジェクトを作成するには、各 JSON オブジェクトに必要なプロパティと、ソース配列の値をそれらのプロパティにマップする方法を指定します。 また、これらの JSON オブジェクトのコンポーネントを変更することはできますが、出力配列の項目の数は常にソース配列と同じです。

> [!NOTE]
> **選択**アクションからの配列出力をアクションで使用する場合、入力として配列を受け入れるアクションを使用するか、または出力配列を別の互換性のある形式に変換する必要があります。 

コード ビュー エディターを使用する場合は、この記事の例の **選択**と**変数を初期化する**のアクション定義を、独自のロジック アプリの基になる次のワークフロー定義にコピーします。[データ操作コードの例 - 選択](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と、**繰り返し**トリガーおよび**変数を初期化する**アクションを含むロジック アプリを使用します。 このアクションは、サンプルの整数を含む配列が初期値である変数を作成するように設定されています。 後でロジック アプリをテストするときは、トリガーの発生を待つことなく、手動でアプリを実行できます。

   !["選択" アクション用のサンプル ロジック アプリの開始](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. 配列を作成するロジック アプリでは、次のいずれかの手順に従います。 

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["選択" アクションの "新しいステップ" を選択する](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`select`」と入力します。 アクションの一覧で、 **[選択]** アクションを選択します。

   !["選択" アクションを選択する](./media/logic-apps-perform-data-operations/select-select-action.png)

1. **[From]\(ソース\)** ボックスで、ソース配列を指定します。

   この例では、 **[From]\(ソース\)** ボックスの内部をクリックすると、動的コンテンツの一覧が表示されるので、前に作成した変数を選択できます。

   ![選択アクションのソース配列を選択する](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. **[マップ]** ボックスの左側の列では、ソース配列の各値を割り当てるプロパティの名前を指定します。 右側の列では、プロパティを割り当てる値を表す式を指定します。

   この例では、各配列項目にアクセスする式で `item()` 関数を使用することにより、整数配列内の各値を割り当てるプロパティ名として "Product_ID" を指定します。 

   ![JSON オブジェクトのプロパティと値を指定して配列を作成する](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   完成したアクションを次に示します。

   !["選択" アクションの完成例](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

基になるワークフロー定義でのこのアクションについて詳しくは、「[選択アクション](../logic-apps/logic-apps-workflow-actions-triggers.md)」をご覧ください。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

**選択**アクションで意図した結果が作成されるかどうかを確認するには、**選択**アクションからの出力を含む通知を自分自身に送信します。

1. ロジック アプリで、**選択**アクションからの結果を送信できるアクションを追加します。

1. そのアクションでは、結果を表示する任意の場所をクリックします。 動的なコンテンツの一覧が表示されたら、 **[式]** を選択します。 **選択**アクションから配列の出力を取得するには、**選択**アクションの名前を含む次の式を入力します。

   `@actionBody('Select')`

   この例では、Office 365 Outlook の**メールの送信**アクションを使用し、メールの本文には `@actionBody('Select')` 式の出力が含まれます。

   !["選択" アクションからのアクションの出力](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. ロジック アプリを手動で実行します。 デザイナーのツール バーで、 **[実行]** を選択します。

   使用したメール コネクタをに基づいて、次のような結果が表示されます。

   !["選択" アクションの結果を含むメール](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>次のステップ

* [Logic Apps のコネクタ](../connectors/apis-list.md)について確認します。
