---
title: 読み取りレプリカを管理する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: Azure portal から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを管理する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422180"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Azure portal から Azure Database for PostgreSQL - 単一サーバーの読み取りレプリカを作成および管理する

この記事では、Azure Portal から Azure Database for PostgreSQL の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。


## <a name="prerequisites"></a>前提条件
プライマリ サーバーになる [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)。

## <a name="azure-replication-support"></a>Azure レプリケーション サポート

[読み取りレプリカ](concepts-read-replicas.md)と[論理デコード](concepts-logical.md)はどちらも、情報を Postgres 書き込み先行ログ (WAL) に依存しています。 これらの 2 つの機能には、Postgres とは異なるレベルのログが必要です。 論理デコードには、読み取りレプリカよりも高いレベルのログが必要です。

適切なレベルのログを構成するには、Azure レプリケーション サポート パラメーターを使用します。 Azure レプリケーション サポートには、次の 3 つの設定オプションがあります。

* **オフ** - 最小限の情報を WAL に格納します。 この設定は、ほとんどの Azure Database for PostgreSQL サーバーでは使用できません。  
* **レプリカ** - **[オフ]** よりも冗長です。 これは、[読み取りレプリカ](concepts-read-replicas.md)を機能させるために必要な最小レベルのログです。 ほとんどのサーバーでは、この設定が既定値です。
* **論理** - **[レプリカ]** よりも冗長です。 これは、論理デコードを機能させるための最小レベルのログです。 読み取りレプリカはこの設定でも機能します。


> [!NOTE]
> 書き込み集中型の永続的で大量のプライマリのワークロードのために読み取りレプリカをデプロイする場合、レプリケーションの遅延が増加し続け、プライマリに追いつくことができない可能性があります。 これにより、レプリカで受信されるまで WAL ファイルが削除されないため、プライマリでのストレージの使用量も増加する可能性があります。

## <a name="prepare-the-primary-server"></a>プライマリ サーバーを準備する

1. Azure portal で、マスターとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。

2. サーバーのメニューから、 **[レプリケーション]** を選択します。 Azure レプリケーション サポートが少なくとも **[レプリカ]** に設定されていれば、読み取りレプリカを作成できます。 

3. Azure レプリケーション サポートが少なくとも **[レプリカ]** に設定されていない場合は、設定します。 **[保存]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL - レプリケーション - レプリカの設定と保存":::

4. サーバーを再起動して変更を適用するには、 **[はい]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL - レプリケーション - 再起動の確認":::

5. 操作が完了すると、Azure portal の通知を 2 つ受け取ります。 1 つの通知は、サーバー パラメーターの更新に関するものです。 もう 1 つの通知は、すぐ後のサーバーの再起動に関するものです。

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="成功通知":::

6. Azure portal のページを最新の情報に更新して、レプリケーション ツール バーを更新します。 このサーバーの読み取りレプリカを作成できるようになります。
   

## <a name="create-a-read-replica"></a>読み取りレプリカを作成します
読み取りレプリカを作成するには、次の手順に従います。

1. プライマリ サーバーとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。 

2. サーバー サイドバーで、 **[設定]** の **[レプリケーション]** を選択します。

3. **[レプリカの追加]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="レプリカを追加する":::

4. 読み取りレプリカの名前を入力します。 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="レプリカに名前を付ける":::

5. レプリカの場所を選択します。 既定の場所は、プライマリ サーバーの場所と同じです。

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="場所を選択します。":::

   > [!NOTE]
   > レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

6. **[OK]** を選択して、レプリカの作成を確認します。

読み取りレプリカが作成されたら、それを **[レプリケーション]** ウィンドウから表示できます。

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="[レプリケーション] ウィンドウに新しいレプリカを表示する":::
 

