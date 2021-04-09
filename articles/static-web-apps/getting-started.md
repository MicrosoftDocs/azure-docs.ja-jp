---
title: 'クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する'
description: Azure Static Web Apps に静的サイトをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 2d7a2dcbbd0b6e9a10ca8af93798bfddbee94ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182657"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>クイックスタート: Azure Static Web Apps を使用して静的サイトを初めて構築する

Azure Static Web Apps では、GitHub リポジトリからアプリをビルドすることによって、運用環境に Web サイトが発行されます。 このクイックスタートでは、Visual Studio Code 拡張機能を使用して、Web アプリケーションを Azure Static Web Apps にデプロイします。

Azure サブスクリプションを持っていない場合は、[無料試用版アカウントを作成できます](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>前提条件

- [GitHub](https://github.com) アカウント
- [Azure](https://portal.azure.com) アカウント
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code 用 Azure Static Web Apps 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git をインストールする](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

次に、Visual Studio Code を開き、 **[ファイル] > [フォルダーを開く]** に移動して、お使いのマシンにクローンしたリポジトリをエディターで開きます。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. Visual Studio Code 内で、アクティビティ バーの Azure ロゴを選択して、Azure 拡張機能ウィンドウを開きます。

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure ロゴ":::

    > [!NOTE]
    > Azure と GitHub へのサインインが必要です。 Visual Studio Code から Azure と GitHub にまだサインインしていない場合は、拡張機能により、作成プロセス中に両方にサインインするように求められます。

1. マウス ポインターを _[Static Web Apps]_ ラベルの上に置き、**プラス記号** を選択します。

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="アプリケーション名":::

1. エディターの上部にコマンド パレットが表示され、アプリケーションの名前を入力するように求められます。

    「**my-first-static-web-app**」と入力し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="静的 Web アプリを作成する":::

1. **main** ブランチを選択して **Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="ブランチ名":::

1. アプリケーション コードの場所として **/** を選択し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="アプリケーション コードの場所":::

1. 拡張機能により、アプリケーション内の API の場所の入力が求められます。 この記事では API を実装しません。

    **[Skip for now]\(後で確認する\)** を選択し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API の場所":::

1. アプリで運用環境用にファイルを構築する場所を選択します。

    # <a name="no-framework"></a>[フレームワークなし](#tab/vanilla-javascript)

    ボックスをクリアし、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="アプリ ファイルのパス":::

    # <a name="angular"></a>[Angular](#tab/angular)

    「**dist/angular-basic**」と入力し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Angular アプリのファイル パス":::

    # <a name="react"></a>[React](#tab/react)

    「**build**」と入力し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="React アプリのファイル パス":::

    # <a name="vue"></a>[Vue](#tab/vue)

    「**dist**」と入力し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue アプリのファイル パス":::

    ---

1. 最も近い場所を選択し、**Enter** キーを押します。

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="リソースの場所":::

1. アプリが作成されると、確認通知が Visual Studio Code に表示されます。

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="作成された確認":::

1. Visual Studio Code エクスプローラー ウィンドウで、自分のサブスクリプション名が表示されているノードに移動して展開します。 デプロイが完了するまでに数分かかる場合があるので注意してください。 その後、[Static Web Apps] セクションに戻り、アプリの名前を選択して、[my-first-static-web-app] を右クリックし、[ポータルで開く] を選択すると、Azure portal でアプリが表示されます。

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="ポータルを開く":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、拡張機能を使用して Azure Static Web Apps インスタンスを削除することができます。

Visual Studio Code エクスプローラー ウィンドウで、 _[Static Web Apps]_ セクションに戻ります。 **[my-first-static-web-app]** を右クリックし、 **[削除]** を選択します。

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="アプリの削除":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
