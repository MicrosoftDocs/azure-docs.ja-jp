---
title: ドロップした Azure Database for PostgreSQL サーバーを復元する
description: この記事では、Azure Portal を使用して Azure Database for PostgreSQL のドロップしたサーバーを復元する方法について説明します。
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652927"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>ドロップした Azure Database for PostgreSQL サーバーを復元する

サーバーが削除されると、データベース サーバーのバックアップをサービスで最大 5 日間保持できます。 データベースのバックアップは、元々サーバーをホストしている Azure サブスクリプションからのみアクセスおよび復元できます。 次の推奨される手順に従って、サーバーの削除時点から 5 日以内に、削除された PostgreSQL サーバー リソースを復旧できます。 推奨される手順は、サーバーのバックアップがまだ使用可能であり、システムから削除されていない場合にのみ機能します。 

## <a name="pre-requisites"></a>前提条件
ドロップした Azure Database for PostgreSQL サーバーを復元するには、次のことを行う必要があります。
- 元のサーバーをホストしている Azure サブスクリプションの名前
- サーバーが作成された場所

## <a name="steps-to-restore"></a>復元の手順

1. [Azure ポータル](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)にアクセスします。 **Azure Monitor** サービスを選択し、 **[アクティビティ ログ]** を選択します。

2. [アクティビティ ログ] で、次のように **[フィルターの追加]** をクリックし、次のフィルターを設定します。

    - **サブスクリプション** = 削除されたサーバーをホストしているサブスクリプション
    - **リソースの種類** = Azure Database for PostgreSQL サーバー (Microsoft.DBforPostgreSQL/servers)
    - **操作** = [Delete PostgreSQL Server]\(PostgreSQL Server の削除\) (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![PostgreSQL サーバーの削除操作のためにフィルター処理されたアクティビティ ログ](./media/howto-restore-dropped-server/activity-log-azure.png)

3. **PostgreSQL サーバーの削除** イベントを選択し、 **[JSON] タブ** を選択します。JSON 出力の `resourceId` 属性と `submissionTimestamp` 属性をコピーします。 resourceId の形式は次のとおりです: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver`。


 4. PostgreSQL の [[Create Server REST API]\(サーバー REST API の作成\) ページ](/rest/api/PostgreSQL/servers/create)を参照し、緑色で強調表示されている **[試してみる]** タブを選択します。 Azure のアカウントを使用してサインインします。

 5. 前の手順 3 でキャプチャした resourceId 属性の JSON 値に基づいて、**resourceGroupName**、**serverName** (削除されたサーバー名)、**subscriptionId** の各プロパティを指定します。 次の図に示すように、api-version プロパティはあらかじめ設定されており、そのまま残すことができます。

    ![REST API を使用したサーバーの作成](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. [要求本文] セクションまで下にスクロールし、以下を貼り付けて、削除されたサーバーの場所、submissionTimestamp、resourceId を置き換えます。 restorePointInTime については、コマンドがエラーにならないように、submissionTimestamp の値から **15 分** だけ引いた値を指定します。
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    たとえば、現在の時刻が 2020-11-02T23:59:59.0000000Z の場合は、過去の復元ポイントの 15 分以上前の時刻の 2020-11-02T23:44:59.0000000Z を指定することをお勧めします。

    > [!Important]
    > サーバーがドロップされてから 5 日間の時間制限があります。 5 日が経過すると、バックアップ ファイルが見つからないため、エラーが発生します。
    
7. 応答コード 201 または 202 が表示された場合は、復元要求が正常に送信されています。 

    サーバーの作成には、元のサーバーでプロビジョニングされたデータベースのサイズとコンピューティング リソースによって時間がかかることがあります。 復元の状態は、次をフィルター処理することによって、アクティビティ ログから監視できます。 
   - **サブスクリプション** = 自分のサブスクリプション
   - **リソースの種類** = Azure Database for PostgreSQL サーバー (Microsoft.DBforPostgreSQL/servers) 
   - **操作** = [Update PostgreSQL Server Create]\(PostgreSQL Server 作成の更新\)

## <a name="next-steps"></a>次のステップ
- 5 日以内にサーバーを復元しようとしても、前に説明した手順に正確に従ってエラーが発生する場合は、サポート インシデントを開いてください。 削除されたサーバーを 5 日後に復元しようとすると、バックアップファイルが見つからないため、エラーが発生します。 このシナリオではサポート チケットを開かないでください。 バックアップがシステムから削除された場合、サポート チームはサポートを提供できません。 
- サーバーが誤って削除されないように、[リソース ロック](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)を使用することを強くお勧めします。
