---
title: Application Insights JavaScript SDK の React プラグイン
description: Application Insights JavaScript SDK 用の React プラグインをインストールして使用する方法。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 44554434eee51c11e7f89007c532f1a142fc998c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199341"
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

Application Insights への接続を初期化します。

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

高階コンポーネント関数を使用してコンポーネントをラップし、そのコンポーネントで Application Insights を有効にします。

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
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

## <a name="using-react-hooks"></a>React フックを使用する

[React フック](https://reactjs.org/docs/hooks-reference.html)では、クラスベースの React コンポーネントに依存することなく、React アプリケーションでの状態とライフサイクルを管理することができます。 Application Insights React プラグインには、高階コンポーネントのときと同様の方法で動作するフック統合がいくつか用意されています。

### <a name="using-react-context"></a>React Context を使用する

Application Insights の React フックは、[React Context](https://reactjs.org/docs/context.html) をその格納アスペクトとして使用するように設計されています。 Context を使用するには、前述のように Application Insights を初期化してから、Context オブジェクトをインポートします。

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

このコンテキスト プロバイダーにより、すべての子コンポーネント内で Application Insights が `useContext` フックとして使用できるようになります。

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

`useTrackMetric` フックでは、コンポーネント構造にコンポーネントを追加することなく、`withAITracking` 高階コンポーネントの機能がレプリケートされます。 フックは 2 つの引数を取ります。最初に Application Insights インスタンス (`useAppInsightsContext` フックから取得可能) を取り、次に追跡用のコンポーネントの識別子 (名前など) を取ります。

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

これは高階コンポーネントと同様に動作しますが、コンポーネントのライフサイクルではなく、フックのライフサイクル イベントに応答します。 特定の操作で実行する必要がある場合は、フックをユーザー イベントに明示的に指定する必要があります。

### `useTrackEvent`

`useTrackEvent` フックは、ボタン クリックやその他の API 呼び出しなど、アプリケーションで追跡が必要になる可能性があるカスタム イベントの追跡に使用されます。 4 つの引数を受け取ります。
-   Application Insights インスタンス (`useAppInsightsContext` フックから取得可能)。
-   イベントの名前。
-   追跡する必要がある変更をカプセル化するイベント データ オブジェクト。
-   初期化時の `trackEvent` 呼び出しをスキップするための skipFirstRun (省略可能) フラグ。 既定値は `true` に設定されます。

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

フックを使用すると、データ ペイロードをフックに提供して、Application Insights に格納されているイベントにデータを追加できます。

## <a name="react-error-boundaries"></a>React の Error Boundary

[Error Boundary](https://reactjs.org/docs/error-boundaries.html) では、React アプリケーション内で例外が発生した場合に適切に処理することができます。このようなエラーが発生すると、場合によっては例外をログに記録する必要があります。 Application Insights の React プラグインには、エラーが発生したときに自動的にログに記録する Error Boundary コンポーネントが用意されています。

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

`AppInsightsErrorBoundary` には 2 つのプロパティ、つまりアプリケーション用に作成された `ReactPlugin` インスタンスと、エラーが発生したときにレンダリングされるコンポーネントを渡す必要があります。 未処理のエラーが発生すると、Error Boundary に対して指定された情報を使用して `trackException` が呼び出され、`onError` コンポーネントが表示されます。

## <a name="sample-app"></a>サンプル アプリ

[Application Insights React デモ](https://github.com/Azure-Samples/application-insights-react-demo)を確認してください。

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights JavaScript SDK ドキュメント](javascript.md)を参照してください。
- Kusto クエリ言語と Log Analytics でのデータのクエリの詳細については、[ログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
