---
title: 制限に照らした Azure リソース使用量の確認 | Microsoft Docs
description: Azure サブスクリプションの制限に照らして Azure リソース使用量を確認する方法について説明します。
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jeconnoc
ms.openlocfilehash: b8bcbabe4b355e4e9cd617e9003902885b8edd88
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872445"
---
# <a name="check-resource-usage-against-limits"></a>制限に照らしたリソース使用量の確認

この記事では、ご使用のサブスクリプション内でデプロイした各ネットワーク リソースの種類の数値を表示する方法と、[サブスクリプションの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)の概要について説明します。 制限に照らしてリソース使用量を表示する機能は、現在の使用状況を追跡し、今後の使用方法を計画するうえで便利です。 使用量を追跡するには、[Azure Portal](#azure-portal)、[PowerShell](#powershell)、または [Azure CLI](#azure-cli) を使用できます。

## <a name="azure-portal"></a>Azure Portal

1. Azure [portal](https://portal.azure.com) にログインします。
2. Azure portal の左上にある **[すべてのサービス]** を選択します。
3. **[フィルター]** ボックスに「*Subscriptions*」と入力します。 検索結果に **Subscriptions** が表示されたら、それを選びます。
4. 使用状況を確認するサブスクリプションの名前を選択します。
5. **[設定]** で、 **[使用量 + クォータ]** を選択します。
6. 次のオプションを選択できます。
   - **リソースの種類**: すべてのリソースの種類を選択することも、表示する特定のリソースの種類を選択することもできます。
   - **プロバイダー**: すべてのリソース プロバイダーを選択することも、**コンピューティング**、**ネットワーク**、または**ストレージ**を選択することもできます。
   - **場所**: すべての Azure の場所を選択することも、特定の場所を選択することもできます。
   - すべてのリソースを選択することも、少なくとも 1 つがデプロイされているリソースのみを表示することもできます。

     次の図の例は、少なくとも 1 つのリソースがデプロイされている、米国東部のすべてのネットワーク リソースを示しています。

       ![使用状況データを表示する](./media/check-usage-against-limits/view-usage.png)

     列見出しを選択すると、列を並べ替えることができます。 表示されている制限は、ご使用のサブスクリプションに対する制限です。 既定の制限を増やす必要がある場合は、 **[引き上げを依頼する]** を選択し、サポート リクエストを作成して送信します。 Azure の[制限](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)に関するページには、すべてのリソースの最大制限値が一覧表示されています。 現在の制限が既に最大値に達している場合、制限値を増やすことはできません。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール、バージョン 1.0.0 以降が必要です。 コンピューターで `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure にログインする必要もあります。

制限に照らして使用状況を確認するには、[Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage) を使用します。 次の例では、米国東部の場所に少なくとも 1 つのリソースがデプロイされているリソースの使用量が取得されます。

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

次の出力例のような形式の出力が返されます。

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 この記事では、Azure CLI バージョン 2.0.32 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure にログインする必要もあります。

制限に照らして使用量を確認するには、[az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages) を使用します。 次の例では、米国東部の場所にあるリソースの使用量が取得されます。

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

次の出力例のような形式の出力が返されます。

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
