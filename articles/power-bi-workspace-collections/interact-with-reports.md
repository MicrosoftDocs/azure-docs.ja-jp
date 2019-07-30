---
title: JavaScript API を使ったレポートの対話操作 | Microsoft Docs
description: Power BI JavaScript API を使用すると、Power BI レポートをアプリケーションに簡単に埋め込むことができます。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8203070fa8a48420dd192208701c539dc3ca7f25
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669075"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>JavaScript API を使った Power BI レポートの対話操作

Power BI JavaScript API を使用すると、Power BI レポートをアプリケーションに簡単に埋め込むことができます。 この API により、アプリケーションは、ページやフィルターなどのさまざまなレポート要素とプログラムで対話できます。 この対話機能のおかげで、Power BI レポートは、アプリケーションへの組み込みが強化されます。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

Power BI レポートをアプリケーションに埋め込むには、アプリケーションの一部としてホストされている iframe を使用します。 iframe は、次の画像でわかるように、アプリケーションとレポートの間の境界として機能します。

![Power BI ワークスペース コレクションの iframe (Javascript API なし)](media/interact-with-reports/iframe-without-javacript.png)

iframe により、埋め込み処理は非常に簡単になりますが、JavaScript API がなければ、レポートとアプリケーションは相互に対話できません。 このように対話が欠如することで、レポートが実際にはアプリケーションの一部ではないように感じる場合があります。 レポートとアプリケーションは、次の画像のように、実際には相互に通信する必要があります。

![Power BI ワークスペース コレクションの iframe (Javascript API あり)](media/interact-with-reports/iframe-with-javascript.png)

Power BI JavaScript API を使用すると、iframe の境界を安全に通過するコードを記述することができます。 これにより、アプリケーションは、レポートでの操作をプログラムで実行したり、ユーザーがレポート内で行った操作からのイベントをリッスンしたりできます。

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Power BI JavaScript API でできること

JavaScript API を使用すると、レポートの管理、レポート内でのページの移動、レポートのフィルター処理、埋め込みイベントの処理を実行できます。 次の図は、API の構造を示します。

![Power BI JavaScript API diagram](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>レポートの管理
Javascript API を使用すると、レポート レベルおよびページ レベルで動作を管理できます。

* 特定の Power BI レポートを安全にアプリケーションに埋め込む - [埋め込みのデモ アプリケーション](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * アクセス トークンを設定する
* レポートを構成する
  * フィルター ウィンドウとページ ナビゲーション ウィンドウを有効または無効にする - [設定の更新のデモ アプリケーション](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * ページとフィルターの既定値を設定する - [既定値の設定のデモ](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* 全画面表示モードを開始および終了する

[レポートの埋め込みの詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>レポート内でのページの移動
JavaScript API を使用すると、レポート内のすべてのページを探索したり、現在のページを設定したりできます。 [ナビゲーションのデモ アプリケーション](https://azure-samples.github.io/powerbi-angular-client/#/scenario3)を試してください。

[ページ ナビゲーションの詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>レポートのフィルター処理
JavaScript API には、埋め込みレポートとレポート ページ用の基本フィルター処理機能と高度なフィルター処理機能が用意されています。 [フィルター処理のデモ アプリケーション](https://azure-samples.github.io/powerbi-angular-client/#/scenario4)を試し、ここにある入門的なコードを確認してください。

#### <a name="basic-filters"></a>基本フィルター
基本フィルターは、列または階層レベルに配置され、含める値または除外する値の一覧を含みます。

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>高度なフィルター
高度なフィルターでは、論理演算子 AND または OR を使用し、(それぞれに独自の演算子と値を指定した) 1 つまたは 2 つの条件を受け入れます。 サポートされている条件は次のとおりです。

* なし
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[フィルター処理の詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>イベントの処理

アプリケーションは、情報を iframe に送信する以外に、次のイベントに関する情報を iframe から受信することもできます。

* 埋め込み
  * loaded
  * error
* Reports
  * pageChanged
  * dataSelected (近日対応予定)

[イベント処理の詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>次の手順

Power BI JavaScript API の詳細については、次のリンク先を確認してください。

* [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [オブジェクト モデル リファレンス](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [ライブ デモ](https://microsoft.github.io/PowerBI-JavaScript/demo/)