> [!IMPORTANT]
> [読み取りレプリカの概要に関するページの考慮事項セクション](concepts-read-replicas.md#considerations)を確認してください。
>
> プライマリ サーバーの設定が新しい値に更新される前に、レプリカの設定をそれと同等以上の値に更新します。 このアクションは、レプリカがマスターに対するあらゆる変更に追従できるようにするのに役立ちます。

## <a name="stop-replication"></a>レプリケーションの停止
プライマリ サーバーと読み取りレプリカの間のレプリケーションを停止できます。

> [!IMPORTANT]
> プライマリ サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

Azure portal からプライマリ サーバーと読み取りレプリカの間のレプリケーションを停止するには、次の手順に従います。

1. Azure portal で、ご利用の プライマリ Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、 **[設定]** の **[レプリケーション]** を選択します。

3. レプリケーションを停止するレプリカ サーバーを選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="レプリカを選択する":::
 
4. **[レプリケーションを停止する]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="レプリケーションの停止を選択する":::
 
5. **[OK]** を選択して、レプリケーションを停止します。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="レプリケーションの停止を確認する":::
 

## <a name="delete-a-primary-server"></a>プライマリ サーバーの削除
プライマリ サーバーを削除するには、スタンドアロンの Azure Database for PostgreSQL サーバーの削除と同じ手順を使用します。 

> [!IMPORTANT]
> プライマリ サーバーを削除すると、すべての読み取りレプリカへのレプリケーションが停止されます。 読み取りレプリカは、読み取りと書き込みの両方をサポートするようになったスタンドアロン サーバーになります。

Azure Portal からサーバーを削除するには、次の手順に従います。

1. Azure portal で、ご利用の プライマリ Azure Database for PostgreSQL サーバーを選択します。

2. サーバーの **[概要]** ページを開きます。 **[削除]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="サーバーの [概要] ページで、プライマリ サーバーの削除を選択します":::
 
3. 削除するプライマリ サーバーの名前を入力します。 **[削除]** を選択して、プライマリ サーバーの削除を確認します。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="プライマリ サーバーの削除を確認する":::
 

## <a name="delete-a-replica"></a>レプリカの削除
プライマリ サーバーの削除と同様の方法で読み取りレプリカを削除できます。

- Azure Portal で、読み取りレプリカの **[概要]** ページを開きます。 **[削除]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="レプリカの [概要] ページで、レプリカの削除を選択します。":::
 
次の手順に従って、 **[レプリケーション]** ウィンドウから読み取りレプリカを削除することもできます。

1. Azure portal で、ご利用の プライマリ Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、 **[設定]** の **[レプリケーション]** を選択します。

3. 削除する読み取りレプリカを選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="削除するレプリカを選択する":::
 
4. **[レプリカの削除]** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="レプリカの削除を選択する":::
 
5. 削除するレプリカの名前を入力します。 **[削除]** を選択して、レプリカの削除を確認します。

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="レプリカの削除を確認する":::
 

## <a name="monitor-a-replica"></a>レプリカの監視
読み取りレプリカを監視するには 2 つのメトリックを使用できます。

### <a name="max-lag-across-replicas-metric"></a>Max Lag Across Replicas (レプリカ間の最大ラグ) メトリック
**Max Lag Across Replicas (レプリカ間の最大ラグ)** メトリックは、プライマリ サーバーと最も遅れているレプリカの間のラグ (バイト単位) を示します。 

1.  Azure portal で、プライマリ Azure Database for PostgreSQL サーバーを選択します。

2.  **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、 **[Max Lag Across Replicas] (レプリカ間の最大ラグ)** を選択します。

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="レプリカ間の最大ラグを監視する":::
 
3.  **[集計]** で **[Max] (最大)** を選択します。


### <a name="replica-lag-metric"></a>Replica Lag (レプリカ ラグ) メトリック
**Replica Lag (レプリカ ラグ)** メトリックは、レプリカで最後に再生されたトランザクションからの経過時間を示します。 マスターでトランザクションが発生していない場合、メトリックにはこのタイム ラグが反映されます。

1. Azure Portal で、Azure Database for PostgreSQL の読み取りレプリカを選択します。

2. **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、 **[Replica Lag] (レプリカ ラグ)** を選択します。

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="レプリカ ラグを監視する":::
 
3. **[集計]** で **[Max] (最大)** を選択します。 
 
## <a name="next-steps"></a>次のステップ
* [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。
* [Azure CLI と REST API で読み取りレプリカの作成と管理](howto-read-replicas-cli.md)を行う方法について確認してください。