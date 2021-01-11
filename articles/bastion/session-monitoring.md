---
title: Azure Bastion のセッションの監視と管理 |Microsoft Docs
description: この記事では、進行中のセッションを選択し、それを強制切断または削除する方法について説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: 5974ebe7960eec1ca3bb8610f66061395fea64d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744104"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure Bastion のセッションの監視と管理

Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM にシームレスに接続できます。 ユーザーがワークロードに接続すると、Azure Bastion を使用してリモート セッションを監視し、迅速な管理操作を実行できます。 Azure Bastion のセッションの監視では、どのユーザーがどの VM に接続されているかを表示できます。 これには、ユーザーの接続元の IP、接続されている時間、および接続時刻が表示されます。 セッション管理エクスペリエンスでは、進行中のセッションを選択し、セッションを強制的に切断または削除して、実行中のセッションからユーザーを切断することができます。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>リモート セッションを監視する

1. [Azure portal](https://portal.azure.com) で、Azure Bastion リソースに移動し、Azure Bastion ページから **[Sessions]\(セッション\)** を選択します。

   ![セッション](./media/session-monitoring/sessions.png)
2. **[Sessions]\(セッション\)** ページの右側に、進行中のリモート セッションが表示されます。

   ![セッションの表示](./media/session-monitoring/view-session.png)
3. **[Refresh]\(更新\)** を選択して、更新されたリモート セッションの一覧を表示します。 [Refresh]\(更新\) を選択すると、Azure Bastion は最新の監視情報を取得し、ポータルで更新します。

   ![更新](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>実行中のリモート セッションを削除または強制切断する

セッションのセットを選択して、強制的に切断できます。 次の手順では、リモート セッションを削除する方法について説明します。

1. Azure Bastion リソースに移動し、Azure Bastion ページから **[Sessions]\(セッション\)** を選択します。

   ![移動](./media/session-monitoring/navigate.png)
2. [Sessions]\(セッション\) を選択すると、リモート セッションの一覧が表示されます。

   ![セッションの一覧](./media/session-monitoring/list.png)
3. 特定のリモート セッションを選択してから、セッション行の右端にある 3 つの省略記号を選択し、 **[Delete]\(削除\)** を選択します。

   ![delete](./media/session-monitoring/delete.png)
4. [Delete]\(削除\) を選択すると、リモート セッションが切断され、リモート セッションでユーザーに "切断されました" というメッセージが表示されます。

   ![切断](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
