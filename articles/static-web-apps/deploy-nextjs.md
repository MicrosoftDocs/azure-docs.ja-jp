---
title: チュートリアル:静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps にデプロイする
description: Next.js の動的サイトを生成し、Azure Static Web Apps を使用してデプロイします。
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: a22d06137c3ec17851280605ac85c94ef8b342cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563077"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps プレビューにデプロイする

このチュートリアルでは、[Next.js](https://nextjs.org) で生成された静的 Web サイトを [Azure Static Web Apps](overview.md) にデプロイする方法について説明します。 まず、Next.js アプリを設定、構成、デプロイする方法を学習します。 このプロセスの間に、Next.js を使用して静的ページを生成するときによく発生する一般的な課題に対処する方法についても学習します

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- GitHub アカウント。 [無料でアカウントを作成できます](https://github.com/join)。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="set-up-a-nextjs-app"></a>Next.js アプリを設定する

Next.js CLI を使用してアプリを作成するのではなく、既存の Next.js アプリを含むスターター リポジトリを使用できます。 このリポジトリには動的ルートを使用する Next.js アプリが用意されており、デプロイに関する一般的な問題が明らかになっています。 動的ルートには、後で詳しく説明する追加のデプロイ構成が必要です。

最初に、お使いの GitHub アカウントに、テンプレート リポジトリから新しいリポジトリを作成します。

1. [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate) に移動します
1. リポジトリに **nextjs-starter** という名前を設定します
1. 次に、新しいリポジトリをお使いのコンピューターに複製します。 `<YOUR_GITHUB_ACCOUNT_NAME>` をアカウント名に置き換えます。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. 新しく複製された Next.js アプリに移動します。

   ```bash
   cd nextjs-starter
   ```

1. 依存関係をインストールします。

    ```bash
    npm install
    ```

1. 開発環境で Next.js アプリを起動します。

    ```bash
    npm run dev
    ```

`http://localhost:3000` に移動してアプリを開きます。いつも使用しているブラウザーで、次の Web サイトが開かれます。

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Next.js アプリを開始する":::

フレームワークまたはライブラリをクリックすると、選択した項目に関する詳細ページが表示されます。

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="詳細ページ":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Next.js のビルドから静的 Web サイトを生成する

`npm run build` を使用して Next.js のサイトをビルドすると、アプリは静的サイトではなく、従来の Web アプリとしてビルドされます。 静的サイトを生成するには、次のアプリケーション構成を使用します。

1. 静的ルートを構成するには、プロジェクトのルートに _next.config.js_ という名前のファイルを作成し、次のコードを追加します。

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      この構成では、`/` ルートに対して提供され、_pages/index.js_ ページ ファイルである Next.js ページに、`/` がマップされます。

1. ビルドの後 `next export` コマンドを使用して静的サイトも生成されるように、_package.json_ のビルド スクリプトを更新します。 `export` コマンドにより、静的サイトが生成されます。

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    このコマンドを追加すると、コミットをプッシュするたびに Static Web Apps によって `build` スクリプトが実行されるようになります。

1. 静的サイトを生成します。

    ```bash
    npm run build
    ```

    静的サイトが生成され、作業ディレクトリのルートにある _out_ フォルダーにコピーされます。

    > [!NOTE]
    > このフォルダーは、デプロイ時に CI/CD によって生成される必要があるため、 _.gitignore_ ファイルにリストされています。

## <a name="push-your-static-website-to-github"></a>静的 Web サイトを GitHub にプッシュする

Azure Static Web Apps により、GitHub リポジトリからアプリがデプロイされます。これは、指定されたブランチにプッシュされるすべてのコミットに対し、常に行われます。 次のコマンドを使用して、変更を GitHub に同期します。

1. 変更されたすべてのファイルをステージします

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

### <a name="create-a-static-app"></a>静的アプリを作成する

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

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="静的 Web アプリを作成する":::

### <a name="add-a-github-repository"></a>GitHub リポジトリを追加する

新しい Static Web Apps アカウントは、コミットを自動的にデプロイできるように、Next.js アプリでリポジトリにアクセスする必要があります。

1. **[GitHub でサインイン]** ボタンをクリックします
1. Next.js プロジェクト用のリポジトリを作成した **[組織]** を選択します。これは、GitHub のユーザー名である場合があります。
1. 前に作成したリポジトリの名前を探して選択します。
1. *[ブランチ]* ドロップダウンから、ブランチとして **main** を選択します。

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="GitHub に接続する":::

### <a name="configure-the-build-process"></a>ビルド プロセスを構成する

Azure Static Web Apps は、npm モジュールのインストールや、各デプロイ中の `npm run build` の実行など、一般的なタスクを自動的に実行するように作成されています。 ただし、アプリケーション ソース フォルダーやビルド先フォルダーなど、手動で構成する必要のある設定がいくつかあります。

1. **[ビルド]** タブをクリックして、静的な出力フォルダーを構成します。

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="[ビルド] タブ":::

2. *[App artifact location]\(アプリ成果物の場所\)* テキスト ボックスに「**out**」と入力します。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。
1. **[作成]** をクリックして、リソースの作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。
1. デプロイが完了したら、 **[リソースに移動]** をクリックします
1. _[概要]_ ウィンドウで、 *[URL]* リンクをクリックして、デプロイしたアプリケーションを開きます。 

Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。
Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。

リポジトリの Actions に移動することで、Actions ワークフローの状態を確認できます。

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>変更を同期する

アプリを作成すると、Azure Static Web Apps によってリポジトリに GitHub Actions ワークフロー ファイルが作成されます。 git 履歴が同期されるように、このファイルをローカル リポジトリに取り込む必要があります。

ターミナルに戻り、`git pull origin main` コマンドを実行します。

## <a name="configure-dynamic-routes"></a>動的なルートを構成する

新しくデプロイされたサイトに移動し、フレームワークまたはライブラリのロゴの 1 つをクリックします。 詳細ページは表示されず、代わりに 404 エラー ページが表示されます。

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="動的ルートでの 404":::

このエラーが発生するのは、Next.js ではアプリケーションの構成に基づいてホーム ページだけが生成されるためです。

## <a name="generate-static-pages-from-dynamic-routes"></a>動的ルートから静的ページを生成する

1. _next.config.js_ ファイルを更新し、Next.js が使用可能なすべてのデータの一覧を使用して、各フレームワークまたはライブラリの静的ページを生成できるようにします。

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap` 関数は非同期関数であるため、この関数で API に対する要求を行い、返された一覧を使用してパスを生成できます。

2. 新しい変更を GitHub リポジトリにプッシュし、GitHub Actions によってサイトが再度ビルドされるまで数分待ちます。 ビルドが完了すると、404 エラーは表示されなくなります。

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="修正された動的ルートでの 404":::

> [!div class="nextstepaction"]
> [カスタム ドメインの設定](custom-domain.md)
