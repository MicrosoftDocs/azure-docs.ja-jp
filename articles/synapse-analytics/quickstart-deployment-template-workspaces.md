---
title: クイック スタート:Azure Resource Manager テンプレートを使用して Azure Synapse ワークスペースを作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Synapse ワークスペースを作成する方法を学習します。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740371"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure Synapse ワークスペースを作成する

この Azure Resource Manager テンプレート (ARM テンプレート) では、基になる Data Lake Storage を使って Azure Synapse ワークスペースが作成されます。 Azure Synapse ワークスペースは、Azure Synapse Analytics の分析プロセス用のセキュリティ保護可能なコラボレーションの境界です。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

テンプレートを確認するには、 **[Visualize]\(視覚化\)** リンクを選択します。 次に **[テンプレートの編集]** を選択します。

[![視覚化する](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

このテンプレートでは、次の 2 つのリソースが定義されます。

- ストレージ アカウント
- ワークスペース

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。 このテンプレートによって、Synapse ワークスペースが作成されます。

   [![Azure へのデプロイ 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. 次の値を入力または更新します。

   - **サブスクリプション**:Azure サブスクリプションを選択します。
   - **[リソース グループ]** : **[新規作成]** を選択して、リソース グループの一意の名前を入力し、 **[OK]** を選択します。 新しいリソース グループを作成すると、リソースのクリーンアップが容易になります。
   - **[リージョン]** :リージョンを選択します。  たとえば **[米国中部]** です。
   - **Name**:ワークスペースの名前を入力します。
   - **[SQL Administrator login]\(SQL 管理者のログイン\)** : SQL Server の管理者のユーザー名を入力します。
   - **[SQL Administrator password]\(SQL 管理者のパスワード\)** : SQL Server の管理者のパスワードを入力します。
   - **[タグ値]** : 既定値のままにします。
   - **[確認と作成]** : 選択。
   - **作成**:選択。

## <a name="next-steps"></a>次のステップ

Azure Synapse Analytics と Azure Resource Manager の詳細については、以下の記事に進んでください。

- [Azure Synapse Analytics の概要](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)を読む
- [Azure Resource Manager](../azure-resource-manager/management/overview.md) の詳細を確認する
- [初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
