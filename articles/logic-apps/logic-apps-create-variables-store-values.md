---
title: 値を格納して渡すための変数を作成して管理する
description: Azure Logic Apps で作成する自動化されたタスクとワークフローで、変数を使用して、値を格納、管理、使用、および渡す方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333904"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Azure Logic Apps で変数を使用して値を格納および管理する

この記事では、ロジック アプリに値を格納するために使用する変数を作成して操作する方法について説明します。 たとえば、変数を使用してループの実行回数を追跡することができます。 配列を反復処理したり、配列の特定の要素をチェックしたりするには、変数を使用して各配列要素のインデックス番号を参照することができます。

整数、浮動小数点、ブール、文字列、配列、オブジェクトなど、各種データ型の変数を作成できます。 変数を作成した後、次のようなタスクを実行できます。

* 変数の値を取得または参照する。
* 変数を一定の値だけ増減させる。この操作は "*値を増やす*" および "*値を減らす*" と呼ばれています。
* 異なる値を変数に代入する。
* 文字列または配列の最後の項目として、変数の値を挿入または "*追加*" します。

変数は、それを作成したロジック アプリ インスタンスの範囲内でのみ、グローバルに存在します。 また、同じロジック アプリ インスタンス内であれば、ループの反復処理の境界を越えて存続します。 アクションの出力を参照するときはアクションの名前を使用するのが普通ですが、変数を参照するときは、変数の名前をトークンとして使用します。

