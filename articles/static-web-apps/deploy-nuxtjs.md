---
title: チュートリアル:サーバーでレンダリングされた Nuxt.js の Web サイトを Azure Static Web Apps にデプロイする
description: Nuxt.js の動的サイトを生成し、Azure Static Web Apps を使用してデプロイします。
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563468"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>サーバーでレンダリングされた Nuxt.js の Web サイトを Azure Static Web Apps プレビューにデプロイする

このチュートリアルでは、[Nuxt.js](https://nuxtjs.org) で生成された静的 Web サイトを [Azure Static Web Apps](overview.md) にデプロイする方法について説明します。 まず、Nuxt.js アプリを設定、構成、デプロイする方法を学習します。 このプロセスの間に、Nuxt.js を使用して静的ページを生成するときによく発生する一般的な課題に対処する方法についても学習します

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- GitHub アカウント。 [無料でアカウントを作成できます](https://github.com/join)。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="set-up-a-nuxtjs-app"></a>Nuxt.js アプリを設定する

`create-nuxt-app` を使用して、新しい Nuxt.js プロジェクトを設定できます。 このチュートリアルでは、新しいプロジェクトではなく、既存のリポジトリを複製することで始めます。 このリポジトリは、動的 Nuxt.js アプリを静的サイトとしてデプロイする方法がわかるように設定されています。

1. お使いの GitHub アカウントに、テンプレート リポジトリから新しいリポジトリを作成します。
1. [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate) に移動します
1. リポジトリに **nuxtjs-starter** という名前を設定します
1. 次に、新しいリポジトリをお使いのコンピューターに複製します。 <YOUR_GITHUB_ACCOUNT_NAME> は、必ず自分のアカウント名に置き換えてください。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. 新しく複製された Nuxt.js アプリに移動します。

   ```bash
   cd nuxtjs-starter
   ```

1. 依存関係をインストールします。

    ```bash
    npm install
    ```

1. 開発環境で Nuxt.js アプリを起動します。

    ```bash
    npm run dev
    ```

`http://localhost:3000` に移動してアプリを開きます。いつも使用しているブラウザーで、次の Web サイトが開かれます。

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Nuxt.js アプリを開始する":::

フレームワークまたはライブラリをクリックすると、選択した項目に関する詳細ページが表示されます。

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="詳細ページ":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Nuxt.js のビルドから静的 Web サイトを生成する

`npm run build` を使用して Nuxt.js のサイトをビルドすると、アプリは静的サイトではなく、従来の Web アプリとしてビルドされます。 静的サイトを生成するには、次のアプリケーション構成を使用します。

1. `nuxt generate` コマンドを使用して静的サイトのみが生成されるように、_package.json_ のビルド スクリプトを更新します。

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    このコマンドを追加すると、コミットをプッシュするたびに Static Web Apps によって `build` スクリプトが実行されるようになります。

1. 静的サイトを生成します。

    ```bash
    npm run build
    ```

    Nuxt.js によって静的サイトが生成され、作業ディレクトリのルートにある _dist_ フォルダーにコピーされます。

    > [!NOTE]
    > このフォルダーは、デプロイ時に CI/CD によって生成される必要があるため、 _.gitignore_ ファイルにリストされています。

## <a name="push-your-static-website-to-github"></a>静的 Web サイトを GitHub にプッシュする

Azure Static Web Apps により、GitHub リポジトリからアプリがデプロイされます。これは、指定されたブランチにプッシュされるすべてのコミットに対し、常に行われます。 次のコマンドを使用して、変更を GitHub に同期します。

1. 変更されたすべてのファイルをステージします。

    ```bash
    git add .
    ```

1. すべての変更をコミットします

    ```bash
    git commit -m "Update build config"
    ```

1. 変更を GitHub にプッシュします。

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>静的 Web サイトをデプロイする

次の手順では、GitHub にプッシュしたアプリを Azure Static Web Apps にリンクする方法を示します。 Azure に移動すると、アプリケーションを運用環境にデプロイできます。

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Azure Static Web Apps プレビューのリソースを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** をクリックします
1. **[Static Web Apps]** を探します
1. **[Static Web Apps (Preview)]\(Static Web Apps (プレビュー)\)** をクリックします
1. **[作成]**

1. *[サブスクリプション]* ドロップダウンの一覧からサブスクリプションを選択するか、既定値を使用します。
1. *[リソース グループ]* ドロップダウンの下にある **[新規]** リンクをクリックします。 *[新しいリソース グループ名]* に「**mystaticsite**」と入力して、 **[OK]** をクリックします
1. アプリのグローバルに一意の名前を **[名前]** テキスト ボックスに入力します。 有効な文字は、`a-z`、`A-Z`、`0-9`、`-` です。 この値は、静的アプリの URL プレフィックスとして `https://<APP_NAME>.azurestaticapps.net` の形式で使用されます。
1. *[リージョン]* ドロップダウンで、最も近いリージョンを選択します。
1. [SKU] ドロップダウンから **[Free]** を選択します。

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="静的 Web アプリを作成する":::

### <a name="add-a-github-repository"></a>GitHub リポジトリを追加する

新しい Static Web Apps アカウントは、コミットを自動的にデプロイできるように、Nuxt.js アプリでリポジトリにアクセスする必要があります。

1. **[GitHub でサインイン]** ボタンをクリックします
1. Nuxt.js プロジェクト用のリポジトリを作成した **[組織]** を選択します。これは、GitHub のユーザー名である場合があります。
1. 前に作成したリポジトリの名前を探して選択します。
1. *[ブランチ]* ドロップダウンから、ブランチとして **main** を選択します。

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="GitHub に接続する":::

### <a name="configure-the-build-process"></a>ビルド プロセスを構成する

Azure Static Web Apps は、npm モジュールのインストールや、各デプロイ中の `npm run build` の実行など、一般的なタスクを自動的に実行するように作成されています。 ただし、アプリケーション ソース フォルダーやビルド先フォルダーなど、手動で構成する必要のある設定がいくつかあります。

1. **[ビルド]** タブをクリックして、静的な出力フォルダーを構成します。

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="[ビルド] タブ":::

1. *[App artifact location]\(アプリ成果物の場所\)* テキスト ボックスに「**dist**」と入力します。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。
1. **[作成]** をクリックして、リソースの作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。
1. デプロイが完了したら、 **[リソースに移動]** をクリックします
1. _[概要]_ ウィンドウで、 *[URL]* リンクをクリックして、デプロイしたアプリケーションを開きます。 

Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。

リポジトリの Actions に移動することで、Actions ワークフローの状態を確認できます。

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>変更を同期する

アプリを作成すると、Azure Static Web Apps によってリポジトリに GitHub Actions ワークフロー ファイルが作成されます。 git 履歴が同期されるように、このファイルをローカル リポジトリに取り込む必要があります。

ターミナルに戻り、`git pull origin main` コマンドを実行します。

## <a name="configure-dynamic-routes"></a>動的なルートを構成する

新しくデプロイされたサイトに移動し、フレームワークまたはライブラリのロゴの 1 つをクリックします。 詳細ページは表示されず、代わりに 404 エラー ページが表示されます。

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="動的ルートでの 404":::

その理由は、Nuxt.js ではホーム ページに対してのみ静的サイトが生成されているためです。 Nuxt.js では、すべての `.vue` ページ ファイルに対して同等の静的 `.html` ファイルを生成できますが、1 つ例外があります。 

ページが `_id.vue` などの動的ページの場合、そのような動的ページから静的 HTML を生成するのに十分な情報がありません。 動的ルートに使用できるパスを明示的に指定する必要があります。

## <a name="generate-static-pages-from-dynamic-routes"></a>動的ルートから静的ページを生成する

1. _nuxt.config.js_ ファイルを更新し、Nuxt.js が使用可能なすべてのデータの一覧を使用して、各フレームワークまたはライブラリの静的ページを生成できるようにします。

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` は非同期関数であるため、この関数で API に対する要求を行い、返された一覧を使用してパスを生成できます。

2. 新しい変更を GitHub リポジトリにプッシュし、GitHub Actions によってサイトが再度ビルドされるまで数分待ちます。 ビルドが完了すると、404 エラーは表示されなくなります。

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="修正された動的ルートでの 404":::

> [!div class="nextstepaction"]
> [カスタム ドメインの設定](custom-domain.md)
