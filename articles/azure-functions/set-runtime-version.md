---
title: Azure Functions ランタイム バージョンをターゲットにする方法
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる Function App のランタイム バージョンを指定する方法について説明します。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: e9aa5546b5f07b724fe22bc1e20a2e97feb2aec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435564"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

Function App は、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、次の 3 つがあります:[3.x、2.x、および 1.x](functions-versions.md)。 既定では、Function App はバージョン 3.x のランタイムで作成されます。 この記事では、選択したバージョンで実行されるように Azure の Function App を構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

特定のバージョンを手動でターゲットにする方法は、Windows と Linux のどちらを実行しているかによって異なります。

## <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新

_このセクションは、[Linux 上](#manual-version-updates-on-linux)で関数アプリを実行する場合には適用されません。_

Azure Functions を使用すると、Function App 内で `FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用することで、Windows 上のランタイムの特定のバージョンをターゲットにすることができます。 Function App は、新しいバージョンへの移行を明示的に選択しない限り、指定されたメジャー バージョンに保持されます。 メジャー バージョンのみを指定した場合、Function App は、ランタイムの新しいマイナー バージョンが利用可能になった時点で、自動的に新しいマイナー バージョンに更新されます。 新しいマイナー バージョンには、重大な変更は導入できません。 

マイナー バージョン (たとえば "2.0.12345") を指定した場合、Function App は、明示的にバージョンを変更するまで、その特定のバージョンに固定されます。 古いマイナー バージョンは、運用環境から定期的に削除されます。 マイナー バージョンが削除されると、関数アプリは、`FUNCTIONS_EXTENSION_VERSION` で設定されたバージョンではなく、最新バージョンでの実行に戻ります。 このため、特定のマイナー バージョンを必要とする関数アプリの問題を迅速に解決する必要があります。 その後、メジャー バージョンをターゲットにする設定に戻ることができます。 マイナー バージョンの削除は、[App Service のお知らせ](https://github.com/Azure/app-service-announcements/issues)ページで発表されます。

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

>[!NOTE]
>`~2.0` にピン留めされた .NET 関数アプリでは、.NET Core 3.1 への自動アップグレードがオプトアウトされます。 詳細については、[Functions v2. x に関する考慮事項](functions-dotnet-class-library.md#functions-v2x-considerations)に関するページを参照してください。  

## <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

_このセクションは、[Linux 上](#manual-version-updates-on-linux)で関数アプリを実行する場合には適用されません。_

Function App によって使用されるランタイム バージョンを変更できます。 破壊的変更の可能性があるため、ランタイム バージョンの変更は、関数アプリで関数を作成する前にのみ実行できます。 

> [!IMPORTANT]
> ランタイム バージョンは `FUNCTIONS_EXTENSION_VERSION` の設定によって決定されますが、この変更はその設定の直接の変更によってではなく、Azure portal でのみ行う必要があります。 これは、ポータルが変更を検証し、必要に応じてその他の関連する変更を行うためです。

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

このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 

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

`<FUNCTION_APP>` をお使いの関数アプリの名前に置き換えます。 また、`<RESOURCE_GROUP>` をお使いの関数アプリのリソース グループの名前に置き換えます。 また、`<VERSION>` を特定のバージョンか、`~3`、`~2`、または `~1` に置き換えます。

前のコード例で **[試してみる]** を選択し、[Azure Cloud Shell](../cloud-shell/overview.md) でコマンドを実行します。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)実行して、このコマンドを実行することもできます。 ローカルで実行する場合は、最初に [az login](/cli/azure/reference-index#az-login) を実行してサインインする必要があります。

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

## <a name="manual-version-updates-on-linux"></a>Linux 上でのバージョンの手動更新

Linux 関数アプリを特定のホスト バージョンにピン留めするには、サイト構成の 'LinuxFxVersion' フィールドにイメージの URL を指定します。たとえば、Node 10 の関数アプリをホスト バージョン 3.0.13142 にピン留めする場合は、次のようになります。

**Linux アプリ サービスまたはエラスティック Premium アプリ** の場合: `LinuxFxVersion` を `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` に設定します。

**Linux 従量課金アプリ** の場合: `LinuxFxVersion` を `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` に設定します。

# <a name="portal"></a>[ポータル](#tab/portal)

関数アプリのサイト構成設定の表示と変更は、Azure portal ではサポートされていません。 代わりに Azure CLI を使用してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI を使用して `LinuxFxVersion` を表示および設定することができます。  

現在のランタイム バージョンを表示するには、[az functionapp config show](/cli/azure/functionapp/config) コマンドと共に使用します。

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 `linuxFxVersion` の現在の値が返されます。

関数アプリの `linuxFxVersion` の設定を更新するには、[az functionapp config set](/cli/azure/functionapp/config) コマンドを使用します。

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

`<FUNCTION_APP>` をお使いの関数アプリの名前に置き換えます。 また、`<RESOURCE_GROUP>` をお使いの関数アプリのリソース グループの名前に置き換えます。 また、上記のように、`<LINUX_FX_VERSION>` を特定のイメージの値に置き換えます。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

現時点では、Azure PowerShell を使用して `linuxFxVersion` を設定することはできません。 代わりに Azure CLI を使用してください。

---

サイト構成に変更が加えられると、関数アプリが再起動されます。

> [!NOTE]
> 従量課金プランで実行されているアプリの場合、`LinuxFxVersion` を特定のイメージに設定すると、コールド スタート時間が長くなる可能性があります。 これは、特定のイメージにピン留めすると、Functions がコールド スタートの最適化を使用できなくなるためです。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)
