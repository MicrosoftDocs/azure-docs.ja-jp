---
title: 'クイックスタート: CLI を使用して Azure Static Web Apps で最初の静的サイトを構築する'
description: Azure CLI を使用して、静的サイトを Azure Static Web Apps にデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 98b40218d168133193410174bcc158ab8698c381
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225389"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>クイックスタート: Azure CLI を使用して最初の静的サイトを構築する

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このクイックスタートでは、Azure CLI を使用して、Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [GitHub の個人用アクセス トークン](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) アカウント
- [Azure CLI](/cli/azure/install-azure-cli) のインストール (バージョン 2.8.0 以上)

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

    ```azurecli
    az login
    ```

1. リポジトリから新しい静的 Web アプリを作成します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > `s` パラメーターに渡す URL には、`.git` サフィックスを含めることはできません。

    - `<RESOURCE_GROUP_NAME>`: この値を既存の [Azure リソース グループ名](../azure-resource-manager/management/manage-resources-cli.md)に置き換えます。

      - リソース グループを一覧表示する方法の詳細については、[az group](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_list) のドキュメントを参照してください。

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

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
