---
title: Azure PowerShell スクリプト - Azure Cosmos アカウントのフェールオーバー優先度を変更したりフェールオーバーをトリガーしたりする
description: Azure PowerShell スクリプト サンプル - Azure Cosmos アカウントのフェールオーバー優先度を変更したりフェールオーバーをトリガーしたりします
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: e4406124a7ea4eac213d830d0e5960e76fb6d364
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155400"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-using-powershell"></a>PowerShell を使用して Azure Cosmos アカウントのフェールオーバー優先度を変更したりフェールオーバーをトリガーしたりする

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

> [!NOTE]
> 手動フェールオーバーは、`failoverPriority=0` のリージョンを変更した場合にトリガーされ、また、手動フェールオーバー用に構成されたアカウントに対してのみ実行できます。 それ以外は、どのリージョンを変更しても、Cosmos アカウントのフェールオーバー優先度が変更されるだけです。
> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントの使用方法を紹介しています。 このサンプルを他の API で使用する場合は、関連するプロパティをコピーし、お使いの API 固有のスクリプトに適用してください。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
|**Azure リソース**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | リソースに対するアクションを呼び出します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../../../powershell-samples.md)に関する記事をご覧ください。
