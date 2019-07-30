---
title: リモート監視ソリューションの UI にページを追加する - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータの Web UI に新しいページを追加する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447082"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>リモート監視ソリューション アクセラレータの Web UI にカスタム ページを追加する

この記事では、リモート監視ソリューション アクセラレータの Web UI に新しいページを追加する方法について説明します。 この記事では、次の内容について説明します。

- ローカルの開発環境を準備する方法。
- Web UI に新しいページを追加する方法。

その他のハウツーガイドでこのシナリオを拡張し、追加するページにさらに機能を追加します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を最後まで行うには、ローカル開発マシンに次のソフトウェアがインストールされている必要があります。

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>UI のローカル開発環境を準備する

リモート監視ソリューション アクセラレータ UI のコードは、[React](https://reactjs.org/) JavaScript フレームワークを使用して実装されます。 [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub リポジトリでソース コードを見つけることができます。

UI を変更してテストするには、ローカルの開発マシンで実行することができます。 必要に応じて、ローカル コピーをソリューション アクセラレータのデプロイ済みインスタンスに接続して、実際のデバイスまたはシミュレートされたデバイスと対話することができます。

ローカル開発環境を準備するには、Git を使用して [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) リポジトリをローカル マシンに複製します。

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>ページの追加

Web UI にページを追加するには、ページが定義されているソース ファイルを追加し、既存のファイルをいくつか変更して、Web UI で新しいページが認識されるようにする必要があります。

### <a name="add-the-new-files-that-define-the-page"></a>ページが定義されている新しいファイルを追加する

**src/walkthrough/components/pages/basicPage** 内にある単純なページを定義する 4 つのファイルから始めます。

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

新しいフォルダー **src/components/pages/example** を作成し、これら 4 つのファイルをコピーします。

### <a name="add-the-new-page-to-the-web-ui"></a>Web UI に新しいページを追加する

新しいページを Web UI に追加するには、既存のファイルを次のように変更します。

1. 新しいページ コンテナーを **src/components/pages/index.js** ファイルに追加します。

    ```js
    export * from './example/basicPage.container';
    ```

1. (省略可能) 新しいページの SVG アイコンを追加します。 詳細については、[webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md) を参照してください。 既存の SVG ファイルを使用できます。

1. ページ名を翻訳ファイル **public/locales/en/translations.json** に追加します。 Web UI の国際化には [i18next](https://www.i18next.com/) が使用されます。

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. 最上位のアプリケーション ページを定義する **src/components/app.js** ファイルを開きます。 インポートの一覧に新しいページを追加します。

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. 同じファイル内で、新しいページを `pagesConfig` 配列に追加します。 ルートの `to` アドレスを設定し、以前に追加した SVG アイコンと翻訳を参照し、`component` をページのコンテナーに設定します。

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. すべての新しい階層リンクを `crumbsConfig` 配列に追加します。

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    この例のページには階層リンクが 1 つしかありませんが、ページによっては複数存在する場合があります。

すべての変更を保存します。 新しいページが追加された Web UI を実行する準備が整いました。

### <a name="test-the-new-page"></a>新しいページをテストする

コマンド プロンプトでリポジトリのローカル コピーのルートに移動し、次のコマンドを実行して必要なライブラリをインストールし、Web UI をローカルで実行します。

```cmd/sh
npm install
npm start
```

前のコマンドは、ローカルの [http://localhost:3000/dashboard](http://localhost:3000/dashboard) で UI を実行します。

Web UI のローカル インスタンスをソリューション アクセラレータのデプロイ済みインスタンスに接続していない場合、ダッシュボードにエラーが表示されます。 このようなエラーは、新しいページをテストする機能に影響しません。

サイトがローカルで実行されている間にコードを編集し、Web UI の更新を動的に表示できるようになりました。

## <a name="optional-connect-to-deployed-instance"></a>[省略可能] デプロイ済みインスタンスに接続する

必要に応じて、Web UI のローカルで実行されているコピーをクラウドの Remote Monitoring ソリューション アクセラレータに接続することができます。

1. **pcs** CLI を使用して、ソリューション アクセラレータの**基本**インスタンスをデプロイします。 デプロイの名前と仮想マシンに提供した資格情報をメモしておきます。 詳しくは、[CLI を使用したデプロイ](iot-accelerators-remote-monitoring-deploy-cli.md)に関するページをご覧ください。

1. Azure portal または [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、ソリューションでマイクロサービスをホストする仮想マシンへの SSH アクセスを有効にします。 例:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    テストと開発時にのみ、SSH アクセスを有効にします。 SSH を有効にした場合、[できるだけ早く無効にする必要があります](../security/azure-security-network-security-best-practices.md)。

1. Azure Portal または [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して、仮想マシンの名前とパブリック IP アドレスを検索します。 例:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH を使用して、前の手順で取得した IP アドレスと、ソリューションをデプロイするために、**pcs** を実行したときに提供した資格情報を使用して、仮想マシンに接続します。

1. ローカル UX で接続できるようにするには、仮想マシンの bash シェルで、次のコマンドを実行します。

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. コマンドが完了し、Web サイトが起動したことを確認したら、仮想マシンから切断できます。

1. [Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) リポジトリのローカル コピーで、 **.env** ファイルを編集し、デプロイ済みのソリューションの URL を追加します。

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のカスタマイズに役立つように使用できるリソースについて説明します。

ページを定義した後の次の手順は、UI に表示するデータを取得する[リモート監視ソリューション アクセラレータ Web UI にカスタム サービスを追加する](iot-accelerators-remote-monitoring-customize-service.md)ことです。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページを参照してください。
