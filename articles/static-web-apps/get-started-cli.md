---
title: クイック スタート:Azure CLI で Azure Static Web Apps を使用して静的 Web アプリを初めてビルドする
description: Azure CLI を使用して Azure Static Web Apps インスタンスをビルドする方法について学習します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 7e0fdbc50dd36e4ea23903a5929735c1c83bd394
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752870"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して静的 Web アプリを初めてビルドする

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このクイックスタートでは、Azure CLI を使用して、Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [GitHub の個人用アクセス トークン](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) アカウント
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) のインストール (バージョン 2.8.0 以上)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

次に、次のコマンドを使用して、新しいフォルダーに切り替えます。

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

リポジトリが作成されたので、Azure CLI から静的 Web アプリを作成できます。

> [!IMPORTANT]
> ターミナルで _my-first-static-web-app_ フォルダー内で作業していることを確認してください。

1. 次のコマンドを使用して、Azure CLI にサインインします。

    ```bash
    az login
    ```

1. リポジトリから新しい静的 Web アプリを作成します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`:この値を既存の Azure リソース グループ名に置き換えます。

    - `<YOUR_GITHUB_ACCOUNT_NAME>`:この値を自分の GitHub ユーザー名に置き換えてください。

    - `<LOCATION>`:この値を最も近い場所に置き換えます。 次のオプションがあります。_CentralUS_、_EastAsia_、_EastUS2_、_WestEurope_、および _WestUS2_。

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`:この値を、以前に生成した [GitHub 個人用アクセス トークン](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)に置き換えます。

    これで、作成したアプリを Azure で表示できるようになりました。

1. [Azure portal](https://portal.azure.com) を開きます。

1. 上部の検索バーから **my-first-web-static-app** を検索します。

1. **[my-first-web-static-app]** を選択します。

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次のコマンドを実行して Azure Static Web Apps インスタンスを削除することができます。

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
