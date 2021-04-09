---
title: クイック スタート:初めての PowerShell クエリ
description: このクイックスタートでは、手順に従って、Azure PowerShell の Resource Graph 拡モジュールを有効にし、最初のクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 131bed4fe60035682a317e186f11561bc005b298
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917675"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して最初の Resource Graph クエリを実行します

Azure Resource Graph を使用する最初の手順では、Azure PowerShell のモジュールがインストールされていることを確認します。 このクイック スタートでは、Azure PowerShell のインストールにモジュールを追加するプロセスについて説明します。

このプロセスの最後では、選択した Azure PowerShell のインストールにモジュールを追加して、最初の Resource Graph クエリを実行しています。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Resource Graph モジュールを追加する

Azure PowerShell を Azure Resource Graph のクエリに対して有効にするには、モジュールを追加する必要があります。 このモジュールは、ローカルにインストールされた PowerShell、[Azure Cloud Shell](https://shell.azure.com)、または [PowerShell Docker イメージ](https://hub.docker.com/_/microsoft-powershell)で使用できます。

### <a name="base-requirements"></a>基本要件

Azure Resource Graph モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 1.0.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。

- PowerShellGet 2.0.1 以上。 インストールされていない場合、または更新されていない場合は、こちらの[手順](/powershell/scripting/gallery/installing-psget)に従ってください。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell の Resource Graph モジュールは、**Az.ResourceGraph** です。

1. **管理** PowerShell プロンプトで次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. モジュールがインポートされていて、最新のバージョン (0.7.5) であることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

Azure PowerShell モジュールが選択した環境に追加されたので、簡単な Resource Graph クエリを試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

1. `Search-AzGraph` コマンドレットを使用して最初の Resource Graph クエリを実行します。

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `order by`**名前** プロパティに対するクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. 最初に **名前** プロパティで並べ替え (`order by`) を行ってから結果を上位 5 件に制限 (`limit`) するようにクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

> [!NOTE]
> 既にアクセスできているサブスクリプションからクエリの結果が返されなかった場合、`Search-AzGraph` コマンドレットでは既定コンテキストのサブスクリプションが既定で使用されることに注意してください。 既定のコンテキストの一部であるサブスクリプション ID の一覧を表示するには、この `(Get-AzContext).Account.ExtendedProperties.Subscriptions` を実行します。アクセスできるすべてのサブスクリプション全体を検索する場合は、`$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}` を実行して `Search-AzGraph` コマンドレットの PSDefaultParameterValues を設定できます
   
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Resource Graph モジュールを Azure PowerShell 環境から削除する場合は、次のコマンドを使用して行うことができます。

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> これによって、以前にダウンロードしたモジュール ファイルが削除されることはありません。 実行中の PowerShell セッションから削除されるだけです。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure PowerShell 環境に Resource Graph モジュールを追加し、最初のクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)