---
title: Application Insights JavaScript SDK の Native React プラグイン
description: Application Insights JavaScript SDK の Native React プラグインをインストールして使用する方法について説明します。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593601"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK の Native React プラグイン

Application Insights JavaScript SDK 用の Native React プラグインでは、デバイス情報が収集されます。既定では、このプラグインによって、以下が収集されます。

- **一意のデバイス ID** (インストール ID とも呼ばれます)。
- **デバイス モデル名** (iPhone X、Samsung Galaxy Fold、Huawei P30 Pro など)
- **デバイスの種類** (たとえば、受話器やタブレットなど)

## <a name="requirements"></a>必要条件

`@microsoft/applicationinsights-web` のバージョン 2.0.0 以上を使用する必要があります。 このプラグインは、react-native アプリでのみ機能します。 これは、[Expo フレームワークを使用するアプリ](https://docs.expo.io/)では動作しないため、[React Native の作成] でも動作しません。

## <a name="getting-started"></a>作業の開始

[react-native-device-info](https://www.npmjs.com/package/react-native-device-info) パッケージをインストールしてリンクします。 自分のアプリを使用して最新のデバイス名を収集するには、`react-native-device-info` パッケージを最新の状態に保ちます。

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>プラグインの初期化

このプラグインを使用するには、プラグインを構築し、既存の Application Insights インスタンスに `extension` として追加する必要があります。

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights JavaScript SDK ドキュメント](javascript.md)を参照してください。
- Kusto クエリ言語と Log Analytics でのデータのクエリの詳細については、[ログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
