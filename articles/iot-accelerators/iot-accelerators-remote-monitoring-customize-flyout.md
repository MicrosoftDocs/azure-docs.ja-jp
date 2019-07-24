---
title: リモート監視ソリューションの UI にポップアップを追加する - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータの Web UI のページに新しいポップアップを追加する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167428"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>リモート監視ソリューション アクセラレータの Web UI にカスタム ポップアップを追加する

この記事では、リモート監視ソリューション アクセラレータの Web UI のページに新しいポップアップを追加する方法を示します。 この記事では、次の内容について説明します。

- ローカル開発環境を準備する方法。
- Web UI のページに新しいポップアップを追加する方法。

この記事で使用するポップアップの例では、「[Add a custom grid to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-grid.md)」(リモート監視ソリューション アクセラレータの Web UI にカスタム グリッドを追加する) ハウツー記事で追加方法が示されているグリッドがページに表示されます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を最後まで行うには、ローカル開発マシンに次のソフトウェアがインストールされている必要があります。

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始する前に

続ける前に、次の記事の手順を完了しておく必要があります。

- [リモート監視ソリューション アクセラレータの Web UI にカスタム ページを追加する](iot-accelerators-remote-monitoring-customize-page.md)。
- [リモート監視ソリューション アクセラレータの Web UI にカスタム サービスを追加する](iot-accelerators-remote-monitoring-customize-service.md)
- [リモート監視ソリューション アクセラレータの Web UI にカスタム グリッドを追加する](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>ポップアップの追加

Web UI にポップアップを追加するには、ポップアップが定義されているソース ファイルを追加し、既存のファイルをいくつか変更して、Web UI で新しいコンポーネントが認識されるようにする必要があります。

### <a name="add-the-new-files-that-define-the-flyout"></a>ポップアップが定義されている新しいファイルを追加する

作業を始められるよう、**src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** フォルダーにポップアップを定義したファイルが格納されています。

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

**src/walkthrough/components/pages/pageWithFlyout/flyouts** フォルダーを **src/components/pages/example** フォルダーにコピーします。

### <a name="add-the-flyout-to-the-page"></a>ポップアップをページに追加する

ポップアップを追加するには、**src/components/pages/example/basicPage.js** を変更します。

**components/shared** からのインポートに **Btn** を追加し、**svgs** と **ExampleFlyoutContainer** のインポートを追加します。

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

**closedFlyoutState** の **const** 定義を追加し、それをコンストラクターの state に追加します。

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

**BasicPage** クラスに、次の関数を追加します。

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

次の **const** 定義を、**render** 関数に追加します。

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

ポップアップを開くボタンをコンテキスト メニューに追加します。

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

いくつかのテキストとポップアップのコンテナーを、ページのコンテンツに追加します。

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>ポップアップをテストする

まだ Web UI をローカルに実行していない場合は、リポジトリのローカル コピーのルートで次のコマンドを実行します。

```cmd/sh
npm start
```

前のコマンドでは、UI が [http://localhost:3000/dashboard](http://localhost:3000/dashboard) でローカルに実行されます。 **[Example]\(例\)** ページに移動して、**[Open Flyout]\(ポップアップを開く\)** をクリックします。

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のページの追加またはカスタマイズに使用できるリソースについて説明しました。

ページにポップアップを定義したので、次に[リモート監視ソリューション アクセラレータ Web UI のダッシュボードにパネルを追加](iot-accelerators-remote-monitoring-customize-panel.md)します。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページを参照してください。
