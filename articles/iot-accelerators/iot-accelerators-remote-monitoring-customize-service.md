---
title: リモート監視ソリューションの UI にサービスを追加する - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータの Web UI に新しいサービスを追加する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447048"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>リモート監視ソリューション アクセラレータの Web UI にカスタム サービスを追加する

この記事では、リモート監視ソリューション アクセラレータの Web UI に新しいサービスを追加する方法について説明します。 この記事では、次の内容について説明します。

- ローカルの開発環境を準備する方法。
- Web UI に新しいサービスを追加する方法。

この記事で使用するサービスの例では、「[Add a custom grid to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-grid.md)」(リモート監視ソリューション アクセラレータ Web UI にカスタム グリッドを追加する) のハウツー記事で追加方法が説明されているグリッドのデータを提供します。

通常、React アプリケーションでは、サービスはバックエンド サービスと対話します。 リモート監視ソリューション アクセラレータの例として、IoT ハブ マネージャーや構成マイクロサービスと対話するサービスなどがあります。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を最後まで行うには、ローカル開発マシンに次のソフトウェアがインストールされている必要があります。

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始する前に

続行する前に、「[Add a custom page to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-page.md)」(リモート監視ソリューション アクセラレータ Web UI にカスタム ページを追加する) のハウツー記事に記載されている手順を完了してください。

## <a name="add-a-service"></a>サービスの追加

Web UI にサービスを追加するには、サービスが定義されているソース ファイルを追加し、既存のファイルをいくつか変更して、Web UI で新しいサービスが認識されるようにする必要があります。

### <a name="add-the-new-files-that-define-the-service"></a>サービスが定義されている新しいファイルを追加する

すぐに始められるように、**src/walkthrough/services** フォルダーには単純なサービスを定義するファイルが用意されています。

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

サービスの実装方法の詳細については、「[The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)」(不足しているリアクティブ プログラミングの紹介) を参照してください。

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

**exampleService.js** を **src/services** フォルダーにコピーし、**exampleModels.js** を **src/services/models** フォルダーにコピーします。

**src/services** フォルダーの **index.js** ファイルを更新して、新しいサービスをエクスポートします。

```js
export * from './exampleService';
```

**src/services/models** フォルダーの **index.js** ファイルを更新して、新しいモデルをエクスポートします。

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>ストアからサービスへの呼び出しを設定する

すぐに始められるように、**src/walkthrough/store/reducers** フォルダーにはサンプル レジューサーが用意されています。

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

**exampleReducer.js** を **src/store/reducers** フォルダーにコピーします。

レジューサーと**エピック**の詳細については、[redux-observable](https://redux-observable.js.org/) に関するページを参照してください。

### <a name="configure-the-middleware"></a>ミドルウェアを構成する

ミドルウェアを構成するには、**src/store** フォルダーの **rootReducer.js** ファイルにレジューサーを追加します。

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

エピックを **src/store** フォルダーの **rootEpics.js** ファイルに追加します。

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のサービスの追加またはカスタマイズに使用できるリソースについて説明しました。

サービスを定義した後の次の手順は、サービスから返されるデータを表示する[リモート監視ソリューション アクセラレータ Web UI にカスタム グリッドを追加する](iot-accelerators-remote-monitoring-customize-grid.md)ことです。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページを参照してください。
