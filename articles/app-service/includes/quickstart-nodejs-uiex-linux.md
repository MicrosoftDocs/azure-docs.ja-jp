---
title: 'クイックスタート: Node.js Web アプリを作成する - Linux'
description: Azure App Service に、初めての Node.js の Hello World を数分でデプロイします。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109145"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3.Visual Studio Code から Azure App Service にデプロイする

1. Visual Studio Code でアプリケーション フォルダーを開きます。

    ```bash
    code .
    ```

1. **AZURE APP SERVICE** エクスプローラーで、 **[Azure にサインイン]** を選択し、指示に従います。 サインインすると、エクスプローラーにご使用の Azure サブスクリプションの名前が表示されます。

    ![Azure にサインインする](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>Azure サインインのトラブルシューティング</summary>
    
    Azure にサインインするときに **"Cannot find subscription with name [subscription ID] (サブスクリプション名 [サブスクリプション ID] が見つかりません)"** というエラーが表示される場合、原因としては、プロキシの内側にいるために、Azure API に到達できないことが考えられます。 ご利用のターミナルで `export` を使用して、自分のプロキシ情報で `HTTP_PROXY` と `HTTPS_PROXY` の環境変数を構成してください。
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [問題を報告する](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. **Azure App Service** エクスプローラーで青色の上矢印アイコンを選択して、自分のアプリを Azure にデプロイします。 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="選択されている青い矢印アイコンを示している、VS Code での Azure App Service のスクリーンショット。":::

1. 現在開いているディレクトリ (`nodejs-docs-hello-world`) を選択します。

1. **[Create new Web App]\(新しい Web アプリの作成\)** を選択します。既定のデプロイ先は App Service on Linux になります。

1. グローバルに一意になるように入力します <abbr title="アプリ名に使用できる有効な文字は "a-z"、"0-9"、"-" です。">name</abbr> (Web アプリ用)。その後、**Enter** キーを押します。 

1. 該当する **Node.js バージョン** を選択します (LTS を推奨)。

    自分のアプリ用に作成されている Azure リソースが通知チャネルに表示されます。

1. ターゲット サーバーで `npm install` を実行するように構成を更新するよう求められたら、 **[はい]** を選択します。 その後、アプリがデプロイされます。

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="[はい] ボタンが選択されている、ターゲット サーバー上の構成を更新するためのプロンプトのスクリーンショット。":::

1. 以後のデプロイのターゲットが自動的に同じ App Service Web アプリとなるように自分のワークスペースを更新するように求められたら、 **[はい]** を選択します。 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="[はい] ボタンが選択されている、ワークスペースを更新するためのプロンプトのスクリーンショット。":::




1. デプロイが完了したら、プロンプトで **[Web サイトの参照]** を選択して、新しくデプロイした Web アプリを表示します。

<br/>
<details>
<summary><strong>トラブルシューティング</strong></summary>

これらの手順を完了できない場合は、次のことを確認してください。

* 必ず、PORT 環境変数 (`process.env.PORT`) に指定されたポートで自分のアプリケーションがリッスンするようにしてください。

* **"このディレクトリまたはページを表示するアクセス許可がありません"** というエラーが表示された場合、おそらくアプリケーションが正常に起動できていません。 ログ出力を確認し、エラーを探して修正してください。 

</details>

<br>

[問題を報告する](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>


