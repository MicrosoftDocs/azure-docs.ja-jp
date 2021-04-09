---
title: 読み取りレプリカの管理 - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを設定し、管理する方法について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: d33734dc7404e49aed94dffae8644b2bc4386925
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492830"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを作成し、管理する方法

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーの読み取りレプリカは、プレビュー段階です。

この記事では、Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの読み取りレプリカを作成および管理する方法について説明します。

> [!Note]
> 高可用性が有効になっているサーバーでは、レプリカはサポートされていません。 

## <a name="prerequisites"></a>前提条件

- ソース サーバーとして使用される [Azure Database for MySQL サーバーのフレキシブル サーバー](quickstart-create-server-portal.md)。

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
> 既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次の手順を使用して作成できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. ソースとして使用する既存の Azure Database for MySQL フレキシブル サーバーを選択します。 この操作で、 **[概要]** ページが開きます。

3. **[設定]** で、メニューから **[レプリケーション]** を選択します。

4. **[レプリカの追加]** を選択します。

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - レプリケーション":::

5. レプリカ サーバーの名前を入力します。

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - レプリカ名":::

6. **[OK]** を選択して、レプリカの作成を確認します。

> [!NOTE]
> ソースと同じサーバー構成で、読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 レプリカ サーバーは、ソース サーバーと同じリソース グループ、同じ場所、および同じサブスクリプションに常に作成されます。 レプリカ サーバーを別のリソース グループや別のサブスクリプションに作成したい場合は、作成後に[レプリカ サーバーを移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)します。 レプリカが確実にソースに追随できるように、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくことをお勧めします。

レプリカ サーバーを作成すると、**レプリケーション** ブレードから表示できます。

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - レプリカの一覧":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。

> [!IMPORTANT]
> サーバーへのレプリケーションの停止は、元に戻すことができません。 ソースとレプリカの間のレプリケーションを停止すると、元に戻すことはできません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーをもう一度レプリカにすることはできません。

Azure Portal からソースとレプリカ サーバー間のレプリケーションを停止するには、次の手順を使用します。

1. Azure portal で、ソースの Azure Database for MySQL フレキシブル サーバーを選択します。 

2. **[設定]** で、メニューから **[レプリケーション]** を選択します。

3. レプリケーションを停止するレプリカ サーバーを選択します。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - レプリケーション選択サーバーの停止":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. **[レプリケーションを停止する]** を選択します。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - レプリケーションの停止":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. **[OK]** をクリックして停止したいレプリケーションを確認します。

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - レプリケーション確認の停止":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを Azure Portal から削除するには、次の手順を使用します。

1. Azure portal で、ソースの Azure Database for MySQL フレキシブル サーバーを選択します。

2. **[設定]** で、メニューから **[レプリケーション]** を選択します。

3. 削除するレプリカ サーバーを選択します。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - レプリカ選択サーバーの停止":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. **[レプリカの削除]** を選択します

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - レプリカの削除":::

5. レプリカの名前を入力して、 **[削除]** をクリックし、レプリカの削除を確定します。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - レプリカ確定の削除":::

## <a name="delete-a-source-server"></a>ソース サーバーを削除する

> [!IMPORTANT]
> ソース サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、ソース サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

ソース サーバーを Azure Portal から削除するには、次の手順を使用します。

1. Azure portal で、ソースの Azure Database for MySQL フレキシブル サーバーを選択します。

2. **[概要]** ページから **[削除]** を選択します。

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL - ソースの削除":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. ソース サーバーの名前を入力して、 **[削除]** をクリックし、ソース サーバーの削除を確定します。  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - ソースの削除の確認":::

## <a name="monitor-replication"></a>レプリケーションを監視します

1. [Azure portal](https://portal.azure.com/) で、監視するレプリカの Azure Database for MySQL フレキシブル サーバーを選択します。

2. サイドバーの **[監視]** セクションで、 **[メトリック]** を選択します。

3. 利用可能なメトリックのドロップダウンリストから、 **秒単位のレプリケーションのラグ** を選択します。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="レプリケーションのラグを選択します":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. 表示する時間の範囲を選択します。 次の図では、30 分間の時間範囲を選択しています。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="時間範囲を選択します":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. 選択した時間範囲のレプリケーションのラグを表示します。 次の図では、過去 30 分間が表示されます。

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="30 分の時間範囲を選択する":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>次のステップ

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する