---
title: チュートリアル:VuePress サイトを Azure Static Web Apps に発行する
description: このチュートリアルでは、VuePress アプリケーションを Azure Static Web Apps にデプロイする方法について説明します。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650450"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>チュートリアル:VuePress サイトを Azure Static Web Apps プレビューに発行する

この記事では、[VuePress](https://vuepress.vuejs.org/) Web アプリケーションを作成して [Azure Azure Static Web Apps](overview.md) にデプロイする方法を説明します。 最終結果として、アプリの構築と発行の方法を制御できる新しい Azure Static Web Apps アプリケーションおよび関連する GitHub Actions が得られます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - VuePress アプリの作成
> - Azure Static Web Apps のセットアップ
> - VuePress アプリの Azure へのデプロイ

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="create-a-vuepress-app"></a>VuePress アプリの作成

コマンド ライン インターフェイス (CLI) で VuePress アプリを作成します。

1. VuePress アプリ用の新しいフォルダーを作成します。

   ```bash
   mkdir static-site
   ```

1. _README.md_ ファイルをフォルダーに追加します。

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. _package.json_ ファイルを初期化します。

   ```bash
   npm init -y
   ```

1. VuePress を `devDependency` として追加します。

   ```bash
   npm install --save-dev vuepress
   ```

1. テキスト エディターで _package.json_ ファイルを開き、build コマンドを [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) セクションに追加します。

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. _.gitignore_ ファイルを作成し、_node\_modules_ フォルダーを除外します。

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Git リポジトリを初期化します。

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションの GitHub へのプッシュ

Azure Static Web Apps に接続するには、GitHub のリポジトリが必要です。 次の手順では、サイトのリポジトリの作成方法を説明します。

1. [https://github.com/new](https://github.com/new) で **vuepress-static-app** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

次の手順では、新しい Static Web Apps アプリケーションを作成し、運用環境にデプロイする方法について説明します。

### <a name="create-the-application"></a>アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** をクリックします
1. **Static Web Apps** を検索します
1. **[Static Web Apps (Preview)]\(Static Web Apps (プレビュー)\)** をクリックします
1. **[作成]** をクリックします。

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="ポータルでの Static Web Apps (プレビュー) の作成":::

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。

1. _[リソース グループ]_ で、 **[新規]** を選択します。 _[新しいリソース グループ名]_ に「**vuepress-static-app**」と入力し、 **[OK]** を選択します。

1. 次に、 **[名前]** ボックスに対象のアプリの名前を入力します。 有効な文字には、`a-z`、`A-Z`、`0-9`、および `-` があります。

1. _[リージョン]_ で、近くの使用可能なリージョンを選択します。

1. _[SKU]_ で、 **[Free]** を選択します。

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="詳細情報の入力":::

1. **[GitHub でサインイン]** ボタンをクリックします。

1. リポジトリを作成した **[組織]** を選択します。

1. **vuepress-static-app** を _[リポジトリ]_ として選択します。

1. _[ブランチ]_ では、**main** を選択します。

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="入力済みの GitHub 情報":::

### <a name="build"></a>Build

次に、ビルド プロセスがアプリのビルドに使用する構成設定を追加します。 次の設定では、GitHub アクション ワークフロー ファイルが構成されます。

1. **[次へ:ビルド >]** ボタンをクリックして、ビルド構成を編集します。

1. _[App location]\(アプリの場所\)_ に **/** を設定します。

1. _[App artifact location]\(アプリ成果物の場所\)_ に **.vuepress/dist** を設定します。

この時点では API をデプロイしていないため _[API location]\(アプリの場所\)_ の値は必要ありません。

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="ビルド設定":::

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。

1. **[作成]** をクリックして、Azure Static Web Apps の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. リソース画面で、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。 GitHub アクションが完了するまで 1 - 2 分かかることがあります。

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="デプロイされたアプリケーション":::

### <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
