---
title: リモート監視ソリューションの UI にパネルを追加する - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータの Web UI のダッシュボードに新しいパネルを追加する方法について示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447065"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>リモート監視ソリューション アクセラレータの Web UI のダッシュボードにカスタム パネルを追加する

この記事では、リモート監視ソリューション アクセラレータの Web UI のダッシュボード ページに新しいパネルを追加する方法について示します。 この記事では、次の内容について説明します。

- ローカルの開発環境を準備する方法。
- Web UI のダッシュボード ページに新しいパネルを追加する方法。

この記事のパネルの例は、既存のダッシュボード ページに表示されます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を最後まで行うには、ローカル開発マシンに次のソフトウェアがインストールされている必要があります。

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始する前に

続行する前に、「[リモート監視ソリューション アクセラレータの Web UI にカスタム ページを追加する](iot-accelerators-remote-monitoring-customize-page.md)」の記事に記載されている手順を完了する必要があります。

## <a name="add-a-panel"></a>パネルの追加

Web UI にパネルを追加するには、パネルが定義されているソース ファイルを追加し、パネルを表示するようにダッシュボードを変更する必要があります。

### <a name="add-the-new-files-that-define-the-panel"></a>パネルが定義されている新しいファイルを追加する

作業を始められるよう、**src/walkthrough/components/pages/dashboard/panels/examplePanel** フォルダーにパネルを定義した次のようなファイルが含まれています。

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

**src/walkthrough/components/pages/dashboard/panels/examplePanel** フォルダーを **src/components/pages/dashboard/panels** フォルダーにコピーします。

次のエクスポートを **src/walkthrough/components/pages/dashboard/panels/index.js** ファイルに追加します。

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>パネルをダッシュボードに追加する

**src/components/pages/dashboard/dashboard.js** を変更してパネルを追加します。

パネルからのインポートの一覧にパネルの例を追加します。

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

次のセルの定義をページ コンテンツのグリッドに追加します。

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>ポップアップをテストする

まだ Web UI をローカルに実行していない場合は、リポジトリのローカル コピーのルートで次のコマンドを実行します。

```cmd/sh
npm start
```

前のコマンドは、ローカルの [http://localhost:3000/dashboard](http://localhost:3000/dashboard) で UI を実行します。 新しいパネルを表示するには、 **[ダッシュボード]** ページに移動します。

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のダッシュボードの追加またはカスタマイズに使用できるリソースについて説明しました。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページを参照してください。
