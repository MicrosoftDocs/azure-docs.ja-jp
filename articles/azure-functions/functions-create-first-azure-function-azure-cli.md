---
title: Azure CLI での初めての関数の作成
description: Azure CLI と Azure Functions Core Tools を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 82fbaa35d8d06cdb1999a76a36fb4a1dade017e9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883890"
---
# <a name="create-your-first-function-from-the-command-line"></a>コマンド ラインから最初の関数を作成する

このクイックスタート トピックでは、コマンド ラインまたはターミナルから最初の関数を作成する方法について説明します。 Azure CLI を使用して、関数をホストする[サーバーレス](https://azure.microsoft.com/solutions/serverless/) インフラストラクチャである Function App を作成します。 関数コード プロジェクトは、[Azure Functions Core Tools](functions-run-local.md) を使用してテンプレートから生成されます。このツールは、関数アプリ プロジェクトを Azure にデプロイするためにも使用されます。

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行することができます。

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

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 Function App の作成には、[az functionapp create](/cli/azure/functionapp#az-functionapp-create) コマンドを使用します。 

次のコマンドでは、`<APP_NAME>` プレースホルダーを一意の Function App 名で、`<STORAGE_NAME>` をストレージ アカウント名で置き換えます。 `<APP_NAME>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 また、関数アプリの `<language>` ランタイムを `dotnet` (C#) または `node` (JavaScript) から設定する必要があります。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

_consumption-plan-location_ パラメーターを設定すると、従量課金ホスティング プランで Function App がホストされます。 このサーバーレス プランでは、関数からの要求に応じてリソースが動的に追加され、関数が実行中のときだけ課金されます。 詳細については、「[Azure Functions の適切なサービス プランを選択する](functions-scale.md)」を参照してください。

Function App が作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

