---
title: Application Insights JavaScript SDK の React プラグイン
description: Application Insights JavaScript SDK 用の React プラグインをインストールして使用する方法。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226920"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK の React プラグイン

Application Insights JavaScript SDK 用の React プラグインを使用すると、次が有効になります。

- ルート変更の追跡
- React コンポーネントの使用状況に関する統計情報

## <a name="getting-started"></a>作業の開始

npm パッケージをインストールします。

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>基本的な使用方法

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>構成

| 名前    | Default | 説明                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | React ルーターの履歴 詳細については、[React ルーター パッケージに関するドキュメント](https://reactrouter.com/web/api/history)を参照してください。 コンポーネントの外部にある履歴オブジェクトにアクセスする方法については、[React ルーターに関する FAQ](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components) を参照してください。    |

### <a name="react-components-usage-tracking"></a>React コンポーネントの使用状況の追跡

さまざまな React コンポーネントの使用状況の追跡をインストルメント化するには、高階コンポーネント関数 `withAITracking` を適用します。

これによって `ComponentDidMount` イベントから `ComponentWillUnmount` イベントまでの時間が計測されます。 ただし、これをより正確にするために、ユーザーがアイドルであった時間が差し引かれます (`React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`)。

Azure portal でこのメトリックを表示するには、Application Insights リソースに移動し、[メトリック] タブを選択し、カスタム メトリック名 "React Component Engaged Time (秒)" を表示する空のグラフを構成し、メトリックの集計 (合計、平均など) を選択し、"Component Name" での分割を適用する必要があります。

![カスタム メトリック "React Component Engaged Time (秒)" を "Component Name" で分割して表示するグラフのスクリーンショット](./media/javascript-react-plugin/chart.png)

また、カスタム クエリを実行して Application Insights データを分類すれば、自分の要件に合わせてレポートと視覚エフェクトを生成することもできます。 Azure portal で、Application Insights リソースに移動し、[概要] タブの上部のメニューから [分析] を選択して、クエリを実行します。

![カスタム メトリック クエリの結果のスクリーンショット。](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 新しいカスタム メトリックが Azure portal に表示されるまでに最大で 10 分かかることがあります。

## <a name="sample-app"></a>サンプル アプリ

[Application Insights React デモ](https://github.com/Azure-Samples/application-insights-react-demo)を確認してください。

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights JavaScript SDK ドキュメント](javascript.md)を参照してください。
- Kusto クエリ言語と Log Analytics でのデータのクエリの詳細については、[ログ クエリの概要](../../azure-monitor/log-query/log-query-overview.md)に関するページを参照してください。
