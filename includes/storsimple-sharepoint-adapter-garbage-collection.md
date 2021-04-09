---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9c734ff03b1cf277c7e0967d8b76b1941434f414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050340"
---
ここでは、次の操作を行います。

1. [Maintainer の実行可能ファイルを実行する準備をします](#to-prepare-to-run-the-maintainer) 。
2. [孤立した BLOB をすぐに削除するようにコンテンツ データベースとごみ箱を準備します](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)。
3. [Maintainer.exe を実行します](#to-run-the-maintainer)。
4. [コンテンツ データベースとごみ箱の設定を元に戻します](#to-revert-the-content-database-and-recycle-bin-settings)。

#### <a name="to-prepare-to-run-the-maintainer"></a>Maintainer の実行を準備するには
1. Web フロントエンド サーバーで、SharePoint 2013 管理シェルを管理者として開きます。
2. *ブート ドライブ*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\. フォルダーに移動します。
3. **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** の名前を **web.config** に変更します。
4. `aspnet_regiis -pdf connectionStrings` を使用して、web.config ファイルの暗号化を解除します。
5. 暗号化が解除された web.config ファイルで、 `connectionStrings` ノードの下に、SQL Server インスタンスの接続文字列とコンテンツ データベース名を追加します。 次の例を参照してください。
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. `aspnet_regiis –pef connectionStrings` を使用して、web.config ファイルを再び暗号化します。 
7. web.config の名前を Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config に変更します。 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>孤立した BLOB をすぐに削除するようにコンテンツ データベースとごみ箱を準備するには
1. SQL Server の SQL Management Studio で、対象のコンテンツ データベースに対して次の更新クエリを実行します。 

    `use WSS_Content`

    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. Web フロントエンド サーバーの **[サーバーの全体管理]** で、目的のコンテンツ データベースの **[Web アプリケーションの全般設定]** を編集して、一時的にごみ箱を無効にします。 この操作を実行すると、関連するすべてのサイト コレクションのごみ箱も空になります。 そのためには、 **[サーバーの全体管理]**  ->  **[アプリケーション管理]**  ->  **[Web アプリケーション (Web アプリケーションの管理)]**  ->  **[SharePoint - 80]**  ->  **[アプリケーションの全般設定]** の順にクリックします。 **[ごみ箱の状態]** を **[オフ]** に設定します。
   
    ![[Web アプリケーションの全般設定]](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Maintainer を実行するには
* Web フロントエンド サーバーの SharePoint 2013 管理シェルで、次のように Maintainer を実行します。
  
    `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > 現時点では、StorSimple でサポートされているのは `GarbageCollection` 操作のみです。 また、Microsoft.Data.SqlRemoteBlobs.Maintainer.exe に対して発行されたパラメーターでは大文字と小文字が区別されることにも注意してください。 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>コンテンツ データベースとごみ箱の設定を元に戻すには
1. SQL Server の SQL Management Studio で、対象のコンテンツ データベースに対して次の更新クエリを実行します。
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Web フロントエンド サーバーの **[サーバーの全体管理]** で、目的のコンテンツ データベースの **[Web アプリケーションの全般設定]** を編集して、ごみ箱を再び有効にします。 そのためには、 **[サーバーの全体管理]**  ->  **[アプリケーション管理]**  ->  **[Web アプリケーション (Web アプリケーションの管理)]**  ->  **[SharePoint - 80]**  ->  **[アプリケーションの全般設定]** の順にクリックします。 [ごみ箱の状態] を **[オン]** に設定します。

