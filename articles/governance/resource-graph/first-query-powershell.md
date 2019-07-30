---
title: Azure PowerShell を使用して最初のクエリを実行する
description: この記事では、Azure PowerShell の Resource Graph モジュールを有効にして、最初のクエリを実行する手順について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 76771a7dfcc323cca6ea52366195c895ee510701
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165592"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して最初の Resource Graph クエリを実行します

Azure Resource Graph を使用する最初の手順では、Azure PowerShell のモジュールがインストールされていることを確認します。 このクイック スタートでは、Azure PowerShell のインストールにモジュールを追加するプロセスについて説明します。

このプロセスの最後では、選択した Azure PowerShell のインストールにモジュールを追加して、最初の Resource Graph クエリを実行しています。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Resource Graph モジュールを追加する

Azure PowerShell を Azure Resource Graph のクエリに対して有効にするには、モジュールを追加する必要があります。 このモジュールは、ローカルにインストールされた PowerShell、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell/)で使用できます。

### <a name="base-requirements"></a>基本要件

Azure Resource Graph モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 1.0.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。

- PowerShellGet 2.0.1 以上。 インストールされていない場合、または更新されていない場合は、こちらの[手順](/powershell/gallery/installing-psget)に従ってください。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell の Resource Graph モジュールは、**Az.ResourceGraph** です。

1. **管理** PowerShell プロンプトで次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. モジュールがインポートされており、適切なバージョン (0.7.1) であることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

Azure PowerShell モジュールが選択した環境に追加されたので、簡単な Resource Graph クエリを試してみましょう。 クエリは、各リソースの**名前**および**リソースの種類**を使用して、最初の 5 つの Azure リソースを返します。

1. `Search-AzGraph` コマンドレットを使用して最初の Resource Graph クエリを実行します。

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `order by`**名前**プロパティに対するクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 これによりクエリの結果をまず制限し、それからそれらを注文します。

1. 最初に**名前**プロパティで並べ替え (`order by`) を行ってから結果を上位 5 件に制限 (`limit`) するようにクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

最終的なクエリを複数回実行したとき、環境内で何も変更がないと仮定すると、返される結果は一貫性があり、想定どおりになります。つまり、結果は**名前**プロパティで並べ替えられますが、上位 5 件に制限されます。

> [!NOTE]
> 既にアクセスできているサブスクリプションからクエリの結果が返されなかった場合、`Search-AzGraph` コマンドレットでは既定コンテキストのサブスクリプションが既定で使用されることに注意してください。 既定のコンテキストの一部であるサブスクリプション ID の一覧を表示するには、この `(Get-AzContext).Account.ExtendedProperties.Subscriptions` を実行します。アクセスできるすべてのサブスクリプション全体を検索する場合は、`$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID }` を実行して `Search-AzGraph` コマンドレットの PSDefaultParameterValues を設定できます
   
## <a name="clean-up-resources"></a>リソースのクリーンアップ

Resource Graph モジュールを Azure PowerShell 環境から削除する場合は、次のコマンドを使用して行うことができます。

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> これにより、以前にダウンロードしたモジュールのファイルは削除されません。 実行中の PowerShell セッションから削除されるだけです。

## <a name="next-steps"></a>次の手順

- [クエリ言語](./concepts/query-language.md)に関するさらなる情報を入手します
- [その他のリソース](./concepts/explore-resources.md)
- [Azure CLI ](first-query-azurecli.md)を使用して最初のクエリを実行します
- [Starter クエリ](./samples/starter.md)のサンプルを参照してください
- [高度なクエリ](./samples/advanced.md)のサンプルを参照してください
- [UserVoice ](https://feedback.azure.com/forums/915958-azure-governance)にフィードバックを提供してください
