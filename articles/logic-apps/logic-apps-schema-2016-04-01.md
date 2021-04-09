---
title: スキーマの更新 (2016 年 6 月 1 日)
description: Azure Logic Apps でロジック アプリ定義のために更新されたスキーマ バージョン 2016-06-01
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000319"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure Logic Apps のスキーマの更新 - 2016 年 6 月 1 日

Azure Logic Apps の[更新されたバージョンのスキーマ](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)と API には、ロジック アプリの信頼性と使いやすさを向上させる複数の機能強化が行われています。

* [スコープ](#scopes)により、複数のアクションを一連のアクションとしてグループ化したり、入れ子したりできるようになりました。
* [条件とループ](#conditions-loops)がアクションに昇格しました。
* `runAfter` プロパティ (`dependsOn` の後継) でアクションの実行順序をより正確に定義できるようになりました。

ロジック アプリを 2015 年 8 月 1 日の preview スキーマから 2016 年 6 月 1 日のスキーマにアップグレードするには、[アップグレードに関するセクションを参照](#upgrade-your-schema)してください。

<a name="scopes"></a>

## <a name="scopes"></a>スコープ

このスキーマにはスコープが含まれており、アクションをまとめてグループ化したり、入れ子にできます。 たとえば、1 つの条件に別の条件を含めることができます。 [スコープの構文](./logic-apps-control-flow-loops.md)の詳細を確認するか、以下のコードで基本的なスコープの例を確認してください。

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>条件とループに関する変更

以前のバージョンのスキーマでは、条件とループはパラメーターとして単一のアクションに関連付けられていました。 このスキーマではその制限が廃止され、条件とループはアクションのタイプとして利用できるようになりました。 [ループおよびスコープ](./logic-apps-control-flow-loops.md)と、[条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)の詳細を確認するか、条件アクションを示すこの基本的な例を確認してください。

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>runAfter プロパティ

`dependsOn` プロパティが `runAfter` プロパティに置き換えられ、前のアクションの状態に基づいてアクションの実行順序をより正確に指定できるようになりました。 `dependsOn` プロパティでは、アクションを実行したかった回数ではなく、前のアクションが成功したか、失敗したか、スキップされたかに応じて "アクションが実行され成功した" かどうかが示されていました。 `runAfter` プロパティを使えば、オブジェクトの実行後のアクションをすべて名前で指定できるため、オブジェクトとしての柔軟性が大きくなります。 このプロパティでは、トリガーとして許容される状態の配列も定義します。 たとえば、アクション A が成功し、かつ、アクション B が成功または失敗した後でアクションが実行されるようにする場合には、次のような `runAfter` プロパティを設定します。

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>スキーマのアップグレード

[最新のスキーマ](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)にアップグレードするには、いくつかの手順を行うだけです。 アップグレード プロセスでは、アップグレード スクリプトを実行し、新しいロジック アプリを保存します。必要があれば、以前のロジック アプリの上書きも行います。

1. Azure Portal でロジック アプリを開きます。

2. **[概要]** に移動します。 ロジック アプリのツール バーで、 **[スキーマの更新]** を選択します。
   
   ![[スキーマの更新] を選択][1]
   
   アップグレード後の定義が返されます。これは、必要に応じてコピーしてリソース定義に貼り付けることができます。 

   > [!IMPORTANT]
   > アップグレードされたロジック アプリ内でもすべての接続参照が有効のままとなるように、*必ず* **[名前を付けて保存]** を選択します。

3. アップグレードのブレードのツール バーで **[名前を付けて保存]** を選択します。

4. ロジックの名前と状態を入力します。 **[作成]** をクリックして、アップグレードしたロジック アプリをデプロイします。

5. アップグレードしたロジック アプリが正常に動作することを確認します。
   
   > [!NOTE]
   > 手動トリガーまたは要求トリガーを使用している場合、新しいロジック アプリではコールバック URL が変化します。 新しい URL をテストして、エンド ツー エンドのエクスペリエンスを確認してください。 以前の URL をそのまま維持する場合には、既存のロジック アプリを複製しておきます。

6. *(省略可能)* 以前のロジック アプリを新しいスキーマ バージョンで上書きするには、ツール バーの **[Clone (複製)]** ( **[スキーマの更新]** の横) を使用します。 このステップは、ロジック アプリのリソース ID または要求トリガー URL をそのまま維持したい場合に必要となります。

## <a name="upgrade-tool-notes"></a>アップグレード ツールの注意事項

### <a name="mapping-conditions"></a>条件のマッピング

アップグレード ツールでは、アップグレード後の定義で条件分岐アクションを可能な限り 1 つのスコープとしてグループ化しようとします。 具体的には、`@equals(actions('a').status, 'Skipped')` というデザイナー パターンが、`else` アクションとして表示されます。 ただし、認識できないパターンが検出された場合は、true の分岐と false の分岐について別個の条件が作成される可能性があります。 アップグレード後、必要に応じてアクションのマッピングを変更してください。

#### <a name="foreach-loop-with-condition"></a>条件判定を伴う foreach ループ

新しいスキーマでは、フィルター アクションを使用することで、項目ごとに 1 つの条件が設定された **For each** ループを使用するパターンをレプリケートすることができます。 ただし、変更はアップグレード時に自動的に行われます。 このような条件は、**For each** ループに先行して表示されるフィルター アクションに置き換えられます。つまり、このフィルター アクションによって、条件に一致したアイテムの配列のみが取得され、その配列が **For each** アクションに渡されます。 例については、[ループとスコープ](./logic-apps-control-flow-loops.md)に関する記事を参照してください。

### <a name="resource-tags"></a>リソース タグ

リソース タグはアップグレード後に削除されるので、アップグレード後のワークフローで設定し直す必要があります。

## <a name="other-changes"></a>その他の変更点

### <a name="renamed-manual-trigger-to-request-trigger"></a>manual トリガーの名前を request トリガーに変更

`manual` タイプのトリガーが非推奨となり、`http` タイプの `request` に名前が変更されました。 これにより、このトリガーを使用して構築されるパターンとの一貫性が向上しました。

### <a name="new-filter-action"></a>新しい 'フィルター' アクション

大きな配列のサイズを小さくして、より小規模な一連のアイテムにするには、`filter` という新しいタイプを使用します。このタイプは、配列と条件を受け取り、各アイテムを条件に照らして評価し、条件を満たしたアイテムの配列を返します。

### <a name="restrictions-for-foreach-and-until-actions"></a>foreach と until アクションの制限

`foreach` と `until` ループは、単一のアクションに制限されます。

### <a name="new-trackedproperties-for-actions"></a>アクションの新しい trackedProperties

アクションに `trackedProperties` というプロパティを (`runAfter` や `type` の兄弟として) 追加できるようになりました。 ワークフローの一環として出力される Azure 診断のテレメトリに含める特定のアクションの入力または出力を、このオブジェクトで指定します。 次に例を示します。

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>次のステップ

* [ロジック アプリのワークフロー定義の作成](../logic-apps/logic-apps-author-definitions.md)
* [ロジック アプリ デプロイを自動化する](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
