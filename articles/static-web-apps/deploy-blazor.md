---
title: チュートリアル:Azure Static Web Apps での Blazor を使用した静的 Web アプリのビルド
description: Blazor を使用して Azure Static Web Apps Web サイトをビルドする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350230"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>チュートリアル:Azure Static Web Apps での Blazor を使用した静的 Web アプリのビルド

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このチュートリアルでは、Azure portal を使用して、Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント

## <a name="application-overview"></a>アプリケーションの概要

Azure Static Web Apps を使用して、サーバーレス バックエンドでサポートされる静的 Web アプリケーションを作成できます。 次のチュートリアルに、気象データを返す C# Blazor Web アプリケーションをデプロイする方法が示されてます。

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="完全な Blazor アプリ":::

このチュートリアルで紹介するアプリは、3 つの異なる Visual Studio プロジェクトから構成されています。

- **Api**:静的アプリに気象情報を提供する API エンドポイントを実装する C# Azure Functions アプリケーション。 **WeatherForecastFunction** は、`WeatherForecast` オブジェクトの配列を返します。

- **クライアント**: フロントエンド Blazor Web アセンブリ プロジェクト。 すべてのルートで _index.html_ ファイルが確実に処理されるように、[フォールバック ルート](#fallback-route)が実装されます。

- **共有**:Api プロジェクトと Client プロジェクトの両方のプロジェクトによって参照される共通クラスを保持します。これにより、データを API エンドポイントからフロントエンド Web アプリにフローさせることができます。 両方のアプリで、[`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) クラスが共有されます。

これらのプロジェクトがまとまって、API バックエンドによってサポートされているブラウザーで実行される Blazor Web アセンブリ アプリケーションを作成するために必要な部分が構成されます。

## <a name="fallback-route"></a>フォールバック ルート

アプリケーションでは、アプリケーションの特定のルートにマップされる _/counter_ や _/fetchdata_ などの URL が公開されます。 このアプリは単一ページ アプリケーションとして実装されるため、各ルートで _index.html_ ファイルが使用されます。 任意のパスに対する要求で _index.html_ が確実に返されるようにするため、Client プロジェクトの _wwwroot_ フォルダーにある _routes.json_ ファイルに [フォールバック ルート](./routes.md#fallback-routes)が実装されます。

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

上記の構成により、アプリ内の任意のルートへの要求で、_index.html_ ページが確実に返されます。

## <a name="create-a-repository"></a>リポジトリを作成する

この記事では、簡単に作業を開始できるように、GitHub テンプレート リポジトリを使用します。 テンプレートには、Azure Static Web Apps にデプロイされるスターター アプリが含まれます。

1. GitHub にサインインしていることを確認し、次の場所に移動して新しいリポジトリを作成します。
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. リポジトリの名前を **my-first-static-blazor-app** に設定します

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

リポジトリが作成されたので、Azure portal から静的 Web アプリを作成します。

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** を選択します
1. **[Static Web Apps]** を探します
1. **[Static Web Apps (プレビュー)]** を選択します
1. **[作成]**

_[基本]_ セクションで、新しいアプリを構成し、それを GitHub リポジトリにリンクすることから始めます。

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="[基本] タブ":::

1. お使いの "_Azure サブスクリプション_" を選択します
1. "_リソース グループ_" を選択するか、新しく作成します
1. アプリに **my-first-static-blazor-app** という名前を付けます。
    - 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。
1. 最も近い "_リージョン_" を選択します
1. **[Free]** _SKU_ を選択します
1. **[GitHub アカウントでサインイン]** ボタンを選択し、GitHub で認証します

GitHub にサインインした後、リポジトリ情報を入力します。

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="リポジトリの詳細":::

1. 希望する "_組織_" を選択します
1. _[リポジトリ]_ ドロップダウンから **[my-first-static-blazor-app]** を選択します。
1. _[ブランチ]_ ドロップダウンから **[main]** を選択します

    リポジトリが表示されない場合は、GitHub で Azure Static Web Apps を承認しなければならない場合があります。 GitHub リポジトリを参照し、 **[設定]、[アプリケーション]、[認可された OAuth アプリ]** の順に移動して、 **[Azure Static Web Apps]** 、 **[許可]** の順に選択します。 組織リポジトリの場合は、アクセス許可を付与する組織の所有者である必要があります。

1. _[ビルドの詳細]_ セクションで、Blazor 固有の構成の詳細を追加します。

    - _[ビルドのプリセット]_ ドロップダウンから **[Blazor]** を選択し、すべての既定値を保持します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="[確認および作成] ボタン":::

1. **［作成］** を選択します

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="[作成] ボタン":::

1. **[リソースに移動]** を選択します。

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="[リソース グループ] ボタン":::

## <a name="view-the-website"></a>Web サイトを表示する

静的アプリのデプロイには 2 つの側面があります。 まず、アプリを構成する基になる Azure リソースをプロビジョニングします。 2 つ目は、アプリケーションをビルドして発行する GitHub Actions ワークフローです。

新しい静的サイトに移動する前にまず、デプロイ ビルドの実行が完了している必要があります。

Static Web Apps の概要ウィンドウには、Web アプリとの対話に役立つ一連のリンクが表示されます。

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="概要ウィンドウ":::

1. "_Click here to check the status of your GitHub Actions runs (こちらをクリックして、GitHub Actions の実行の状態を確認してください)_ " というバナーをクリックすると、リポジトリに対して実行されている GitHub アクションが表示されます。 デプロイ ジョブが完了したことを確認したら、生成された URL を使用して Web サイトに移動できます。

2. GitHub Actions ワークフローが完了したら、 _[URL]_ リンクを選択して、新しいタブで Web サイトを開くことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用して Azure Static Web Apps インスタンスを削除することができます。

1. [Azure portal](https://portal.azure.com) を開きます。
1. 上部の検索バーから **my-first-static-blazor-app** を検索します。
1. アプリ名を選択します
1. **[削除]** ボタンを選択します
1. **[はい]** を選択して、削除操作を確定します

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と権限承認](./authentication-authorization.md)
