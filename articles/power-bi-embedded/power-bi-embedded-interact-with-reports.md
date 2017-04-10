---
title: "JavaScript API を使ったレポートの対話操作 | Microsoft Docs"
description: "Power BI Embedded は、JavaScript API を使用してレポートと対話します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: 28eddb52c33d9883219f146480b110574f728f89


---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>JavaScript API を使った Power BI レポートの対話操作
Power BI JavaScript API を使用すると、Power BI レポートをアプリケーションに簡単に埋め込むことができます。 この API により、アプリケーションは、ページやフィルターなどのさまざまなレポート要素とプログラムで対話できます。 この対話機能のおかげで、Power BI レポートは、アプリケーションへの組み込みが強化されます。

Power BI レポートをアプリケーションに埋め込むには、アプリケーションの一部としてホストされている iframe を使用します。 iframe は、次の画像でわかるように、アプリケーションとレポートの間の境界として機能します。 

![Power BI embedded iframe without Javascript API](media/powerbi-embedded-interact-with-reports/powerbi-embedded-interact-report-1.png)

iframe により、埋め込み処理は非常に簡単になりますが、JavaScript API がなければ、レポートとアプリケーションは相互に対話できません。 このように対話が欠如することで、レポートが実際にはアプリケーションの一部ではないように感じる場合があります。 レポートとアプリケーションは、次の画像のように、実際には相互に通信する必要があります。

![Power BI embedded iframe with Javascript API](media/powerbi-embedded-interact-with-reports/powerbi-embedded-interact-report-2.png)

Power BI JavaScript API を使用すると、iframe の境界を安全に通過するコードを記述することができます。 これにより、アプリケーションは、レポートでの操作をプログラムで実行したり、ユーザーがレポート内で行った操作からのイベントをリッスンしたりできます。

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Power BI JavaScript API でできること
JavaScript API を使用すると、レポートの管理、レポート内でのページの移動、レポートのフィルター処理、埋め込みイベントの処理を実行できます。 次の図は、API の構造を示します。

![Power BI JavaScript API diagram](media/powerbi-embedded-interact-with-reports/powerbi-embedded-interact-report-3.png)

### <a name="manage-reports"></a>レポートの管理
Javascript API を使用すると、レポート レベルおよびページ レベルで動作を管理できます。

* 特定の Power BI レポートを安全にアプリケーションに埋め込む - [埋め込みのデモ アプリケーション](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * アクセス トークンを設定する
* レポートを構成する
  * フィルター ウィンドウとページ ナビゲーション ウィンドウを有効または無効にする - [設定の更新のデモ アプリケーション](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * ページとフィルターの既定値を設定する - [既定値の設定のデモ](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* 全画面表示モードを開始および終了する

[レポートの埋め込みの詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>レポート内でのページの移動
JavaScript API を使用すると、レポート内のすべてのページを探索したり、現在のページを設定したりできます。 [ナビゲーションのデモ アプリケーション](http://azure-samples.github.io/powerbi-angular-client/#/scenario3)を試してください。

[ページ ナビゲーションの詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>レポートのフィルター処理
JavaScript API には、埋め込みレポートとレポート ページ用の基本フィルター処理機能と高度なフィルター処理機能が用意されています。 [フィルター処理のデモ アプリケーション](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)を試し、ここにある入門的なコードを確認してください。  

#### <a name="basic-filters"></a>基本フィルター
基本フィルターは、列または階層レベルに配置され、含める値または除外する値の一覧を含みます。

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
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
* 指定値を含む
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
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
* レポート
  * pageChanged
  * dataSelected (近日対応予定)

[イベント処理の詳細についてはこちらをご覧ください](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>次のステップ
Power BI JavaScript API の詳細については、次のリンク先を確認してください。

* [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [オブジェクト モデル リファレンス](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* サンプル
  * [Angular](http://azure-samples.github.io/powerbi-angular-client)
  * [Ember](https://github.com/Microsoft/powerbi-ember)
* [ライブ デモ](https://microsoft.github.io/PowerBI-JavaScript/demo/)




<!--HONumber=Jan17_HO1-->


