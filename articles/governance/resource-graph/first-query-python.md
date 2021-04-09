---
title: クイック スタート:初めての Python クエリ
description: このクイックスタートでは、手順に従って、Python 用の Resource Graph ライブラリを有効にし、初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 6fc7b8f6a6fa7cde8d10dec6a4b2b834d41325e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920124"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>クイック スタート:Python を使用して初めての Resource Graph クエリを実行する

Azure Resource Graph を使用する最初の手順では、Python に必要なライブラリがインストールされていることを確認します。 このクイックスタートでは、ライブラリを Python のインストールに追加するプロセスについて説明します。

このプロセスの最後で、ライブラリを Python のインストールに追加し、初めての Resource Graph クエリを実行します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Resource Graph ライブラリを追加する

Python で Azure Resource Graph のクエリを実行できるようにするには、ライブラリを追加する必要があります。 このライブラリは、[Windows 10 の bash](/windows/wsl/install-win10) やローカルにインストールされている場合も含め、Python を使用できる場所ならどこでも動作します。

1. 最新の Python がインストールされていることを確認します (**3.8** 以降)。 まだインストールされていない場合は、[Python.org](https://www.python.org/downloads/) でダウンロードします。

1. 最新の Azure CLI がインストールされていることを確認します (**2.5.1** 以降)。 まだインストールされていない場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

   > [!NOTE]
   > 次の例で、Python で **CLI ベースの認証** を使用できるようにするには、Azure CLI が必要です。 その他のオプションの詳細については、[Python 用 Azure 管理ライブラリを使用した認証](/azure/developer/python/azure-sdk-authenticate)に関するページを参照してください。

1. Azure CLI から認証します。

   ```azurecli
   az login
   ```

1. 選択した Python 環境で、Azure Resource Graph に必要なライブラリをインストールします。

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > すべてのユーザーに対して Python がインストールされている場合、これらのコマンドは管理者特権のコンソールから実行する必要があります。

1. ライブラリがインストールされていることを確認します。 `azure-mgmt-resourcegraph` は **2.0.0** 以上、`azure-mgmt-resource` は **9.0.0** 以上、`azure-cli-core` は **2.5.0** 以上である必要があります。

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

選択した環境に Python ライブラリを追加したので、簡単な Resource Graph クエリを試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

1. インストールしたライブラリと `resources` メソッドを使用して、初めての Azure Resource Graph を実行します。

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `getresources` への呼び出しを更新し、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. `getresources` への呼び出しを更新し、最初にクエリを **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するように変更します。

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストールしたライブラリをお使いの Python 環境から削除する場合は、次のコマンドを使用します。

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Resource Graph ライブラリをお使いの Python 環境に追加し、初めてのクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)
