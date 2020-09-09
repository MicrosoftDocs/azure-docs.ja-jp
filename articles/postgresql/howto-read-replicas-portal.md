---
title: 読み取りレプリカを管理する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: Azure portal から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを管理する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 8ca4d3d2d52e79dbcaaa15eba5794a4d2d28366a
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274545"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Azure portal から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを作成および管理する

この記事では、Azure Portal から Azure Database for PostgreSQL の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。


## <a name="prerequisites"></a>前提条件
マスター サーバーになる [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)。

## <a name="azure-replication-support"></a>Azure レプリケーション サポート

[読み取りレプリカ](concepts-read-replicas.md)と[論理デコード](concepts-logical.md)はどちらも、情報を Postgres 書き込み先行ログ (WAL) に依存しています。 これらの 2 つの機能には、Postgres とは異なるレベルのログが必要です。 論理デコードには、読み取りレプリカよりも高いレベルのログが必要です。

適切なレベルのログを構成するには、Azure レプリケーション サポート パラメーターを使用します。 Azure レプリケーション サポートには、次の 3 つの設定オプションがあります。

* **オフ** - 最小限の情報を WAL に格納します。 この設定は、ほとんどの Azure Database for PostgreSQL サーバーでは使用できません。  
* **レプリカ** - **[オフ]** よりも冗長です。 これは、[読み取りレプリカ](concepts-read-replicas.md)を機能させるために必要な最小レベルのログです。 ほとんどのサーバーでは、この設定が既定値です。
* **論理** - **[レプリカ]** よりも冗長です。 これは、論理デコードを機能させるための最小レベルのログです。 読み取りレプリカはこの設定でも機能します。

このパラメーターを変更した後、サーバーを再起動する必要があります。 内部的には、このパラメーターによって、Postgres パラメーター `wal_level`、`max_replication_slots`、および `max_wal_senders` が設定されます。

## <a name="prepare-the-master-server"></a>マスター サーバーの準備

1. Azure portal で、マスターとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。

2. サーバーのメニューから、 **[レプリケーション]** を選択します。 Azure レプリケーション サポートが少なくとも **[レプリカ]** に設定されていれば、読み取りレプリカを作成できます。 

3. Azure レプリケーション サポートが少なくとも **[レプリカ]** に設定されていない場合は、設定します。 **[保存]** を選択します。

   ![Azure Database for PostgreSQL - レプリケーション - レプリカの設定と保存](./media/howto-read-replicas-portal/set-replica-save.png)

4. サーバーを再起動して変更を適用するには、 **[はい]** を選択します。

   ![Azure Database for PostgreSQL - レプリケーション - 再起動の確認](./media/howto-read-replicas-portal/confirm-restart.png)

5. 操作が完了すると、Azure portal の通知を 2 つ受け取ります。 1 つの通知は、サーバー パラメーターの更新に関するものです。 もう 1 つの通知は、すぐ後のサーバーの再起動に関するものです。

   ![成功通知](./media/howto-read-replicas-portal/success-notifications.png)

6. Azure portal のページを最新の情報に更新して、レプリケーション ツール バーを更新します。 このサーバーの読み取りレプリカを作成できるようになります。
   

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します
読み取りレプリカを作成するには、次の手順に従います。

1. マスター サーバーとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。 

2. サーバー サイドバーで、 **[設定]** の **[レプリケーション]** を選択します。

3. **[レプリカの追加]** を選択します。

   ![レプリカを追加する](./media/howto-read-replicas-portal/add-replica.png)

4. 読み取りレプリカの名前を入力します。 

    ![レプリカに名前を付ける](./media/howto-read-replicas-portal/name-replica.png)

