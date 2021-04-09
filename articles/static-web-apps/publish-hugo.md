---
title: チュートリアル:Hugo サイトを Azure Static Web Apps に発行する
description: Azure Static Web Apps に Hugo アプリケーションをデプロイする方法について説明します。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652340"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>チュートリアル:Hugo サイトを Azure Static Web Apps プレビューに発行する

この記事では、[Hugo](https://gohugo.io/) Web アプリケーションを作成して [Azure Static Web Apps](overview.md) にデプロイする方法を説明します。 最終結果は、アプリのビルドと発行の方法を制御できる GitHub Actions が関連付けられた新しい Azure Static Web App になります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Hugo アプリを作成する
> - Azure Static Web Apps をセットアップする
> - Hugo アプリを Azure にデプロイする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。

## <a name="create-a-hugo-app"></a>Hugo アプリを作成する

Hugo コマンド ライン インターフェイス (CLI) を使用して Hugo アプリを作成します。

1. お使いの OS での Hugo の[インストール ガイド](https://gohugo.io/getting-started/installing/)に従ってください。

1. ターミナルを開きます

1. Hugo CLI を実行して新しいアプリを作成します。

   ```bash
   hugo new site static-app
   ```

1. 新しく作成されたアプリに移動します。

   ```bash
   cd static-app
   ```

1. Git リポジトリを初期化します。

   ```bash
    git init
   ```

1. 次に、git サブモジュールとしてテーマをインストールしてから、Hugo 構成ファイルでそれを指定することにより、サイトにテーマを追加します。

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 変更をコミットします。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションを GitHub にプッシュする

Azure Static Web Apps に接続するには、GitHub のリポジトリが必要です。 次の手順では、サイトのリポジトリの作成方法を説明します。

1. [https://github.com/new](https://github.com/new) で **hugo-static-app** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

次の手順では、新しい静的サイト アプリを作成し、運用環境にデプロイする方法について説明します。

### <a name="create-the-application"></a>アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** をクリックします
1. **Static Web Apps** を検索します
1. **[Static Web Apps (Preview)]\(Static Web Apps (プレビュー)\)** をクリックします
1. **[作成]** をクリックします。

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="ポータルで Azure Static Web Apps リソースを作成する":::

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。

1. _[リソース グループ]_ で、 **[新規]** を選択します。 "_新しいリソース グループ名_" として「**hugo-static-app**」と入力し、 **[OK]** を選択します。

1. 次に、 **[名前]** ボックスに対象のアプリの名前を入力します。 有効な文字には、`a-z`、`A-Z`、`0-9`、および `-` があります。

1. _[リージョン]_ で、近くの使用可能なリージョンを選択します。

1. _[SKU]_ で、 **[Free]** を選択します。

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="詳細情報の入力":::

1. **[GitHub でサインイン]** ボタンをクリックします。

1. リポジトリを作成した **[組織]** を選択します。

1. _[リポジトリ]_ として **hugo-static-app** を選択します。

1. _[ブランチ]_ では、**main** を選択します。

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="入力済みの GitHub 情報":::

### <a name="build"></a>Build

次に、ビルド プロセスがアプリのビルドに使用する構成設定を追加します。 次の設定では、GitHub アクション ワークフロー ファイルが構成されます。

1. **[次へ:ビルド >]** ボタンをクリックして、ビルド構成を編集します。

1. _[App location]\(アプリの場所\)_ を「 **/** 」に設定します。

1. _[App artifact location]\(アプリ成果物の場所\)_ を「**public**」に設定します。

   この時点では API をデプロイしていないため _[API location]\(アプリの場所\)_ の値は必要ありません。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。

1. **[作成]** をクリックして、Azure Static Web Apps の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. GitHub アクションが完了するのを待ちます。

1. Azure portal で新しく作成された Azure Static Web Apps リソースの _[概要]_ ウィンドウに移動し、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="デプロイされたアプリケーション":::

#### <a name="custom-hugo-version"></a>カスタム Hugo バージョン

静的 Web アプリを生成すると、アプリケーションの発行構成設定を含む[ワークフロー ファイル](./github-actions-workflow.md)が生成されます。 `env` セクション内に `HUGO_VERSION` の値を指定することにより、ワークフロー ファイル内に特定の Hugo バージョンを指定できます。 次の構成例は、Hugo を特定のバージョンに設定する方法を示しています。

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
