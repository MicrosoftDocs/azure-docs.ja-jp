---
title: ドロップした Azure Database for MySQL サーバーを復元する
description: この記事では、Azure Portal を使用して、ドロップした Azure Database for MySQL サーバーを復元する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657092"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>ドロップした Azure Database for MySQL サーバーを復元する

サーバーが削除されると、データベース サーバーのバックアップをサービスで最大 5 日間保持できます。 データベースのバックアップは、元々サーバーをホストしている Azure サブスクリプションからのみアクセスおよび復元できます。 次の推奨される手順に従って、サーバーの削除から 5 日以内に、削除された MySQL サーバー リソースを復旧できます。 推奨される手順は、サーバーのバックアップがまだ使用可能であり、システムから削除されていない場合にのみ機能します。 

## <a name="pre-requisites"></a>前提条件
ドロップした Azure Database for MySQL サーバーを復元するには、次のことを行う必要があります。
- 元のサーバーをホストしている Azure サブスクリプションの名前
- サーバーが作成された場所

## <a name="steps-to-restore"></a>復元の手順

1. Azure portal のモニター ブレイドから[アクティビティ ログ](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)に移動します 

2. アクティビティ ログで、次のように **[Add filter (フィルターの追加)]** をクリックし、次のフィルターを設定します。 

    - **サブスクリプション** = 削除されたサーバーをホストしているサブスクリプション
    - **リソース タイプ** = Azure Database for MySQL servers (Microsoft.DBforMySQL/servers) 
    - **操作** = Delete MySQL Server (Microsoft.DBforMySQL/servers/delete) 
 
     [![MySQL サーバーの削除操作のためにフィルター処理されたアクティビティログ](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. [MySQL サーバーの削除] イベントをダブルクリックし、[JSON] タブをクリックして、JSON 出力の "resourceId" 属性と "submissionTimestamp" 属性を記録します。 ResourceId の形式は次のとおりです:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. 次に、[サーバー REST API の作成ページ](/rest/api/mysql/servers/create)に移動し、緑色で強調表示されている [使ってみる] タブを クリックして、Azure アカウントでログインします
 
 5. ステップ 3 でキャプチャした resourceId 属性から得られる resourceGroupName、serverName (削除されたサーバーの名前)、subscriptionId を指定します。画像に示されるように、API のバージョンはあらかじめ設定されています。
 
     [![REST API を使用したサーバーの作成](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
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

7. 応答コード 201 または 202 が表示された場合は、復元要求が正常に送信されています。 

8. サーバーの作成には、元のサーバーでプロビジョニングされたデータベースのサイズとコンピューティング リソースによって時間がかかることがあります。 復元の状態は、次をフィルター処理することによって、アクティビティ ログから監視できます。 
   - **サブスクリプション** = 自分のサブスクリプション
   - **リソース タイプ** = Azure Database for MySQL servers (Microsoft.DBforMySQL/servers) 
   - **操作** = MySQL サーバー作成の更新

## <a name="next-steps"></a>次のステップ
- 5 日以内にサーバーを復元しようとしても、前に説明した手順に正確に従ってエラーが発生する場合は、サポート インシデントを開いてください。 削除されたサーバーを 5 日後に復元しようとすると、バックアップファイルが見つからないため、エラーが発生します。 このシナリオではサポート チケットを開かないでください。 バックアップがシステムから削除された場合、サポート チームはサポートを提供できません。 
- サーバーが誤って削除されないように、[リソース ロック](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)を使用することを強くお勧めします。