---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889087"
---
> [!NOTE]
> SharePoint 用 StorSimple アダプターの RBS 構成を変更する際は、Domain Admins グループに属しているユーザー アカウントでログオンする必要があります。 さらに、サーバーの全体管理と同じホストで実行されているブラウザーから構成ページにアクセスする必要があります。
> 
> 

#### <a name="to-configure-rbs"></a>RBS を構成するには
1. [SharePoint サーバーの全体管理] ページを開き、 **[システム設定]** を参照します。 
2. **[Azure StorSimple]** セクションで、**[StorSimple アダプターの構成]** をクリックします。
   
    ![Configure the StorSimple Adapter](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. **[StorSimple アダプターの構成]** ページで、次の手順を実行します。
   
   1. **[パスの編集を有効にする]** チェック ボックスがオンになっていることを確認します。
   2. ボックスに、BLOB ストアの汎用名前付け規則 (UNC) パスを入力します。
      
      > [!NOTE]
      > BLOB ストア ボリュームは、StorSimple デバイス上で構成されている iSCSI ボリュームでホストされている必要があります。

   3. リモート記憶域用に構成する各コンテンツ データベースの下の **[有効]** をクリックします。
      
      > [!NOTE]
      > BLOB ストアは、すべての Web フロントエンド (WFE) サーバーで共有し、これらのサーバーから到達できる必要があります。また、SharePoint サーバー ファーム用に構成されているユーザー アカウントは、共有にアクセスできる必要があります。
      
      ![Enable the RBS provider](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      RBS を有効または無効にすると、次のメッセージも表示されます。
      
      ![Configure StorSimple Adapter Enable Disable](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. **[更新]** をクリックして構成を適用します。 **[更新]** をクリックすると、すべての WFE サーバー上で RBS 構成状態が更新され、ファーム全体で RBS が有効になります。 次のメッセージが表示されます。
      
      ![Adapter configuration message](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > 大量のデータベース (200 以上) がある SharePoint ファームに対して RBS を構成している場合、[SharePoint サーバーの全体管理] Web ページがタイムアウトする可能性があります。その場合は、ページを更新します。 ページを更新しても構成プロセスに影響はありません。

4. 構成を確認します。
   
   1. SharePoint サーバーの全体管理 Web サイトにサインインし、**[StorSimple アダプターの構成]** ページに移動します。
   2. 構成の詳細を表示して、入力した設定に一致しているかどうかを確認します。 
5. RBS が正しく動作することを確認します。
   
   1. ドキュメントを SharePoint にアップロードします。 
   2. 構成した UNC パスを参照します。 RBS のディレクトリ構造が作成されていること、およびアップロードしたオブジェクトがそこに含まれていることを確認します。
6. (省略可能) SharePoint に付属する Microsoft RBS `Migrate()` PowerShell コマンドレットを使用して、既存の BLOB コンテンツを StorSimple デバイスに移行できます。 詳しくは、 [コンテンツを SharePoint 2013 の RBS 外に移行する][6] または [コンテンツをリモート BLOB ストレージ (RBS) 内または RBS 外に移行する (SharePoint Foundation 2010)][7] をご覧ください。
7. (省略可能) テスト インストールで、次のように、BLOB がコンテンツ データベースから移動されたことを確認できます。 
   
   1. SQL Management Studio を起動します。
   2. 次のように、ListBlobsInDB_2010.sql クエリまたは ListBlobsInDB_2013.sql クエリを実行します。
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      RBS が正しく構成されている場合は、アップロードされて RBS によって正常に外部化されたすべてのオブジェクトについて、SizeOfContentInDB 列に NULL 値が表示されます。
8. (省略可能) RBS を構成し、すべての BLOB コンテンツを StorSimple デバイスに移動した後、コンテンツ データベースをデバイスに移動することができます。 コンテンツ データベースを移動する場合は、デバイスのコンテンツ データベース ストレージをプライマリ ボリュームとして構成することをお勧めします。 次に、SQL Server の移行に関する確立されたベスト プラクティスを使用して、コンテンツ データベースを StorSimple デバイスに移行します。 
   
   > [!NOTE]
   > デバイスへのコンテンツ データベースの移動は、StorSimple 8000 シリーズ デバイスでのみサポートされています (5000 シリーズまたは 7000 シリーズではサポートされていません)。
   
   BLOB とコンテンツ データベースを StorSimple デバイス上の別々のボリュームに保存する場合は、それらのボリュームを同じボリューム コンテナー内に構成することをお勧めします。 これにより、これらのボリュームは同時にバックアップされるようになります。
   
   > [!WARNING]
   > RBS を有効にしていない場合は、コンテンツ データベースを StorSimple デバイスに移行することはお勧めしません。 このような構成はテストされていません。
   
9. 次のステップへ進みます:[ガベージ コレクションを構成する](#configure-garbage-collection)。

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
