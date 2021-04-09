---
title: プライベート リンクを使用して Synapse ワークスペースに接続する
description: この記事では、プライベート リンクを使用して Azure Synapse ワークスペースに接続する方法について説明します。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9782cce4165487b612c0295dc893d120ed043225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218265"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links"></a>プライベート リンクを使用して Azure Synapse ワークスペースに接続する

この記事では、Azure Synapse ワークスペースへのプライベート エンドポイントを作成する方法について説明します。 詳細については、[プライベート リンクとプライベート エンドポイント](../../private-link/index.yml)に関するページを参照してください。

## <a name="step-1-register-network-resource-provider"></a>手順 1:ネットワーク リソースプロバイダーを登録する

まだ登録していない場合、ネットワーク リソース プロバイダーを登録します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 [登録](../../azure-resource-manager/management/resource-providers-and-types.md)時、リソースプロバイダーの一覧から *Microsoft.Network* を選択します。 ネットワーク リソースプロバイダーが既に登録されている場合は、手順 2 に進みます。

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>手順 2:Azure portal で Azure Synapse ワークスペースを開く

**[セキュリティ]** で **[プライベート エンドポイント接続]** を選択します。 
![Azure portal で Azure Synapse ワークスペースを開く](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

次の画面で、 **[+ プライベート エンドポイント]** を選択します。

![Azure portal でプライベート エンドポイントを開く](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>手順 3:サブスクリプションとリージョンの詳細を選択する

**[基本]** タブの **[プライベート エンドポイントの作成]** ウィンドウで、 **[サブスクリプション]** と **[リソース グループ]** を選択します。 作成するプライベート エンドポイントに **[名前]** を付けます。 プライベート エンドポイントを作成する **[リージョン]** を選択します。

プライベート エンドポイントはサブネット内に作成されます。 選択されたサブスクリプション、リソース グループ、リージョンによって、プライベート エンドポイントのサブネットがフィルター処理されます。完了したら、 **[次へ: リソース >]** を選択します。
![サブスクリプションとリージョンの詳細の選択 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>手順 4:Azure Synapse ワークスペースの詳細を選択する

**[リソース]** タブで **[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。Azure Synapse ワークスペースが含まれている **[サブスクリプション]** を選択します。 Azure Synapse ワークスペースへのプライベート エンドポイントを作成するための **[リソースの種類]** は *Microsoft.Synapse/workspaces* です。

**[リソース]** として Azure Synapse ワークスペースを選択します。 すべての Azure Synapse ワークスペースには、プライベート エンドポイントの作成先として指定できる次の 3 つの **[ターゲット サブリソース]** があります。Sql、SqlOnDemand、Dev。

**[次へ: 構成 >]** を選択して、セットアップの次の部分に進みます。
![サブスクリプションとリージョンの詳細の選択 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**[構成]** タブで、プライベート エンドポイントを作成する **[仮想ネットワーク]** と **[サブネット]** を選択します。 また、プライベート エンドポイントにマップされる DNS レコードも作成する必要があります。

**[プライベート DNS ゾーンとの統合]** では **[はい]** を選択して、プライベート エンドポイントをプライベート DNS ゾーンと統合します。 Microsoft Azure Virtual Network に関連付けられたプライベート DNS ゾーンがない場合は、新しいプライベート DNS ゾーンが作成されます。 完了したら、 **[確認と作成]** を選択します。

![サブスクリプションとリージョンの詳細の選択 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

デプロイが完了したら、Azure portal で Azure Synapse ワークスペースを開き、 **[プライベート エンドポイント接続]** を選択します。 新しいプライベート エンドポイントと、そのプライベート エンドポイントに関連付けられたプライベート エンドポイント接続名が表示されます。

![サブスクリプションとリージョンの詳細の選択 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>次のステップ

[マネージド ワークスペースの Virtual Network](./synapse-workspace-managed-vnet.md) の詳細を学習する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)