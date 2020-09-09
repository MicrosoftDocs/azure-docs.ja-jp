---
title: データ ソースの結果に接続するためのマネージド プライベート エンドポイントを作成します
description: この記事では、Azure Synapse ワークスペースからデータ ソースへのマネージド プライベート エンドポイントを作成する方法について説明します。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: fae958542f1bc391f285104d80d1e19131470abe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065470"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>データ ソースへのマネージド プライベート エンドポイントを作成する (プレビュー)

この記事では、Azure でデータ ソースへのマネージド プライベート エンドポイントを作成する方法について説明します。 詳細については、[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)に関するページを参照してください。

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>手順 1:Azure portal で Azure Synapse ワークスペースを開く

Azure Synapse Studio から、データ ソースへのマネージド プライベート エンドポイントを作成できます。 Azure portal で **[概要]** タブを選択し、 **[Synapse Studio の起動]** を選択します。
![Azure Synapse Studio を起動する](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>手順 2:Synapse Studio で [Managed Virtual Networks] (マネージド仮想ネットワーク) タブに移動する

Azure Synapse Studio で、左側のナビゲーションから **[管理]** タブを選択します。 **[Managed Virtual Networks]\(マネージド仮想ネットワーク\)** を選択し、 **[+ New]\(+ 新規作成\)** を選択します。
![新しいマネージド プライベート エンドポイントを作成する](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>手順 3:データ ソースの種類を選択する

データ ソースの種類を選択します。 この場合、ターゲット データ ソースは ADLS Gen2 アカウントです。 **[続行]** をクリックします。
![ターゲット データ ソースの種類を選択する](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>手順 4:データ ソースに関する情報を入力する

次のウィンドウで、データ ソースに関する情報を入力します。 この例では、ADLS Gen2 アカウントへのマネージド プライベート エンドポイントを作成しています。 マネージド プライベート エンドポイントの **[名前]** を入力します。 **[Azure サブスクリプション]** および **[ストレージ アカウント名]** を指定します。 **［作成］** を選択します
![ターゲット データ ソースの詳細を入力する](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>手順 5:マネージド プライベート エンドポイントが正常に作成されたことを確認する

要求を送信すると、その状態が表示されます。 マネージド プライベート エンドポイントが正常に作成されたことを検証するには、その *[Provisioning State]\(プロビジョニングの状態\)* を確認します。 プロビジョニングの状態を更新するために、1 分待ってから **[更新]** を選択することが必要な場合があります。 ADLS Gen2 アカウントへのマネージド プライベート エンドポイントが正常に作成されたことがわかります。

また、 *[承認状態]* が *[Pending]\(保留中\)* であることも確認できます。 ターゲット リソースの所有者は、プライベート エンドポイント接続要求を承認または拒否できます。 所有者がプライベート エンドポイント接続要求を承認すると、プライベート リンクが確立されます。 拒否した場合、プライベート リンクは確立されません。
![マネージド プライベート エンドポイント作成要求の状態](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>次のステップ

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)の詳細を学習する