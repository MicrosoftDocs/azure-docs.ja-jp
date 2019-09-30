---
title: Azure Network Watcher を使用したネットワーク セキュリティ グループの管理 - Azure CLI | Microsoft Docs
description: このページは、Azure CLI で Azure Network Watcher のネットワーク セキュリティ グループのフロー ログを管理する方法を説明します。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 5e7c09c1a06a94a2ed64f3624ee38dc42606d7bc
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563493"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Azure CLI を使用したネットワーク セキュリティ グループ フローのログの構成

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

ネットワーク セキュリティ グループ フロー ログは、ネットワーク セキュリティ グループを介して IP トラフィックの送信と受信に関する情報を表示できるようにする Network Watcher の機能です。 これらのフローのログは json 形式で記述され、ルールごとの送信、受信フロー、フロー (送信元/送信先 IP、送信元/送信先ポート、プロトコル) についての 5 組の情報が適用される NIC、トラフィックが許可されているか、拒否されているかを示しています。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (CLI) をインストール](/cli/azure/install-azure-cli)する必要があります。

## <a name="register-insights-provider"></a>Insights プロバイダーの登録

フローのログ記録を成功させるには、**Microsoft.Insights** プロバイダーを登録する必要があります。 **Microsoft.Insights** プロバイダーが登録されているかどうか不明な場合は、次のスクリプトを実行します。

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>ネットワーク セキュリティ グループのフローのログを有効にする

フローのログを有効にするコマンドを次の例に示します。

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

指定するストレージ アカウントに対しては、Microsoft サービスまたは特定の仮想ネットワークのみにネットワーク アクセスを制限するネットワーク規則が構成されていてはなりません。 ストレージ アカウントは、フロー ログを有効化した NSG と同じ Azure サブスクリプションにも、異なる Azure サブスクリプションにも設定できます。 異なるサブスクリプションを使用する場合は、それらが同じ Azure Active Directory テナントに関連付けられている必要があります。 各サブスクリプションで使用するアカウントは、[必要なアクセス許可](required-rbac-permissions.md)を持っている必要があります。 

ストレージ アカウントが、ネットワーク セキュリティ グループではなく、異なるリソース グループまたはサブスクリプション内にある場合、ストレージ アカウントの名前ではなく完全な ID を指定します。 たとえば、ストレージ アカウントが *RG-Storage* というリソース グループ内にある場合、前のコマンドで *storageAccountName* を指定するのではなく、 */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName* を指定する必要があります。

## <a name="disable-network-security-group-flow-logs"></a>ネットワーク セキュリティ グループのフローのログを無効にする

フローのログを無効にするには、次の例を使用します。

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>フローのログをダウンロードする

フローのログの保存場所は作成時に定義されます。 ストレージ アカウントに保存されているこれらのフローのログにアクセスする際の便利なツールが Microsoft Azure Storage Explorer です。このツールは、 https://storageexplorer.com/ からダウンロードできます。

ストレージ アカウントが指定されている場合、フロー ログ ファイルは、次の場所にあるストレージ アカウントに保存されます。

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> 現在、Network Watcher の[ネットワーク セキュリティ グループ (NSG) フロー ログ](network-watcher-nsg-flow-logging-overview.md)がアイテム保持ポリシー設定に基づいて BLOB ストレージから自動的に削除されないという問題があります。 0 以外のアイテム保持ポリシーが存在する場合は、保持期間を過ぎているストレージ BLOB を定期的に削除して、課金が発生しないようにすることをお勧めします。 NSG フロー ログ ストレージ BLOB を削除する方法の詳細については、[NSG フロー ログ ストレージ BLOB の削除](network-watcher-delete-nsg-flow-log-blobs.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

[PowerBI を使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-power-bi.md)方法を確認する

[オープン ソース ツールを使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)方法を確認する
