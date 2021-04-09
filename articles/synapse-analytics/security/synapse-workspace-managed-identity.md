---
title: Synapse ワークスペースのマネージド ID
description: Azure Synapse ワークスペースのマネージド ID について説明する記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7790bc2895449e8ab21cbd30d7da0e5529eb0562
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670681"
---
# <a name="azure-synapse-workspace-managed-identity"></a>Azure Synapse ワークスペース マネージド ID

この記事では、Azure Synapse ワークスペースのマネージド ID について学習します。

## <a name="managed-identities"></a>マネージド ID

Azure リソースのマネージド ID は、Azure Active Directory の機能です。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 マネージド ID 機能を使用して、Azure AD 認証をサポートする任意のサービスを認証できます。

Azure リソースのマネージド ID は、以前のマネージド サービス ID (MSI) の新しい名前です。 詳細については、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

## <a name="azure-synapse-workspace-managed-identity"></a>Azure Synapse ワークスペース マネージド ID

ワークスペースを作成すると、Azure Synapse ワークスペースに対して、システムによって割り当てられたマネージド ID が作成されます。

>[!NOTE]
>このワークスペース マネージド ID は、このドキュメントの残りの部分ではマネージド ID と呼ばれます。

Azure Synapse では、マネージド ID を使用してパイプラインを統合します。 マネージド ID のライフサイクルは、Azure Synapse ワークスペースに直接関連付けられています。 Azure Synapse ワークスペースを削除すると、マネージド ID もクリーンアップされます。

ワークスペース マネージド ID には、パイプラインで操作を実行するためのアクセス許可が必要です。 アクセス許可を付与するときに、オブジェクト ID または Azure Synapse ワークスペース名を使用してマネージド ID を見つけることができます。

## <a name="retrieve-managed-identity-in-azure-portal"></a>Azure portal でマネージド ID を取得する

Azure portal でマネージド ID を取得できます。 Azure portal で Azure Synapse ワークスペースを開き、左側のナビゲーションから **[概要]** を選択します。 マネージド ID オブジェクト ID がメイン画面に表示されます。

![マネージド ID オブジェクト ID](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

マネージド ID 情報は、マネージド ID 認証をサポートするリンクされたサービスを Azure Synapse Studio から作成するときにも表示されます。

**Azure Synapse Studio** を起動し、左側のナビゲーションから **[管理]** タブを選択します。 次に、 **[リンクされたサービス]** を選択し、 **[+ 新規]** オプションを選択して、新しいリンクされたサービスを作成します。

![リンクされたサービスの作成 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

**[新しいリンクされたサービス]** ウィンドウで、「*Azure Data Lake Storage Gen2*」と入力します。 リストから **[Azure Data Lake Storage Gen2]** のリソースの種類を選択し、 **[続行]** をクリックします。

![リンクされたサービスの作成 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

次のウィンドウで、 **[認証方法]** として **[マネージド ID]** を選択します。 マネージド ID の **名前** と **オブジェクト ID** が表示されます。

![リンクされたサービスの作成 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース マネージド ID へのアクセス許可の付与](./how-to-grant-workspace-managed-identity-permissions.md)の詳細情報