5. レプリカの場所を選択します。 既定の場所は、マスター サーバーの場所と同じです。

    ![場所を選択します。](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

6. **[OK]** を選択して、レプリカの作成を確認します。

読み取りレプリカが作成されたら、それを **[レプリケーション]** ウィンドウから表示できます。

![[レプリケーション] ウィンドウに新しいレプリカを表示する](./media/howto-read-replicas-portal/list-replica.png)
 

> [!IMPORTANT]
> [読み取りレプリカの概要に関するページの考慮事項セクション](concepts-read-replicas.md#considerations)を確認してください。
>
> マスター サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

## <a name="stop-replication"></a>レプリケーションの停止
マスター サーバーと読み取りレプリカの間のレプリケーションを停止できます。

> [!IMPORTANT]
> マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

Azure Portal からマスター サーバーと読み取りレプリカの間のレプリケーションを停止するには、次の手順に従います。

1. Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、 **[設定]** の **[レプリケーション]** を選択します。

3. レプリケーションを停止するレプリカ サーバーを選択します。

   ![レプリカを選択する](./media/howto-read-replicas-portal/select-replica.png)
 
4. **[レプリケーションを停止する]** を選択します。

   ![レプリケーションの停止を選択する](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. **[OK]** を選択して、レプリケーションを停止します。

   ![レプリケーションの停止を確認する](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>マスター サーバーを削除します
マスター サーバーを削除するには、スタンドアロンの Azure Database for PostgreSQL サーバーの削除と同じ手順を使用します。 

> [!IMPORTANT]
> マスター サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

Azure Portal からサーバーを削除するには、次の手順に従います。

1. Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2. サーバーの **[概要]** ページを開きます。 **[削除]** を選択します。

   ![サーバーの [概要] ページで、マスター サーバーの削除を選択します。](./media/howto-read-replicas-portal/delete-server.png)
 
3. 削除するマスター サーバーの名前を入力します。 **[削除]** を選択して、マスター サーバーの削除を確認します。

   ![マスター サーバーの削除を確認する](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>レプリカの削除
マスター サーバーの削除と同様の方法で読み取りレプリカを削除できます。

- Azure Portal で、読み取りレプリカの **[概要]** ページを開きます。 **[削除]** を選択します。

   ![レプリカの [概要] ページで、レプリカの削除を選択します。](./media/howto-read-replicas-portal/delete-replica.png)
 
次の手順に従って、 **[レプリケーション]** ウィンドウから読み取りレプリカを削除することもできます。

1. Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、 **[設定]** の **[レプリケーション]** を選択します。

3. 削除する読み取りレプリカを選択します。

   ![削除するレプリカを選択する](./media/howto-read-replicas-portal/select-replica.png)
 
4. **[レプリカの削除]** を選択します。

   ![レプリカの削除を選択する](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. 削除するレプリカの名前を入力します。 **[削除]** を選択して、レプリカの削除を確認します。

   ![レプリカの削除を確認する](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>レプリカの監視
読み取りレプリカを監視するには 2 つのメトリックを使用できます。

### <a name="max-lag-across-replicas-metric"></a>Max Lag Across Replicas (レプリカ間の最大ラグ) メトリック
**Max Lag Across Replicas (レプリカ間の最大ラグ)** メトリックは、マスター サーバーと最も遅れているレプリカの間のラグ (バイト単位) を示します。 

1.  Azure Portal で、マスターの Azure Database for PostgreSQL サーバーを選択します。

2.  **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、 **[Max Lag Across Replicas] (レプリカ間の最大ラグ)** を選択します。

    ![レプリカ間の最大ラグを監視する](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  **[集計]** で **[Max] (最大)** を選択します。


### <a name="replica-lag-metric"></a>Replica Lag (レプリカ ラグ) メトリック
**Replica Lag (レプリカ ラグ)** メトリックは、レプリカで最後に再生されたトランザクションからの経過時間を示します。 マスターでトランザクションが発生していない場合、メトリックにはこのタイム ラグが反映されます。

1. Azure Portal で、Azure Database for PostgreSQL の読み取りレプリカを選択します。

2. **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、 **[Replica Lag] (レプリカ ラグ)** を選択します。

   ![レプリカ ラグを監視する](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. **[集計]** で **[Max] (最大)** を選択します。 
 
## <a name="next-steps"></a>次のステップ
* [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。
* [Azure CLI と REST API で読み取りレプリカの作成と管理](howto-read-replicas-cli.md)を行う方法について確認してください。