---
title: リモート監視ソリューションの UI にグリッドを追加する - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータの Web UI のページに新しいグリッドを追加する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 0eab7d743823109ed138ce19f35cea0ffb143422
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422727"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>リモート監視ソリューション アクセラレータの Web UI にカスタム グリッドを追加する

この記事では、リモート監視ソリューション アクセラレータの Web UI のページに新しいグリッドを追加する方法について説明します。 この記事では、次の内容について説明します。

- ローカルの開発環境を準備する方法。
- Web UI のページに新しいグリッドを追加する方法。

この記事で使用するグリッドの例では、「[Add a custom service to the Remote Monitoring solution accelerator web UI](iot-accelerators-remote-monitoring-customize-service.md)」(リモート監視ソリューション アクセラレータ Web UI にカスタム サービスを追加する) のハウツー記事で追加方法が説明されているサービスのデータが表示されます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を最後まで行うには、ローカル開発マシンに次のソフトウェアがインストールされている必要があります。

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>開始する前に

続ける前に、次の記事の手順を完了しておく必要があります。

- [リモート監視ソリューション アクセラレータの Web UI にカスタム ページを追加する](iot-accelerators-remote-monitoring-customize-page.md)。
- [リモート監視ソリューション アクセラレータの Web UI にカスタム サービスを追加する](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>グリッドの追加

Web UI にグリッドを追加するには、グリッドが定義されているソース ファイルを追加し、既存のファイルをいくつか変更して、Web UI で新しいコンポーネントが認識されるようにする必要があります。

### <a name="add-the-new-files-that-define-the-grid"></a>グリッドが定義されている新しいファイルを追加する

すぐに始められるように、**src/walkthrough/components/pages/pageWithGrid/exampleGrid** フォルダーにはグリッドを定義するファイルが用意されています。

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

**src/walkthrough/components/pages/pageWithGrid/exampleGrid** フォルダーを **src/components/pages/example** フォルダーにコピーします。

### <a name="add-the-grid-to-the-page"></a>グリッドをページに追加する

サービス定義をインポートするために、**src/components/pages/example/basicPage.container.js** を次のように変更します。

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

グリッドを追加するために、**src/components/pages/example/basicPage.js** を次のように変更します。

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

テストを更新するために、**src/components/pages/example/basicPage.test.js** を次のように変更します。

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>グリッドをテストする

まだ Web UI をローカルに実行していない場合は、リポジトリのローカル コピーのルートで次のコマンドを実行します。

```cmd/sh
npm start
```

前のコマンドは、`http://localhost:3000/dashboard` のローカルで UI を実行します。 **[例]** ページに移動して、サービスからのグリッド表示データを確認します。

## <a name="select-rows"></a>行を選択する

ユーザーがグリッドで行を選択できるようにするオプションが 2 つあります。

### <a name="hard-select-rows"></a>行をハード選択する

ユーザーが複数の行を同時に処理する必要がある場合は、行に対してチェックボックスを使用します。

1. 行のハード選択を有効にするには、グリッドに用意されている **columnDefs** に **checkboxColumn** を追加します。 **checkboxColumn** は **/src/components/shared/pcsGrid/pcsGrid.js** で定義されています。

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. 選択されている項目にアクセスするには、内部グリッド API への参照を取得します。

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. グリッドの行がハード選択されている場合にページにコンテキスト ボタンを表示します。

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. コンテキスト ボタンがクリックされると、作業対象のハード選択されている項目を取得します。

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>行をソフト選択する

ユーザーが単一の行に対して操作する必要がある場合は、**columnDefs** の 1 列または複数の列に対してソフト選択リンクを構成します。

1. **exampleGridConfig.js** で、**columnDef** の **cellRendererFramework** として **SoftSelectLinkRenderer** を追加します。

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. ソフト選択されたリンクがクリックされると、**onSoftSelectChange** イベントがトリガーされます。 詳細のポップアップを開くなど、その行に必要なアクションが実行されます。 この例では、単にコンソールに出力されます。

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>次のステップ

この記事では、リモート監視ソリューション アクセラレータの Web UI のページの追加またはカスタマイズに使用できるリソースについて説明しました。

グリッドを定義した後の次の手順は、例のページに表示される[リモート監視ソリューション アクセラレータ Web UI にカスタム ポップアップを追加する](iot-accelerators-remote-monitoring-customize-flyout.md)ことです。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページを参照してください。
