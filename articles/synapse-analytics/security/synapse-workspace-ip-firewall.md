---
title: IP ファイアウォール規則を構成する
description: Azure Synapse Analytics で IP ファイアウォール規則を構成する方法を説明した記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 787e600df4185346b250d9a96bdb89927e134075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472014"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Azure Synapse Analytics の IP ファイアウォール規則

この記事では、IP ファイアウォール規則について説明し、それを Azure Synapse Analytics で構成する方法を示します。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則

IP ファイアウォール規則は、各要求の発信元 IP アドレスに基づいて Synapse ワークスペースへのアクセス権を付与または拒否します。 お使いのワークスペースに対して IP ファイアウォール規則を構成できます。 ワークスペース レベルで構成された IP ファイアウォール規則は、ワークスペースのすべてのパブリック エンドポイント (専用 SQL プール、サーバーレス SQL プール、開発) に適用されます。

## <a name="create-and-manage-ip-firewall-rules"></a>IP ファイアウォール規則の作成および管理

IP ファイアウォール規則を Synapse ワークスペースに追加するには、2 つの方法があります。 IP ファイアウォールをワークスペースに追加するには、 **[Security + networking]\(セキュリティ + ネットワーク\)** を選択し、ワークスペースの作成時に **[Allow connections from all IP addresses]\(すべての IP アドレスからの接続を許可する\)** をオンにします。

![[Security + networking]\(セキュリティ + ネットワーク\) ボタンが強調表示されているスクリーンショット。](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Azure portal での Synapse ワークスペース IP の構成。](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

また、ワークスペースの作成後に、Synapse ワークスペースに IP ファイアウォール規則を追加することもできます。 Azure portal の **[セキュリティ]** の下で **[ファイアウォール]** を選択します。 新しい IP ファイアウォール規則を追加するには、それに名前、開始 IP、および終了 IP を指定します。 完了したら、 **[保存]** を選択します。

![Azure portal での Azure Synapse ワークスペース IP の構成。](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>独自のネットワークから Synapse に接続する

Synapse Studio を使用して、Synapse ワークスペースに接続できます。 また、SQL Server Management Studio (SSMS) を使用して、ワークスペース内の SQL リソース (専用 SQL プールとサーバーレス SQL プール) に接続することもできます。

ネットワークおよびローカル コンピューターのファイアウォールで、Synapse Studio に対して TCP ポート 80、443、および 1443 での送信通信を許可するように設定してください。

また、Synapse Studio に対して UDP ポート 53 での送信通信を許可する必要があります。 SSMS や Power BI などのツールを使用して接続するには、TCP ポート1433での送信通信を許可する必要があります。


## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース](../quickstart-create-workspace.md)を作成する

[マネージド ワークスペース仮想ネットワーク](./synapse-workspace-managed-vnet.md)を使用して Azure Synapse ワークスペースを作成する