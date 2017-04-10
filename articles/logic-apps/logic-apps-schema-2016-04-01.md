---
title: "2016-06-01 スキーマへの更新 - Azure Logic Apps | Microsoft Docs"
description: "バージョン 2016-06-01 のスキーマで、Azure Logic Apps の JSON 定義を作成します。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dab219386a32f519e50f76e18013f8f94a2266ff
ms.openlocfilehash: 9d8f0be3d5c8e2c2e5f169dc1d0851c95a641d0c
ms.lasthandoff: 03/01/2017


---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure Logic Apps のスキーマの更新 - 2016 年 6 月 1 日

Azure Logic Apps の新しいバージョンのスキーマと API には、ロジック アプリの信頼性と使いやすさを向上させる複数の機能強化が行われています。

* [スコープ](#scopes)により、複数のアクションを一連のアクションとしてグループ化したり、入れ子したりできるようになりました。
* [条件とループ](#conditions-loops)がアクションに昇格しました。
* `runAfter` プロパティ (`dependsOn` の後継) でアクションの実行順序をより正確に定義できるようになりました。

ロジック アプリを 2015 年 8 月 1 日の preview スキーマから 2016 年 6 月 1 日のスキーマにアップグレードするには、[アップグレードに関するセクションを参照](#upgrading-to-2016-06-01-schema)してください。

<a name="scopes"></a>
## <a name="scopes"></a>スコープ

このスキーマにはスコープが含まれており、アクションをまとめてグループ化したり、入れ子にできます。 たとえば、1 つの条件に別の条件を含めることができます。 [スコープの構文](../logic-apps/logic-apps-loops-and-scopes.md)の詳細を確認するか、以下のコードで基本的なスコープの例を確認してください。

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
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

以前のバージョンのスキーマでは、条件とループはパラメーターとして単一のアクションに関連付けられていました。 このスキーマではその制限が廃止され、条件とループはアクションのタイプとして記述されます。 [ループとスコープ](../logic-apps/logic-apps-loops-and-scopes.md)の詳細を確認するか、以下のコードで基本的な条件アクションの例を確認してください。

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>
## <a name="runafter-property"></a>runAfter プロパティ

`dependsOn` プロパティが `runAfter` プロパティに置き換えられ、前のアクションの状態に基づいてアクションの実行順序をより正確に指定できるようになりました。

`dependsOn` プロパティは、"アクションの実行に成功した場合" という意味です。このため、直前のアクションの結果 (成功、失敗、スキップ) に基づいてアクションを実行する回数を変えたい場合には対応できません。 `runAfter` プロパティを使えば、オブジェクトの実行後のアクションをすべて名前で指定できるため、オブジェクトとしての柔軟性が大きくなります。 このプロパティでは、トリガーとして許容される状態の配列も定義します。 たとえば、ステップ A が成功し、かつ、ステップ B が成功または失敗した後でアクションを実行したい場合には、次のような `runAfter` プロパティを構成します。

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>スキーマのアップグレード

新しいスキーマへのアップグレードは、たった数回のステップで完了します。 アップグレード プロセスでは、アップグレード スクリプトを実行し、新しいロジック アプリを保存します。必要があれば、以前のロジック アプリの上書きも行います。

1. Azure Portal でロジック アプリを開きます。

2. **[概要]** に移動します。 ロジック アプリのツール バーで、**[スキーマの更新]** を選択します。
   
    ![[スキーマの更新] を選択][1]
   
    アップグレード後の定義が返されます。これは、必要に応じてコピーしてリソース定義に貼り付けることができます。 
    ただし、アップグレード後のロジック アプリで使われている接続の参照がすべて有効であることを確認するために、**[名前を付けて保存]** 
    を選択することを**強くお勧めします**。

3. アップグレードのブレードのツール バーで **[名前を付けて保存]** を選択します。

4. ロジックの名前と状態を入力します。 **[作成]** をクリックして、アップグレードしたロジック アプリをデプロイします。

5. アップグレードしたロジック アプリが正常に動作することを確認します。
   
   > [!NOTE]
   > 手動トリガーまたは要求トリガーを使用している場合、新しいロジック アプリではコールバック URL が変化します。 新しい URL をテストして、エンド ツー エンドのエクスペリエンスを確認してください。 以前の URL をそのまま維持する場合には、既存のロジック アプリを複製しておきます。

6. *(省略可能)* 以前のロジック アプリを新しいスキーマ バージョンで上書きするには、ツール バーの **[Clone (複製)]** (**[スキーマの更新]** の横) を使用します。 このステップは、ロジック アプリのリソース ID または要求トリガー URL をそのまま維持したい場合に必要となります。

### <a name="upgrade-tool-notes"></a>アップグレード ツールの注意事項

#### <a name="mapping-conditions"></a>条件のマッピング

アップグレード ツールでは、アップグレード後の定義で条件分岐アクションを可能な限り&1; つのスコープとしてグループ化しようとします。 具体的には、`@equals(actions('a').status, 'Skipped')` というデザイナー パターンが、`else` アクションとして表示されます。 ただし、認識できないパターンが検出された場合は、true の分岐と false の分岐について別個の条件が作成される可能性があります。 アップグレード後、必要に応じてアクションのマッピングを変更してください。

#### <a name="foreach-loop-with-condition"></a>条件判定を伴う foreach ループ

新しいスキーマでは、フィルター アクションを使用して、アイテム単位の条件判定を伴う `foreach` ループのパターンを再現できます。この変更は通常、アップグレード時に自動的に行われます。 このような条件は、foreach ループに先行するフィルター アクションに置き換えられます。つまり、このフィルター アクションによって条件に一致したアイテムのみの配列が取得され、foreach アクションに渡されます。 例については、[ループとスコープ](../logic-apps/logic-apps-loops-and-scopes.md)に関する記事を参照してください。

#### <a name="resource-tags"></a>リソース タグ

リソース タグはアップグレード後に削除されるので、アップグレード後のワークフローで設定し直す必要があります。

## <a name="other-changes"></a>その他の変更点

### <a name="renamed-manual-trigger-to-request-trigger"></a>manual トリガーの名前を request トリガーに変更

`manual` タイプのトリガーが廃止され、`http` タイプの `request` に名前が変更されました。 これにより、このトリガーを使用して構築されるパターンとの一貫性が向上しました。

### <a name="new-filter-action"></a>新しい 'フィルター' アクション

大きな配列のサイズを小さくして、より小規模な一連のアイテムにするには、`filter` という新しいタイプを使用します。このタイプは、配列と条件を受け取り、各アイテムを条件に照らして評価し、条件を満たしたアイテムの配列を返します。

### <a name="restrictions-for-foreach-and-until-actions"></a>foreach と until アクションの制限

`foreach` と `until` ループは、単一のアクションに制限されます。

### <a name="new-trackedproperties-for-actions"></a>アクションの新しい trackedProperties

アクションに `trackedProperties` というプロパティを (`runAfter` や `type` の兄弟として) 追加できるようになりました。 ワークフローの一環として出力される Azure 診断のテレメトリに含める特定のアクションの入力または出力を、このオブジェクトで指定します。 For example:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
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
* [ロジック アプリのデプロイ テンプレートの作成](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png

