---
title: クイック スタート:Azure Static Web Apps を使用して静的 Web アプリを初めてビルドする
description: 好みのフロントエンド フレームワークを使用して Azure Static Web Apps インスタンスをビルドする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: bbc06b657525880f22bd5fb38e902f906d438c9c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565912"
---
# <a name="quickstart-building-your-first-static-web-app"></a>クイック スタート:静的 Web アプリを初めてビルドする

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このクイックスタートでは、任意のフロントエンド フレームワークを使用して GitHub リポジトリから Web アプリケーションをビルドします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント

## <a name="create-a-repository"></a>リポジトリを作成する

この記事では、GitHub テンプレート リポジトリを使用して、新しいリポジトリを簡単に作成できるようにします。 テンプレートでは、さまざまなフロントエンド フレームワークでビルドされたスターター アプリが使用されます。

# <a name="angular"></a>[Angular](#tab/angular)

- GitHub にログインしていることを確認し、次の場所に移動して新しいリポジトリを作成します
  - https://github.com/staticwebdev/angular-basic/generate
- リポジトリの名前を **my-first-static-web-app** に設定します

# <a name="react"></a>[React](#tab/react)

- GitHub にログインしていることを確認し、次の場所に移動して新しいリポジトリを作成します
  - https://github.com/staticwebdev/react-basic/generate
- リポジトリの名前を **my-first-static-web-app** に設定します

# <a name="vue"></a>[Vue](#tab/vue)

- GitHub にログインしていることを確認し、次の場所に移動して新しいリポジトリを作成します
  - https://github.com/staticwebdev/vue-basic/generate
- リポジトリの名前を **my-first-static-web-app** に設定します

# <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

- GitHub にログインしていることを確認し、次の場所に移動して新しいリポジトリを作成します
  - https://github.com/staticwebdev/vanilla-basic/generate
- リポジトリの名前を **my-first-static-web-app** に設定します

> [!NOTE]
> Azure Static Web Apps で Web アプリを作成するには、少なくとも 1 つの HTML ファイルが必要です。 このステップで作成するリポジトリには、1 つの _index.html_ ファイルが含まれます。

---

**[Create repository from template]\(テンプレートからリポジトリを作成する\)** ボタンをクリックします。

:::image type="content" source="media/getting-started/create-template.png" alt-text="テンプレートからリポジトリを作成する":::

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

リポジトリが作成されたので、Azure portal から静的 Web アプリを作成できます。

- [Azure Portal](https://portal.azure.com) に移動します
- **[リソースの作成]** をクリックします
- **[Static Web Apps]** を探します
- **[Static Web Apps (Preview)]\(Static Web Apps (プレビュー)\)** をクリックします
- **[作成]** をクリックします。

### <a name="basics"></a>基本

まず、新しいアプリを構成し、それを GitHub リポジトリにリンクします。

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="[基本] タブ":::

- お使いの "_Azure サブスクリプション_" を選択します
- "_リソース グループ_" を選択するか、新しく作成します
- アプリに **my-first-static-web-app** という名前を設定します。
  - 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。
- 最も近い "_リージョン_" を選択します
- **[Free]** _SKU_ を選択します
- **[GitHub でサインイン]** ボタンをクリックし、GitHub で認証します

GitHub にサインインしたら、リポジトリの情報を入力します。

:::image type="content" source="media/getting-started/repository-details.png" alt-text="リポジトリの詳細":::

- 希望する "_組織_" を選択します
- _[リポジトリ]_ ドロップダウンから **my-first-web-static-app** を選択します
- _[ブランチ]_ ドロップダウンから **[master]** を選択します
- **[次へ: ビルド >]** ボタンをクリックして、ビルド構成を編集します

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="[次へ: ビルド] ボタン":::

> [!NOTE]
>  リポジトリが表示されない場合は、GitHub で Azure Static Web Apps を承認しなければならない場合があります。 [GitHub のホームページ](https://github.com)を参照し、アカウントの画像をクリックしてドロップダウン メニューを開きます。 **[Settings]\(設定\)** 、 **[Applications]\(アプリケーション\) > [Authorized OAuth Apps]\(承認済み OAuth アプリ\) > [Azure Static Web Apps]** の順にクリックし、最後に **[Grant]\(付与\)** を選択します。 組織リポジトリの場合は、アクセス許可を付与する組織の所有者である必要があります。

### <a name="build"></a>ビルド

次に、使用するフロントエンド フレームワークに固有の構成の詳細を追加します。

# <a name="angular"></a>[Angular](#tab/angular)

- _[App location]\(アプリの場所\)_ ボックスに「 **/** 」と入力します
- _[Api location]\(Api の場所\)_ ボックスから既定値をクリアします
- _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「**dist/angular-basic**」と入力します

# <a name="react"></a>[React](#tab/react)

- _[App location]\(アプリの場所\)_ ボックスに「 **/** 」と入力します
- _[Api location]\(Api の場所\)_ ボックスから既定値をクリアします
- _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「**build**」と入力します

# <a name="vue"></a>[Vue](#tab/vue)

- _[App location]\(アプリの場所\)_ ボックスに「 **/** 」と入力します
- _[Api location]\(Api の場所\)_ ボックスから既定値をクリアします
- _[App artifact location]\(アプリ成果物の場所\)_ ボックスに「**dist**」と入力します

# <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

- _[App location]\(アプリの場所\)_ ボックスに「 **/** 」と入力します
- _[Api location]\(Api の場所\)_ ボックスから既定値をクリアします
- _[App artifact location]\(アプリ成果物の場所\)_ ボックスから既定値をクリアします

---

**[確認および作成]** ボタンを選択します。

:::image type="content" source="media/getting-started/review-create.png" alt-text="[確認および作成] ボタン":::

アプリを作成した後でこれらの値を変更するには、[ワークフロー ファイル](github-actions-workflow.md)を編集します。

### <a name="review--create"></a>確認と作成

要求が検証されたら、アプリケーションの作成を続行できます。

**[作成]** ボタンをクリックします

:::image type="content" source="media/getting-started/create-button.png" alt-text="[作成] ボタン":::

リソースが作成されたら、 **[リソースに移動]** ボタンをクリックします

:::image type="content" source="media/getting-started/resource-button.png" alt-text="[リソース グループ] ボタン":::

## <a name="view-the-website"></a>Web サイトを表示する

静的アプリのデプロイには 2 つの側面があります。 まず、アプリを構成する基になる Azure リソースをプロビジョニングします。 2 つ目は、アプリケーションをビルドして発行する GitHub Actions ワークフローです。

新しい静的サイトに移動する前にまず、デプロイ ビルドの実行が完了している必要があります。

Static Web Apps の概要ウィンドウには、Web アプリとの対話に役立つ一連のリンクが表示されます。

:::image type="content" source="media/getting-started/overview-window.png" alt-text="概要ウィンドウ":::

1. "Click here to check the status of your GitHub Actions runs" (こちらをクリックして、GitHub Actions の実行の状態を確認してください) というバナーをクリックすると、リポジトリに対して実行されている GitHub Actions が表示されます。 デプロイ ジョブが完了したことを確認したら、生成された URL を使用して Web サイトに移動できます。

2. GitHub Actions ワークフローが完了したら、 _[URL]_ リンクをクリックして、新しいタブで Web サイトを開くことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用して Azure Static Web Apps インスタンスを削除することができます。

1. [Azure portal](https://portal.azure.com) を開きます。
1. 上部の検索バーから **my-first-web-static-app** を検索します
1. アプリ名をクリックします
1. **[削除]** ボタンをクリックします
1. **[はい]** をクリックして削除操作を確定します

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
