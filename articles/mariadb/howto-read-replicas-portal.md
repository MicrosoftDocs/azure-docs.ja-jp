---
title: Azure Database for MariaDB の読み取りレプリカの作成と管理
description: この記事では、ポータルを使用して Azure Database for MariaDB の読み取りレプリカを設定し、管理する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: eb228138118512c5c64574212910c5f16885ee94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081644"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure portal を使用して Azure Database for MariaDB の読み取りレプリカを作成および管理する方法

この記事では、Azure portal を使用して Azure Database for MariaDB サービスの読み取りレプリカを作成および管理する方法を学習します。

> [!IMPORTANT]
> 同一リージョンの読み取りレプリカは、現在パブリック プレビュー段階です。

## <a name="prerequisites"></a>前提条件

- マスター サーバーとして使用される [Azure Database for MariaDB サーバー ](quickstart-create-mariadb-server-database-using-azure-portal.md)。

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用またはメモリ最適化の価格レベルの Azure Database for MariaDB サーバーにのみ使用可能です。 マスター サーバーがこれらの価格レベルのいずれかを確認します。

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します

読み取りレプリカ サーバーは、次の手順を使用して作成できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. マスターとして使用する既存の Azure Database for MariaDB サーバーを選択します。 この操作で、 **[概要]** ページが開きます。

3. **[設定]** で、メニューから **[レプリケーション]** を選択します。

4. **[レプリカの追加]** を選択します。

   ![Azure Database for MariaDB - レプリケーション](./media/howto-read-replica-portal/add-replica.png)

5. レプリカ サーバーの名前を入力します。

    ![Azure Database for MariaDB - レプリカ名](./media/howto-read-replica-portal/replica-name.png)

6. **[OK]** を選択して、レプリカの作成を確認します。

> [!NOTE]
> マスターと同じサーバー構成で、読み取りレプリカが作成されます。 作成された後、レプリカ サーバーの構成を変更できます。 レプリカをマスターと維持できるようにするために、レプリカ サーバーの構成をマスターと同じかそれ以上の値にしておくようお勧めします。

レプリカ サーバーを作成すると、**レプリケーション** ブレードから表示できます。

   ![Azure Database for MariaDB - レプリカの一覧](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>レプリカ サーバーへのレプリケーションを停止します。

> [!IMPORTANT]
> サーバーへのレプリケーションの停止は、元に戻すことができません。 マスターとレプリカの間のレプリケーションを停止すると、取り消すことができません。 レプリカ サーバーはスタンドアロン サーバーになり、読み取りと書き込みをサポートするようになります。 このサーバーは、レプリカにもう一度確立することはできません。

Azure Portal からマスターとレプリカ サーバー間のレプリケーションを停止するには、次の手順を使用します。

1. Azure portal で、マスターの Azure Database for MariaDB サーバーを選択します。 

2. **[設定]** で、メニューから **[レプリケーション]** を選択します。

3. レプリケーションを停止するレプリカ サーバーを選択します。

   ![Azure Database for MariaDB - レプリケーションを停止するサーバーの選択](./media/howto-read-replica-portal/stop-replication-select.png)

4. **[レプリケーションを停止する]** を選択します。

   ![Azure Database for MariaDB - レプリケーションの停止](./media/howto-read-replica-portal/stop-replication.png)

5. **[OK]** をクリックして停止したいレプリケーションを確認します。

   ![Azure Database for MariaDB - レプリケーション停止の確認](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを Azure Portal から削除するには、次の手順を使用します。

1. Azure portal で、マスターの Azure Database for MariaDB サーバーを選択します。

2. **[設定]** で、メニューから **[レプリケーション]** を選択します。

3. 削除するレプリカ サーバーを選択します。

   ![Azure Database for MariaDB - レプリカを削除するサーバーの選択](./media/howto-read-replica-portal/delete-replica-select.png)

4. **[レプリカの削除]** を選択します

   ![Azure Database for MariaDB - レプリカの削除](./media/howto-read-replica-portal/delete-replica.png)

5. レプリカの名前を入力して、 **[削除]** をクリックし、レプリカの削除を確定します。  

   ![Azure Database for MariaDB - レプリカ削除の確定](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>マスター サーバーを削除します

> [!IMPORTANT]
> マスター サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、マスター サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

マスター サーバーを Azure Portal から削除するには、次の手順を使用します。

1. Azure portal で、マスターの Azure Database for MariaDB サーバーを選択します。

2. **[概要]** ページから **[削除]** を選択します。

   ![Azure Database for MariaDB - マスターの削除](./media/howto-read-replica-portal/delete-master-overview.png)

3. マスター サーバーの名前を入力して、 **[削除]** をクリックし、マスターの削除を確定します。  

   ![Azure Database for MariaDB - マスターの削除](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>レプリケーションを監視します

1. [Azure Portal](https://portal.azure.com/) で、監視するレプリカ Azure Database for MariaDB サーバーを選択します。

2. サイドバーの **[監視]** セクションで、 **[メトリック]** を選択します。

3. 利用可能なメトリックのドロップダウンリストから、 **秒単位のレプリケーションのラグ** を選択します。

   ![レプリケーションのラグを選択します](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. 表示する時間の範囲を選択します。 次の図では、30 分間の時間範囲を選択しています。

   ![時間範囲を選択します](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. 選択した時間範囲のレプリケーションのラグを表示します。 次の図では、大規模なワークロードの過去 30 分間が表示されます。

   ![時間範囲を選択します](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>次の手順

- [レプリカの読み取り](concepts-read-replicas.md) の詳細を確認する