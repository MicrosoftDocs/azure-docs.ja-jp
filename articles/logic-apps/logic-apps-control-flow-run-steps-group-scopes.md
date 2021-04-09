---
title: スコープ別にアクションをグループ化して実行する
description: Azure Logic Apps でグループの状態に基づいて実行されるスコープ付きアクションを作成する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314854"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Azure Logic Apps でスコープを使用してグループの状態に基づいてアクションを実行する

別のアクションのグループが成功または失敗した後にのみアクションを実行するには、そのアクションを "*スコープ*" の中にグループ化します。 この構造は、アクションを論理グループとして整理し、そのグループの状態を評価して、そのスコープの状態に基づいてアクションを実行するときに便利です。 そのスコープ内のすべてのアクションの実行が完了すると、スコープも独自の状態を取得します。 たとえば、[例外とエラー処理](../logic-apps/logic-apps-exception-handling.md#scopes)を実装するときにスコープを使用できます。 

スコープの状態を調べるには、ロジック アプリの実行状態を判定するのと同じ基準 ( **[成功]** 、 **[失敗]** 、 **[取り消し済み]** など) を使用できます。 既定では、スコープのすべてのアクションが成功すると、そのスコープの状態は **[成功]** とマークされます。 ただし、スコープ内のいずれかのアクションが失敗するかキャンセルされると、そのスコープの状態は **[失敗]** とマークされます。 スコープの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md)に関するページをご覧ください。 

一例として、特定のアクションを実行するスコープと、そのスコープの状態をチェックする条件を使用する、基本のロジック アプリを紹介します。 スコープ内のいずれかのアクションが失敗または予期せず終了した場合、そのスコープはそれぞれ **[失敗]** または **[中止]** とマークされ、ロジック アプリによって [Scope failed]\(スコープが失敗した場合\) というメッセージが送信されます。 スコープ内のすべてのアクションが成功した場合、ロジック アプリは [Scope succeeded]\(スコープが成功した場合\) のメッセージを送信します。

