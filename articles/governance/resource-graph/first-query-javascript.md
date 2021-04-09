---
title: クイック スタート:初めての JavaScript クエリ
description: このクイックスタートでは、手順に従って、JavaScript 用の Resource Graph ライブラリを有効にし、初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 97cbd9ddf65a4135f55304f6dd67c704c6fcac5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917523"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>クイック スタート:JavaScript を使用して初めての Resource Graph クエリを実行する

このクイックスタートでは、ライブラリを JavaScript のインストールに追加するプロセスについて説明します。 Azure Resource Graph を使用する最初の手順は、必須のライブラリで JavaScript アプリケーションを初期化することです。

このプロセスの最後で、ライブラリを JavaScript のインストールに追加し、初めての Resource Graph クエリを実行します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- **Node.js**:[Node.js](https://nodejs.org/) バージョン 12 以降が必須です。

## <a name="application-initialization"></a>アプリケーションの初期化

JavaScript で Azure Resource Graph にクエリを実行できるようにするには、環境を構成する必要があります。 [Windows 10 上での Bash](/windows/wsl/install-win10) など、JavaScript を利用できる場所であればどこでもこのセットアップは機能します。

1. 次のコマンドを実行し、新しい Node.js プロジェクトを初期化します。

   ```bash
   npm init -y
   ```

1. yargs モジュールへの参照を追加します。

   ```bash
   npm install yargs
   ```

1. Azure Resource Graph モジュールへの参照を追加します。

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Azure 認証ライブラリへの参照を追加します。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ で `@azure/arm-resourcegraph` がバージョン **2.0.0** 以降であることと `@azure/ms-rest-nodeauth` がバージョン **3.0.3** 以降であることを確認します。

## <a name="query-the-resource-graph"></a>Resource Graph にクエリを実行する

1. _index.js_ という名前のファイルを新規作成し、次のコードを入力します。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. ターミナルに次のコマンドを入力します。

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   `<YOUR_SUBSCRIPTION_ID_LIST>` プレースホルダーを必ず、ご自分の Azure サブスクリプション ID のコンマ区切りリストと置換してください。

   > [!NOTE]
   > このクエリ例では、`order by` などの並べ替え修飾子を指定していません。そのため、このクエリを複数回実行すると、要求ごとに、得られる一連のリソースが異なる可能性があります。

1. `index.js` の最初のパラメーターを変更し、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。 `<YOUR_SUBSCRIPTION_ID_LIST>` をサブスクリプション ID で置き換えます。

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   スクリプトによって認証が試行されると、次のようなメッセージがターミナルに表示されます。

   > サインインするには、Web ブラウザーを使用して https://microsoft.com/devicelogin ページを開き、認証用のコード FGB56WJUGK を入力します。

   ブラウザーでの認証が完了すると、スクリプトが引き続き実行されます。

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. `index.js` の最初のパラメーターを変更し、まず **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するようにクエリを変更します。 `<YOUR_SUBSCRIPTION_ID_LIST>` をサブスクリプション ID で置き換えます。

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストール済みのライブラリをアプリケーションから削除する場合、次のコマンドを実行します。

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Resource Graph ライブラリをお使いの JavaScript 環境に追加し、初めてのクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)