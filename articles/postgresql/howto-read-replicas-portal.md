---
title: Azure Portal から Azure Database for PostgreSQL の読み取りレプリカを管理する
description: Azure Portal から Azure Database for PostgreSQL の読み取りレプリカを管理する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787665"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Azure Portal から読み取りレプリカを作成および管理する

この記事では、Azure Portal から Azure Database for PostgreSQL の読み取りレプリカを作成および管理する方法について説明します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。


## <a name="prerequisites"></a>前提条件
マスター サーバーになる [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)。

## <a name="prepare-the-master-server"></a>マスター サーバーの準備
汎用またはメモリ最適化レベルにあるマスター サーバーを準備するには、次の手順を使用する必要があります。 マスター サーバーをレプリケーション用に準備するには、azure.replication_support パラメーターを設定します。 レプリケーション パラメーターを変更した場合、その変更を有効にするにはサーバーの再起動が必要です。 Azure portal では、1 つのボタン **[レプリケーションのサポートを有効にします]** に、これら 2 つのステップがカプセル化されています。

1. Azure Portal で、マスターとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。

2. サーバー サイドバーで、**[設定]** の **[レプリケーション]** を選択します。

3. **[レプリケーションのサポートを有効にします]** を選択します。 

   ![レプリケーションのサポートを有効にする](./media/howto-read-replicas-portal/enable-replication-support.png)

4. レプリケーションのサポートを有効にすることを確認します。 この操作では、マスター サーバーが再起動されます。 

   ![レプリケーションのサポートを有効にすることを確認する](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. 操作が完了すると、Azure portal の通知を 2 つ受け取ります。 1 つの通知は、サーバー パラメーターの更新に関するものです。 もう 1 つの通知は、すぐ後のサーバーの再起動に関するものです。

   ![成功通知 - 有効](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Azure portal のページを最新の情報に更新して、レプリケーション ツール バーを更新します。 このサーバーの読み取りレプリカを作成できるようになります。

   ![更新されたツール バー](./media/howto-read-replicas-portal/updated-toolbar.png)
   
レプリケーションのサポートを有効にするのは、マスター サーバーごとに 1 回限りの操作です。 **[レプリケーションのサポートを無効にします]** ボタンは、利便性のために用意されています。 このマスター サーバーでレプリカを二度と作成しないことが確実な場合を除き、レプリケーションのサポートを無効にすることはお勧めしません。 マスター サーバーに既存のレプリカがある間は、レプリケーションのサポートを無効できません。


## <a name="create-a-read-replica"></a>読み取りレプリカを作成します
読み取りレプリカを作成するには、次の手順に従います。

1. マスター サーバーとして使用する既存の Azure Database for PostgreSQL サーバーを選択します。 

2. サーバー サイドバーで、**[設定]** の **[レプリケーション]** を選択します。

3. **[レプリカの追加]** を選択します。

   ![レプリカを追加する](./media/howto-read-replicas-portal/add-replica.png)

4. 読み取りレプリカの名前を入力します。 **[OK]** を選択して、レプリカの作成を確認します。

   ![レプリカに名前を付ける](./media/howto-read-replicas-portal/name-replica.png) 

レプリカは、マスターと同じサーバー構成を使用して作成されます。 レプリカが作成されたら、マスター サーバーとは独立にいくつかの設定 (コンピューティング世代、仮想コア、ストレージ、およびバックアップ保持期間) を変更できます。 価格レベルも独立して変更できます (Basic レベルへの変更や Basic レベルからの変更を除く)。

> [!IMPORTANT]
> マスター サーバー構成が新しい値に更新される前に、レプリカ構成をそれと同等以上の値に更新してください。 このアクションにより、レプリカがマスターのどのような変更にも追従できるようになります。

読み取りレプリカが作成されたら、それを **[レプリケーション]** ウィンドウから表示できます。

![[レプリケーション] ウィンドウに新しいレプリカを表示する](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>レプリケーションの停止
マスター サーバーと読み取りレプリカの間のレプリケーションを停止できます。

> [!IMPORTANT]
> マスター サーバーと読み取りレプリカへのレプリケーションを停止した後、それを元に戻すことはできません。 読み取りレプリカは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。 スタンドアロン サーバーをもう一度レプリカにすることはできません。

Azure Portal からマスター サーバーと読み取りレプリカの間のレプリケーションを停止するには、次の手順に従います。

1. Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、**[設定]** の **[レプリケーション]** を選択します。

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
 
次の手順に従って、**[レプリケーション]** ウィンドウから読み取りレプリカを削除することもできます。

1. Azure portal で、ご利用のマスター Azure Database for PostgreSQL サーバーを選択します。

2. サーバー メニューで、**[設定]** の **[レプリケーション]** を選択します。

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

2.  **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、**[Max Lag Across Replicas] (レプリカ間の最大ラグ)** を選択します。

    ![レプリカ間の最大ラグを監視する](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  **[集計]** で **[Max] (最大)** を選択します。


### <a name="replica-lag-metric"></a>Replica Lag (レプリカ ラグ) メトリック
**Replica Lag (レプリカ ラグ)** メトリックは、レプリカで最後に再生されたトランザクションからの経過時間を示します。 マスターでトランザクションが発生していない場合、メトリックにはこのタイム ラグが反映されます。

1. Azure Portal で、Azure Database for PostgreSQL の読み取りレプリカを選択します。

2. **[メトリック]** を選びます。 **[メトリック]** ウィンドウで、**[Replica Lag] (レプリカ ラグ)** を選択します。

   ![レプリカ ラグを監視する](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. **[集計]** で **[Max] (最大)** を選択します。 
 
## <a name="next-steps"></a>次の手順
[Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認してください。