![図には、ロジック アプリのスコープのフローと、[Scope failed]\(スコープが失敗した場合\) と [Scope succeeded]\(スコープが成功した場合\) の例が示されています。](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>前提条件

この記事の例に従って進めるには、次の項目が必要です。

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Logic Apps でサポートされる任意の電子メール プロバイダーの電子メール アカウント。 この例では、Outlook.com を使用します。 別のプロバイダーを使用する場合、フロー全般は変わりませんが、UI の表示が異なります。

* Bing 地図のキー。 このキーを取得するには、<a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">Bing 地図のキーの取得</a>に関する記事をご覧ください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

## <a name="create-sample-logic-app"></a>サンプルのロジック アプリを作成する

まず、このサンプルのロジック アプリを作成し、後でスコープを追加します。

![サンプルのロジック アプリを作成する](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Bing 地図のサービスを指定した間隔でチェックする **[スケジュール - 繰り返し]** トリガー
* 2 つの場所の間の移動時間をチェックする **[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\)** アクション
* 移動時間が指定した移動時間を超えるかどうかをチェックする条件文
* 現在の移動時間が指定した時間を超えたことで電子メールを送信するアクション

ロジック アプリはいつでも保存できるため、頻繁に保存してください。

1. まだサインインしていない場合は、<a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。 空のロジック アプリを作成します。

1. **[スケジュール - 繰り返し]** トリガーを追加します。 **[間隔]** は "1"、 **[頻度]** は [分] に、それぞれ設定します

   ![[スケジュール - 繰り返し] トリガーを設定する](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > ビューを視覚的に簡略化し、デザイナーで各アクションの詳細を非表示にするには、これらのステップを進むにつれ各アクションのシェイプを折りたたみます。

1. **[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\)** アクションを追加します。 

   1. Bing 地図の接続が存在しない場合、接続を作成するように求められます。

      | 設定 | 値 | 説明 |
      | ------- | ----- | ----------- |
      | **Connection Name** | BingMapsConnection | 接続の名前を指定します。 | 
      | **API キー** | <*your-Bing-Maps-key*> | あらかじめ取得しておいた Bing 地図のキーを入力します。 | 
      ||||  

   1. この画像の下の表に示すように、 **[Get route]\(ルートを取得する\)** アクションを設定します。

      ![[Bing Maps - Get route]\(Bing 地図 - ルートを取得する\) アクションを設定する](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      これらのパラメーターの詳細については、「[Calculate a route (ルートの計算)](/bingmaps/rest-services/routes/calculate-a-route)」を参照してください。

      | 設定 | 値 | 説明 |
      | ------- | ----- | ----------- |
      | **通過地点 1** | <*start*> | ルートの出発地を入力します。 | 
      | **通過地点 2** | <*end*> | ルートの目的地を入力します。 | 
      | **回避** | なし | ルート上で回避する要素 (高速道路、通行料金など) を入力します。 使用できる値については、「[Calculate a route](/bingmaps/rest-services/routes/calculate-a-route)」 (ルートを計算する) をご覧ください。 | 
      | **最適化** | timeWithTraffic | ルートを最適化するためのパラメーター (距離、最新の交通情報を加味した移動時間など) を選択します。 この例では、値 "timeWithTraffic" を使用します。 | 
      | **距離単位** | <*your-preference*> | ルートを計算する距離の単位を入力します。 この例では、次の値を使用します:"マイル" | 
      | **Travel mode (移動手段)** | Driving (車) | ルートの移動手段を入力します。 この例では、値 "Driving (車)" を使用します。 | 
      | **Transit Date-Time (交通機関の日時)** | なし | 乗り換えモードの場合のみ適用されます。 | 
      | **Transit Date-Type Type (交通機関の日時の種類)** | なし | 乗り換えモードの場合のみ適用されます。 | 
      ||||  

1. 現在の交通量を加味した移動時間が指定の時間を超えるかどうかをチェックする[条件を追加](../logic-apps/logic-apps-control-flow-conditional-statement.md)します。 
   この例に対して、次の手順に従います。

   1. 条件の名前を次の説明に変更します: **If traffic time is more than specified time (移動時間が指定した時間を超える場合)**

   1. 左端の列で、 **[値の選択]** ボックス内をクリックします。動的コンテンツ リストが表示されます。 その一覧から、 **[Travel Duration Traffic]\(移動時間の交通量\)** フィールドを選択します (秒単位)。 

      ![条件をビルドする](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 真ん中のボックスで、 **[次の値より大きい]** 演算子を選択します。

   1. 右端の列にこの比較値を入力します。これは秒単位であり、10 分であれば次の値になります:**600**

      完成した条件は次の例のようになります。

      ![完成した条件](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. **[If true]\(true の場合\)** ブランチに、お使いの電子メール プロバイダーの "電子メールを送信する" アクションを追加します。 
   この画像にある手順に従い、このアクションを設定します。

   ![[If true]\(true の場合\) ブランチに [Send an email]\(電子メールを送信する\) アクションを追加する](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. **[宛先]** フィールドに、テスト用に自分の電子メール アドレスを入力します。

   1. **[件名]** フィールドに、次のテキストを入力します。

      ```Time to leave: Traffic more than 10 minutes```

   1. **[本文]** フィールドに、次のテキストを入力し、末尾にスペースを追加します。 

      ```Travel time:```

      **[本文]** フィールドにカーソルが表示されている間は、この時点で使用できる任意のパラメーターを選択できるように、動的コンテンツ リストが開いたままになります。

   1. 動的コンテンツ リストの **[式]** を選択します。

   1. **div()** 関数を探して選択します。 
      関数の括弧内にカーソルを置きます。

   1. カーソルが関数の括弧内にある状態で **[動的コンテンツ]** を選択すると、動的コンテンツ リストが表示されます。 
   
   1. **[ルートを取得する]** セクションから、 **[Travel Duration Traffic]\(移動時間の交通量\)** フィールドを選択します。

      ![[Travel Duration Traffic]\(移動時間の交通量\) フィールドを選択する](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. フィールドが JSON 形式に解決された後、数字 ```60``` に続けて **カンマ** (```,```) を追加して、 **[Travel Duration Traffic]\(移動時間の交通量\)** の値を秒から分に変換します。 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      これで式は次の例のようになります。

      ![式を仕上げる](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. 完了したら、 **[OK]** を選びます。

   <!-- markdownlint-disable MD038 -->
   1. 式が解決された後は、先頭にスペースを入れたテキスト ``` minutes``` を追加します
  
       これで **[本文]** フィールドは次の例のようになります。

       ![完成した[本文] フィールド](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. ロジック アプリを保存します。

次に、スコープを追加して特定のアクションをグループ化し、それらの状態を評価します。

## <a name="add-a-scope"></a>スコープを追加する

1. まだ開いていない場合は、ロジック アプリ デザイナーでロジック アプリを開きます。 

1. 必要なワークフローの場所にスコープを追加します。 たとえば、ロジック アプリ ワークフローに既存の手順間のスコープを追加するには、次の手順を行います。 

   1. スコープを追加する場所の矢印の上にポインターを移動します。 
   **プラス記号** ( **+** ) を選択し、 **[アクションの追加]** を選択します。

      ![スコープを追加する](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. 検索ボックスに、フィルターとして「scope」と入力します。 
   **[スコープ]** アクションを選択します。

## <a name="add-steps-to-scope"></a>スコープに手順を追加する

1. ここで、スコープ内で実行するステップを追加するか、既存のステップをドラッグします。 この例では、次のアクションをスコープにドラッグします。
      
   * **[Get route]\(ルートを取得する\)**
   * **If traffic time is more than specified time (移動時間が指定した時間を超える場合)** 。**true** と **false** の両方のブランチが含まれます。

   これでロジック アプリは次の例のようになります。

   ![スコープが追加された状態](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. スコープの下に、スコープの状態をチェックする条件を追加します。 条件の名前を次の説明に変更します: **スコープが失敗した場合**

   ![スコープの状態をチェックする条件を追加する](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. 条件で、スコープの状態が "失敗" または "中止" に相当するかどうかを判断する次のような式を追加します。 

   1. 別の行を追加するには、 **[追加]** を選択します。 

   1. 各行で左のボックス内をクリックすると、動的コンテンツ リストが表示されます。 
   動的コンテンツ リストから **[式]** を選択します。 編集ボックスにこの式を入力し、 **[OK]** を選択します。 
   
      `result('Scope')[0]['status']`

      ![結果の式が強調表示された [式] ボックスを示すスクリーンショット。](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. 両方の行で演算子に **[次の値に等しい]** を選択します。 
   
   1. 比較値については、最初の行に `Failed` を入力します。 
   2 番目の行に `Aborted` を入力します。 

      完成した条件は次の例のようになります。

      ![スコープの状態をチェックする式を追加する](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      次に、条件によってスコープの状態が確認され、後の手順で定義する照合アクションが実行されるように、条件の `runAfter` プロパティを設定します。

   1. **[If scope failed]** 条件で **省略記号** (...) ボタンを選択し、 **[実行条件の構成]** を選択します。

      !["runAfter" プロパティを構成する](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. 状態が **is successful** (成功した)、**has failed** (失敗した)、**is skipped** (スキップされた)、**has timed out** (タイムアウトした) のスコープをすべて選択します。

      ![スコープの状態を選択する](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. 完了したら、 **[完了]** を選択します。 
   条件に "情報" アイコンが表示されます。

1. **[If true]\(true の場合\)** ブランチと **[If false]\(false の場合\)** ブランチに、スコープの状態に基づいて実行するアクション (例: 電子メールまたはメッセージを送信する) を追加します。

   ![スコープの状態に基づいて行うアクションを追加する](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. ロジック アプリを保存します。

完成したロジック アプリは次の例のようになります。

![完成したロジック アプリにスコープが追加された状態](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>作業をテストする

デザイナーのツールバーで、 **[実行]** を選択します。 すべてのアクションが成功した場合、ロジック アプリは [Scope succeeded]\(スコープが成功した場合\) のメッセージを送信します。 いずれかのアクションが失敗した場合は、[Scope failed]\(スコープが失敗した場合\) のメッセージが送信されます。 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON の定義

コード ビューで操作している場合、代わりにロジック アプリの JSON 定義でスコープ構造を定義できます。 たとえば、前のロジック アプリのトリガーとアクションの JSON 定義は、次のようになります。

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>サポートを受ける

* ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
* 機能や提案について投稿や投票を行うには、[Azure Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

* [条件に基づいてステップを実行する (条件付きステートメント)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [さまざまな値に基づいてステップを実行する (switch ステートメント)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [ステップを実行して繰り返す (ループ)](../logic-apps/logic-apps-control-flow-loops.md)
* [並列ステップを実行または結合する (分岐)](../logic-apps/logic-apps-control-flow-branches.md)