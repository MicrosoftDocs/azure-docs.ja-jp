---
title: チュートリアル:Jekyll サイトを Azure Static Web Apps に公開する
description: Azure Static Web Apps に Jekyll アプリケーションをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99585179"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>チュートリアル:Jekyll サイトを Azure Static Web Apps プレビューに公開する

この記事では、[Jekyll](https://jekyllrb.com/) Web アプリケーションを作成して [Azure Static Web Apps](overview.md) にデプロイする方法を説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Jekyll Web サイトを作成する
> - Azure Static Web Apps をセットアップする
> - Jekyll アプリを Azure にデプロイする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Jekyll](https://jekyllrb.com/docs/installation/) のインストール
  - 必要に応じて Linux 用 Windows サブシステムを使用し、Ubuntu の手順に従うこともできます。
- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。

## <a name="create-jekyll-app"></a>Jekyll アプリを作成する

Jekyll コマンド ライン インターフェイス (CLI) を使用して Jekyll アプリを作成します。

1. ターミナルから Jekyll CLI を実行し、新しいアプリを作成します。

   ```bash
   jekyll new static-app
   ```

1. 新しく作成されたアプリに移動します。

   ```bash
   cd static-app
   ```

1. 新しい Git リポジトリを初期化します。

   ```bash
    git init
   ```

1. 変更をコミットします。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションを GitHub にプッシュする

Azure Static Web Apps では、Web サイトの公開に GitHub を使用します。 次の手順は、GitHub リポジトリの作成方法を説明したものです。

1. [https://github.com/new](https://github.com/new) で **jekyll-azure-static** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Git ブランチは、`main` とは異なる名前になっている可能性があります。 このコマンドの `main` を正しい値で置き換えてください。

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

次の手順では、新しい静的サイト アプリを作成し、運用環境にデプロイする方法について説明します。

### <a name="create-the-application"></a>アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) に移動します。

1. **[リソースの作成]** をクリックします。

1. **Static Web Apps** を検索します。

1. **[Static Web Apps (プレビュー)]** をクリックします。

1. **Create** をクリックしてください。

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。

1. _[リソース グループ]_ で、 **[新規]** を選択します。 _[新しいリソース グループ名]_ に「**jekyll-static-app**」と入力し、 **[OK]** を選択します。

1. 次に、 _[名前]_ ボックスに対象のアプリの名前を入力します。 有効な文字には、`a-z`、`A-Z`、`0-9`、および `-` があります。

1. _[リージョン]_ で、近くの使用可能なリージョンを選択します。

1. _[SKU]_ で、 **[Free]** を選択します。

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="詳細情報の入力":::

1. **[GitHub でサインイン]** ボタンをクリックします。

1. リポジトリを作成した **[組織]** を選択します。

1. _[リポジトリ]_ として **jekyll-static-app** を選択します。

1. _[ブランチ]_ では、**main** を選択します。

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="入力済みの GitHub 情報":::

### <a name="build"></a>Build

次に、ビルド プロセスがアプリのビルドに使用する構成設定を追加します。 次の設定では、GitHub アクション ワークフロー ファイルが構成されます。

1. **[次へ:ビルド >]** をクリックして、ビルド構成を編集します。

1. _[App location]\(アプリの場所\)_ を **/_site** に設定します。

1. _[App artifact location]\(アプリ成果物の場所\)_ を空白のままにします。

   この時点では API をデプロイしていないため _[API location]\(アプリの場所\)_ の値は必要ありません。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。

1. **[作成]** をクリックして、Azure Static Web Apps の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. 最初のデプロイは失敗します。これは、ワークフロー ファイルに Jekyll 固有の設定が必要であるためです。 この設定を追加するには、ターミナルに移動し、GitHub アクションを使用してマシンにコミットをプルします。

   ```bash
   git pull
   ```

1. Jekyll アプリをテキスト エディターで開き、 _.github/workflows/azure-pages-<ワークフロー名>.yml_ ファイルを開きます。

1. 行 `- name: Build And Deploy` の前に次の構成ブロックを追加します。

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. 更新されたワークフローをコミットし、GitHub にプッシュします。

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. GitHub アクションが完了するのを待ちます。

1. Azure portal の _[概要]_ ウィンドウで _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="デプロイされたアプリケーション":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