> [!IMPORTANT]
> 既定では、"For each" ループのサイクルは並列に実行されます。 ループ内で変数を使用すると、[順番に](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)ループが実行されるので、変数から予測可能な結果が返されます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 変数の作成先となるロジック アプリ

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイックスタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* ロジック アプリの最初のステップになる[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  変数を作成したり操作したりするためのアクションを追加するには、ロジック アプリがトリガーによって開始される必要があります。

<a name="create-variable"></a>

## <a name="initialize-variable"></a>変数を初期化する

変数の作成とそのデータ型および初期値の宣言は、ロジック アプリの 1 つのアクション内ですべて行うことができます。 変数はグローバル レベルでのみ宣言でき、その範囲をスコープや条件、ループ内に限定することはできません。

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。

   この例では、Azure portal と既存のトリガーを含んだロジック アプリを使用します。

1. ロジック アプリの変数の追加先となるステップで、次のいずれかの手順に従います。 

   * 最後のステップにアクションを追加するには、 **[新しいステップ]** を選択します。

     !["ロジック アプリ デザイナー" ページで選択されている "新しいステップ" のスクリーンショット。](./media/logic-apps-create-variables-store-values/add-action.png)

   * ステップとステップの間にアクションを追加するには、接続矢印の上にマウスを移動してプラス記号 **[+]** を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`variables`」と入力します。 アクションの一覧から **[変数を初期化する]** を選択します。

   ![選択アクション](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. 次に示すように、変数に関する情報を指定します。

   | プロパティ | 必須 | 値 |  説明 |
   |----------|----------|-------|--------------|
   | **名前** | はい | <*variable-name*> | 値を増やす変数の名前 |
   | **Type** | はい | <*variable-type*> | 変数のデータ型 |
   | **Value** | いいえ | <*start-value*> | 変数の初期値 <p><p>**ヒント**:省略することもできますが、変数の開始値を常に把握するため、この値を設定することをお勧めします。 |
   |||||

   次に例を示します。

   ![変数を初期化する](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 続けて必要なアクションを追加します。 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

デザイナーからコード ビュー エディターに切り替えると、JavaScript Object Notation (JSON) 形式で記述されたロジック アプリの定義内には、 **[変数を初期化する]** アクションが次のように表示されます。

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> **変数を初期化する** アクションには、配列として構成されている `variables` セクションがありますが、アクションで作成できる変数は一度に 1 つだけです。 新しい変数ごとに、個別に **変数を初期化する** アクションが必要です。

その他の型の変数についても、いくつかの例を示します。

*文字列変数*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*ブール型変数*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*整数の配列*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*文字列の配列*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>変数の値を取得する

変数の内容は、コード ビュー エディターやロジック アプリ デザイナーで [variables() 関数](../logic-apps/workflow-definition-language-functions-reference.md#variables)を使用して取得または参照することもできます。 アクションの出力を参照するときはアクションの名前を使用するのが普通ですが、変数を参照するときは、変数の名前をトークンとして使用します。

たとえば、次の式は、[先ほどこの記事の中で作成](#append-value)した配列の変数から、`variables()` 関数を使用して要素を取得しています。 `string()` 関数は、変数の内容を文字列形式 (`"1, 2, 3, red"`) で返します。

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>変数の値を増やす 

変数を一定の値だけ増やす ("*値を増やす*") には、**変数の値を増やす** アクションをロジック アプリに追加します。 このアクションが正しく機能するのは、整数型と浮動小数点型の変数だけです。

1. ロジック アプリ デザイナーで、増やしたい変数があるステップの下から、 **[新しいステップ]** を選択します。 

   たとえば、このロジック アプリには既にトリガーがあり、また変数を作成したアクションが存在します。 そこで、これらのステップの下に新しいアクションを追加します。

   ![アクションを追加する](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   既存のステップ間にアクションを追加するには、接続矢印の上にマウスを移動して正符号 (+) を表示させます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「変数の値を増やす」と入力します。 アクションの一覧で **[変数の値を増やす]** を選択します。

   ![[変数の値を増やす] アクションを選択](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. 変数の値を増やすには次の情報を入力します。

   | プロパティ | 必須 | 値 |  説明 |
   |----------|----------|-------|--------------|
   | **名前** | はい | <*variable-name*> | 値を増やす変数の名前 |
   | **Value** | いいえ | <*increment-value*> | 変数の値を増やすために使用する値。 既定値は 1 です。 <p><p>**ヒント**:省略することもできますが、変数の値を増やす場合の具体的な値を常に把握するため、この値を設定することをお勧めします。 |
   ||||

   次に例を示します。

   ![変数の値を増やす場合の例](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

デザイナーからコード ビュー エディターに切り替えると、JSON 形式で記述されたロジック アプリの定義内には、 **[変数の値を増やす]** アクションが次のように表示されます。

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>例:ループ カウンターを作成する

変数は、ループの実行回数をカウントする目的でよく使用されます。 ここでは、そのタスクの例として、メールの添付ファイルをカウントするループを作成することにより、実際に変数を作成して使用する方法を紹介しています。

1. Azure portal で、空のロジック アプリを作成します。 新しいメールと添付ファイルをチェックするトリガーを追加します。

   この例では、Office 365 Outlook の "**新しい電子メールが届いたとき**" のトリガーを使用します。 このトリガーは、メールに添付ファイルが含まれているときにだけ作動するように設定できます。 ただし、添付ファイルを含んだ新しいメールをチェックするものであれば、どのコネクタを使用してもかまいません (Outlook.com コネクタなど)。

1. トリガーで添付ファイルの有無をチェックして、それをロジック アプリのワークフローに渡すには、次のプロパティで **[はい]** を選択します。

   * **添付ファイルあり**
   * **添付ファイルを含める**

   ![添付ファイルの有無をチェックして追加する](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. [ **[変数を初期化する]** アクション](#create-variable)を追加します。 初期値が 0 である `Count` という名前の整数変数を作成します。

   !["変数を初期化する" アクションを追加](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. 各添付ファイルを反復して処理するには、*for each* ループを追加します。

   1. **[変数を初期化する]** アクションの下で、 **[新しいステップ]** を選択します。

   1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、検索フィルターとして「`for each`」と入力し、アクションとして **[For each]** を選択します。

      !["for each" ループを追加](./media/logic-apps-create-variables-store-values/add-loop.png)

1. このループの **[以前の手順から出力を選択]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[添付ファイル]** を選択します。

   ![[添付ファイル] を選択](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **[添付ファイル]** プロパティにより、トリガーの出力からメールの添付ファイルを含んだ配列がループに渡されます。

1. **For each** ループで、 **[アクションの追加]** を選択します。

   ![[アクションの追加] を選択](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. 検索ボックスに、フィルターとして「変数の値を増やす」と入力します。 アクションの一覧で **[変数の値を増やす]** を選択します。

   > [!NOTE]
   > **[変数の値を増やす]** アクションがループ内に表示されることを確認します。 ループの外側に表示されている場合は、このアクションをループの内側にドラッグします。

1. **[変数の値を増やす]** アクションの **[名前]** ボックスの一覧から **Count** 変数を選択します。

   !["Count" 変数を選択](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. ループの下に、添付ファイルの数を送信するアクションを追加します。 たとえば、そのアクション内で、**Count** 変数の値を挿入します。

   ![結果を送信するアクションの追加](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. ロジック アプリが有効になっていない場合は、ロジック アプリのメニューで **[概要]** を選択します。 ツールバーで、 **[Enable]\(有効化\)** を選択します。

1. ロジック アプリ デザイナーのツール バーの **[実行]** を選択します。 この手順では、ロジック アプリを手動で起動します。

1. この例で使用した電子メール アカウントに、添付ファイルを少なくとも 1 つ含んだメールを送信します。

   この手順によってロジック アプリのトリガーが作動し、ロジック アプリのワークフローのインスタンスが作成されて実行されます。 この結果、送信したメールに含まれる添付ファイルの数を示した自分宛てのメッセージまたはメールがロジック アプリから届きます。

デザイナーからコード ビュー エディターに切り替えると、JSON 形式で記述されたロジック アプリの定義内には、**変数の値を増やす** アクションが含まれる **For each** ループが次のように表示されます。

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>変数の値を減らす

変数を一定の値だけ減らす ("*値を減らす*") 手順は、[変数の値を増やす](#increment-value)場合と変わりません。ただし、そのときに検索して選択するアクションは、 **[変数の値を減らす]** になります。 このアクションが正しく機能するのは、整数型と浮動小数点型の変数だけです。

**[変数の値を減らす]** アクションのプロパティを次に示します。

| プロパティ | 必須 | 値 |  説明 |
|----------|----------|-------|--------------|
| **名前** | はい | <*variable-name*> | 値を減らす変数の名前 | 
| **Value** | いいえ | <*increment-value*> | 変数の値を減らすのに使用する値。 既定値は 1 です。 <p><p>**ヒント**:省略することもできますが、変数の値を減らす場合の具体的な値を常に把握するため、この値を設定することをお勧めします。 |
||||| 

デザイナーからコード ビュー エディターに切り替えると、JSON 形式で記述されたロジック アプリの定義内には、 **[変数の値を減らす]** アクションが次のように表示されます。

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>変数を設定する

既存の変数に異なる値を割り当てる手順は、次の点を除いて、[変数の値を増やす](#increment-value)場合と同じです。

1. 検索して選択するアクションは **[変数の設定]** になります。

1. 変数の名前と代入する値を指定します。 新しい値と変数のデータ型が一致している必要があります。 このアクションには既定値がないため、値は必須です。

**[変数の設定]** アクションのプロパティを次に示します。

| プロパティ | 必須 | 値 |  説明 |
|----------|----------|-------|--------------|
| **名前** | はい | <*variable-name*> | 変更する変数の名前 |
| **Value** | はい | <*new-value*> | 変数に代入する値。 両方のデータ型が一致している必要があります。 |
||||| 

> [!NOTE]
> 変数の値を増やすまたは値を減らす場合を除き、ループ内で変数に変更を加えると、予期しない結果を招く "*可能性*" があります。既定では、ループがパラレル (またはコンカレント) に実行されるためです。 このような場合は、ループを逐次的に実行するように設定してみてください。 たとえば、ループ内で変数の値を参照するとき、ループ インスタンスの最初と最後で値が一致していなければならない場合は、次の手順に従ってループの動作を変更してください。 
>
> 1. ループの右上隅の省略記号ボタン **[...]** を選択し、 **[設定]** を選択します。
> 
> 2. **[コンカレンシー制御]** の **[既定値の上書き]** 設定を **[オン]** に変更します。
>
> 3. **[並列処理の次数]** スライダーをドラッグして **1** に設定します。

デザイナーからコード ビュー エディターに切り替えると、JSON 形式で記述されたロジック アプリの定義内には、 **[変数の設定]** アクションが次のように表示されます。 この例では、`Count` 変数の現在の値を別の値に変更しています。

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>変数に追加する

文字列を格納する変数または配列を格納する変数では、その文字列または配列の最後の要素として変数の値を挿入 ("*追加*") することができます。 その手順は、次の点を除いて、[変数の値を増やす](#increment-value)場合と同じです。 

1. 対象の変数が文字列か配列かによって、次のいずれかのアクションを検索して選択します。 

   * **文字列変数に追加**
   * **配列変数に追加** 

1. 文字列または配列の最後の要素として追加する値を指定します。 この値は必須です。

各 **追加** アクションのプロパティを次に示します。

| プロパティ | 必須 | 値 |  説明 |
|----------|----------|-------|--------------|
| **名前** | はい | <*variable-name*> | 変更する変数の名前 |
| **Value** | はい | <*append-value*> | 追加する値。データ型は任意です。 |
|||||

デザイナーからコード ビュー エディターに切り替えると、JSON 形式で記述されたロジック アプリの定義内には、 **[配列変数に追加]** アクションが次のように表示されます。 この例では、配列の変数を作成し、その最後の要素として新たに値を追加しています。 この結果、更新後の変数に格納される配列の内容は `[1,2,3,"red"]` になります。

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>次のステップ

* [Logic Apps のコネクタ](../connectors/apis-list.md)について確認します。
