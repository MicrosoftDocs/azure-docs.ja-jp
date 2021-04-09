---
title: Azure Static Web Apps のアプリケーション設定を構成する
description: Azure Static Web Apps のアプリケーション設定を構成する方法について説明します
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 2886cb6433c4f8a65eb0661eded0ddd08248e137
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219184"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Azure Static Web Apps (プレビュー) のアプリケーション設定を構成する

アプリケーション設定には、データベース接続文字列など、変更される可能性のある値の構成設定が保持されます。 アプリケーション設定を追加すると、アプリケーション コードを変更せずに、アプリへの構成入力を変更できます。

アプリケーション設定は次のようになります。

- 保存時に暗号化される
- [ステージング](review-publish-pull-requests.md)環境と運用環境にコピーされる

アプリケーション設定は、環境変数と呼ばれることもあります。

> [!IMPORTANT]
> この記事で説明しているアプリケーション設定は、Azure Static Web App のバックエンド API にのみ適用されます。
>
> フロントエンド Web アプリケーションでの環境変数の使用については、[JavaScript フレームワーク](#javascript-frameworks-and-libraries)または[静的サイト ジェネレーター](#static-site-generators)のドキュメントを参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Static Web Apps アプリケーション
- [Azure CLI](/cli/azure/install-azure-cli)

## <a name="types-of-application-settings"></a>アプリケーション設定の種類

Azure Static Web Apps アプリケーションには、通常、2 つの側面があります。 1 つ目は、Web アプリケーション、すなわち HTML、CSS、JavaScript、およびイメージで表される静的コンテンツです。 2 つ目は、Azure Functions アプリケーションの機能を使用するバックエンド API です。

この記事では、Azure Functions でバックエンド API のアプリケーション設定を管理する方法について説明します。

この記事で説明しているアプリケーション設定は、静的 Web アプリケーションでは使用および参照できません。 ただし、多くのフロントエンド フレームワークおよび静的サイト ジェネレーターでは、開発時に環境変数を使用できます。 これらの変数は、ビルド時に、生成された HTML または JavaScript で値に置き換えられます。 HTML および JavaScript のデータはサイト訪問者が簡単に検出できるため、フロントエンド アプリケーションには機密情報を入れないようにしてください。 機密データを保持する設定は、アプリケーションの API 部分に配置することをお勧めします。

JavaScript フレームワークまたはライブラリで環境変数を使用する方法の詳細については、次の記事を参照してください。

### <a name="javascript-frameworks-and-libraries"></a>リンクされた JavaScript フレームワークとライブラリ

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>静的サイト ジェネレーター

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>API アプリケーション設定

Azure Static Web Apps の API は Azure Functions の機能を利用しています。これにより、_local.settings.json_ ファイルでアプリケーション設定を定義できます。 このファイルは、構成の `Values` プロパティでアプリケーション設定を定義します。

次のサンプル _local.settings.json_ は、`DATABASE_CONNECTION_STRING` の値を追加する方法を示しています。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

`Values` プロパティに定義された設定は、`process.env` オブジェクトから使用できる環境変数としてコードから参照できます。

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json` ファイルは GitHub リポジトリで追跡されていません。このファイルには、データベース接続文字列などの機密情報が含まれていることが多いためです。 ローカル設定はマシン上に残っているため、設定を手動で Azure にアップロードする必要があります。

一般に、設定のアップロードは頻繁には行われず、毎回のビルドで必要となるわけではありません。

## <a name="uploading-application-settings"></a>アプリケーション設定のアップロード

アプリケーション設定を構成するには、Azure portal または Azure CLI を使用します。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal には、アプリケーション設定を作成、更新、および削除するためのインターフェイスが用意されています。

1. [Azure Portal](https://portal.azure.com) に移動します。

1. 検索バーで **Static Web Apps** を検索して選択します。

1. サイドバーの **[構成]** オプションをクリックします。

1. アプリケーション設定を適用する環境を選択します。 ステージング環境は pull request が生成されると自動的に作成され、pull request がマージされると、運用環境に昇格されます。 アプリケーション設定は、環境ごとに設定できます。

1. **[追加]** ボタンをクリックして、新しいアプリケーション設定を追加します。

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Azure Static Web Apps の構成ビュー":::

1. **[名前]** と **[値]** を入力します。

1. **[OK]** をクリックします。

1. **[保存]** をクリックします。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

`az rest` コマンドを使用すると、設定を Azure に一括アップロードできます。 このコマンドは、`properties` という親プロパティで JSON オブジェクトとしてアプリケーション設定を受け入れます。

適切な値を使用して JSON ファイルを作成する方法として最も簡単なのは、_local.settings.json_ ファイルの変更バージョンを作成することです。

1. 機密データを含む新しいファイルが公開されないようにするには、 _.gitignore_ ファイルに次のエントリを追加します。

   ```bash
   local.settings*.json
   ```

2. 次に、_local.settings.json_ ファイルのコピーを作成して、_local.settings.properties.json_ という名前を付けます。

3. この新しいファイル内で、アプリケーション設定を除く他のすべてのデータをファイルから削除し、名前を `Values` から `properties` に変更します。

   これで、ファイルは次の例のようになります。

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Azure CLI コマンドには、アップロードを実行するために、アカウントに固有の多数の値が必要です。 Static Web Apps リソースの _[Overview]\(概要\)_ ウィンドウから、次の情報にアクセスできます。

1. 静的サイトの名前
2. リソース グループ名
3. サブスクリプション ID

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure Static Web Apps の概要":::

4. ターミナルまたはコマンド ラインから、次のコマンドを実行します。 `<YOUR_STATIC_SITE_NAME>`、`<YOUR_RESOURCE_GROUP_NAME>`、および `<YOUR_SUBSCRIPTION_ID>` のプレースホルダーは、必ず _[Overview]\(概要\)_ ウィンドウの値に置き換えてください。

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> "local.settings.properties.json" ファイルは、このコマンドを実行するのと同じディレクトリに置かれている必要があります。 このファイルには任意の名前を付けることができます。 この名前は重要ではありません。

### <a name="view-application-settings-with-the-azure-cli"></a>Azure CLI を使用してアプリケーション設定を表示する

アプリケーション設定は、Azure CLI を使用して表示できます。

- ターミナルまたはコマンド ラインから、次のコマンドを実行します。 `<YOUR_SUBSCRIPTION_ID>`、`<YOUR_RESOURCE_GROUP_NAME>`、`<YOUR_STATIC_SITE_NAME>` のプレースホルダーは必ず、実際の値に置き換えてください。

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル開発の設定](local-development.md)
