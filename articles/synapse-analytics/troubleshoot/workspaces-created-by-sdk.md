---
title: トラブルシューティング:SDK によって作成されたワークスペースから、Synapse Studio を起動できません。
description: SDK によって作成されたワークスペースから Synapse Studio を起動できない問題を解決する手順
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96465527"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>SDK を使用して作成された Azure Synapse Analytics ワークスペースのトラブルシューティング

この記事では、ソフトウェア開発キット (SDK) で作成された Synapse ワークスペースから Synapse Studio を起動するためのトラブルシューティング手順について説明します。


## <a name="prerequisites"></a>[前提条件]

- SDK を使用して作成された Synapse ワークスペース

## <a name="workaround"></a>回避策

SDK によって作成されたワークスペースから Synapse Studio を起動するには、次の手順を実行します。 
  1.    `az synapse workspace create` を実行してワークスペースを作成します。
  2.    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` を実行してマネージド ID を抽出します。
  3.    ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.` を実行して、ストレージ BLOB データ共同作成者ロールをマネージド ID のストレージ アカウントに付与します。
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` を実行して、ファイアウォール規則を追加します。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse とは](../overview-what-is.md)
* [開始するには](../get-started.md)
