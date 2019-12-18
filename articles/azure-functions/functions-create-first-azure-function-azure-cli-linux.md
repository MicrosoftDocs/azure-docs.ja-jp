---
title: Azure の Linux で初めての関数を作成する
description: コマンド ライン ツール、Azure Functions Core Tools、および Azure CLI を使用して、Azure の Linux でホストされる初めての関数を作成する方法について説明します。
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 57c1b4a0ef5e8f030360bca6d69f66e8752f3da1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964142"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>クイック スタート:コマンド ライン ツールを使用して Linux でホストされる初めての関数を作成する

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、[サーバーレス](https://azure.com/serverless)の Linux 環境でコードを実行できます。 Linux でのホストには、[Functions 2.x 以降のランタイム](functions-versions.md)が必要です。 サーバーレス関数は[従量課金](functions-scale.md#consumption-plan)プランで実行されます。

このクイック スタート記事では、Linux 上で動作する初めての関数アプリを Azure CLI を使用して作成する方法について説明します。 [Azure Functions Core Tools](functions-run-local.md) を使用して、関数のコードをローカルで作成し、その後 Azure にデプロイします。

次の手順は、Mac、Windows、または Linux コンピューターでサポートされます。 この記事では、JavaScript または C# で関数を作成する方法について説明します。 Python 関数を作成する方法については、[Core Tools と Azure CLI を使用した最初の Python 関数の作成](functions-create-first-function-python.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このサンプルを実行する前に、以下が必要です。

+ [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.6.666 以降をインストールします。

+ [Azure CLI]( /cli/azure/install-azure-cli) をインストールします。 この記事では、Azure CLI バージョン 2.0.46 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 [Azure Cloud Shell](https://shell.azure.com/bash) を使用することもできます。

+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Azure に Linux の関数アプリを作成する

Linux での関数の実行をホストするための関数アプリが必要です。 関数アプリは、関数コードを実行するためのサーバーレス環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 Linux 上で動作する関数アプリの作成には、[az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用します。

次のコマンドの `<app_name>` プレースホルダーには一意の Function App 名を、`<storage_name>` にはストレージ アカウント名を使用します。 `<app_name>` は、関数アプリの既定の DNS ドメインでもあります。 この名前は、Azure のすべてのアプリで一意である必要があります。 また、関数アプリの `<language>` ランタイムを `dotnet` (C#)、`node` (JavaScript/TypeScript)、または `python` から設定する必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

関数アプリの作成後、次のメッセージが表示されます。

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Azure の新しい関数アプリにプロジェクトを発行する準備が整いました。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]