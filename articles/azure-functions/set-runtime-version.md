---
title: Azure Functions ランタイム バージョンをターゲットにする方法
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる Function App のランタイム バージョンを指定する方法について説明します。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926577"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

Function App は、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、次の 3 つがあります:[1.x、2.x、および 3.x](functions-versions.md)。 既定では、Function App はバージョン 3.x のランタイムで作成されます。 この記事では、選択したバージョンで実行されるように Azure の Function App を構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

## <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新

Azure Functions を使用すると、Function App 内で `FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用することで、ランタイムの特定のバージョンをターゲットにすることができます。 Function App は、新しいバージョンへの移行を明示的に選択しない限り、指定されたメジャー バージョンに保持されます。 メジャー バージョンのみを指定した場合、Function App は、ランタイムの新しいマイナー バージョンが利用可能になった時点で、自動的に新しいマイナー バージョンに更新されます。 新しいマイナー バージョンには、重大な変更は導入できません。 

マイナー バージョン (たとえば "2.0.12345") を指定した場合、Function App は、明示的にバージョンを変更するまで、その特定のバージョンに固定されます。 古いマイナー バージョンは、運用環境から定期的に削除されます。 これが行われると、Function App は `FUNCTIONS_EXTENSION_VERSION` で設定されたバージョンではなく、最新バージョンで実行されます。 このため、メジャー バージョンをターゲットにするには、特定のマイナー バージョンを必要とする Function App の問題を迅速に解決する必要があります。 マイナー バージョンの削除は、[App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)ページで発表されます。

> [!NOTE]
> Azure Functions の特定のメジャー バージョンに固定された後で、Visual Studio を使用して Azure に発行しようとすると、最新のパ―ジョンへのアップロードまたは発行の取り消しを求めるダイアログ ウィンドウが表示されます。 これを回避するには、`.csproj` ファイルに `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` プロパティを追加します。

新しいバージョンが公開されると、ポータルで確認のメッセージが表示され、そのバージョンに移行することができます。 新しいバージョンに移行した後は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、いつでも以前のバージョンに戻すことができます。

次の表は、各メジャー バージョンの自動更新を有効にする `FUNCTIONS_EXTENSION_VERSION` 値を示しています。

| メジャー バージョン | `FUNCTIONS_EXTENSION_VERSION` 値 |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

ランタイム バージョンを変更するたびに、Function App が再起動されます。

## <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

Function App によって使用されるランタイム バージョンを変更できます。 破壊的変更の可能性があるため、ランタイム バージョンの変更は、Function App で関数を作成する前にのみ実行できます。 

> [!IMPORTANT]
> ランタイム バージョンは `FUNCTIONS_EXTENSION_VERSION` の設定によって決定されますが、この変更はその設定の直接の変更によってではなく、Azure Portal で行う必要があります。 これは、ポータルが変更を検証し、必要に応じてその他の関連する変更を行うためです。

# <a name="portal"></a>[ポータル](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure portal では、既に関数が含まれている Function App のランタイム バージョンを変更することはできません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI から `FUNCTIONS_EXTENSION_VERSION` を表示および設定することもできます。  

Azure CLI を使用して、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドで現在のランタイム バージョンを表示します。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

このコードでは、`<function_app>` をお使いの Function App の名前に置き換えます。 また、`<my_resource_group>` をお使いの Function App のリソース グループの名前に置き換えます。 

明確にするために切り捨てられていますが、次の出力に `FUNCTIONS_EXTENSION_VERSION` が表示されます。

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドで、Function App の `FUNCTIONS_EXTENSION_VERSION` の設定を更新できます。

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

`<FUNCTION_APP>` をお使いの Function App の名前に置き換えます。 また、`<RESOURCE_GROUP>` をお使いの Function App のリソース グループの名前に置き換えます。 また、`<VERSION>` を特定のバージョンか、`~3`、`~2`、または `~1` に置き換えます。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure Functions ランタイムを確認するには、次のコマンドレットを使用します。 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

`<FUNCTION_APP>` をお使いの Function App の名前に、`<RESOURCE_GROUP>` をリソース グループの名前に置き換えます。 `FUNCTIONS_EXTENSION_VERSION` 設定の現在の値がハッシュ テーブルに返されます。

Functions ランタイムを変更するには、次のスクリプトを実行します。

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

以前と同様に、`<FUNCTION_APP>` をお使いの Function App 名に置き換え、`<RESOURCE_GROUP>` をリソース グループの名前に置き換えます。 また、 `<VERSION>` を特定のバージョンまたはメジャー バージョン (`~2` や `~3` など) に置き換えます。 返されたハッシュ テーブルで `FUNCTIONS_EXTENSION_VERSION` 設定の更新された値を確認できます。 

---

アプリケーション設定に変更が加えられると、Function App が再起動します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)
