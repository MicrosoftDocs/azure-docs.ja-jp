---
title: "Azure Portal: Azure SQL データベースのバックアップと復元 | Microsoft Docs"
description: "このチュートリアルでは、自動バックアップから特定の時点に復元する方法、自動バックアップを Azure Recovery Services コンテナーに保存する方法、Azure Recovery Services コンテナーから復元する方法について説明します。"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 797d91b21fcd71672890c6c77bc81eadd73b47ff
ms.lasthandoff: 03/10/2017


---
# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-the-azure-portal"></a>チュートリアル: Azure Portal を使用した Azure SQL データベースのバックアップと復元
このチュートリアルでは、Azure ポータルを使用して次のことを行う方法を説明します。

- データベースの既存のバックアップを表示する
- データベースを以前の状態に復元する
- Azure Recovery Services コンテナーのデータベース バックアップ ファイルの長期的な保有期間を構成する
- Azure Recovery Services コンテナーからバックアップを復元する

**推定所要時間**: このチュートリアルの完了には約 30 分かかります (既に前提条件を満たしていることが前提です)。

> [!TIP]
> これらのタスクは、概要チュートリアルで [PowerShell](sql-database-get-started-backup-recovery-powershell.md) を使用して実行することができます。
>

## <a name="prerequisites"></a>前提条件

* **Azure アカウント**。 Azure アカウントが必要です。 [無料の Azure アカウントを作成する](https://azure.microsoft.com/free/)か、[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)ことができます。 

* **Azure の作成のアクセス許可**。 サブスクリプションの所有者または共同作成者ロールのメンバーであるアカウントを使用して Azure Portal に接続できることが必要です。 ロールベースのアクセス制御 (RBAC) の詳細については、「[Azure Portal でのアクセス管理の概要](../active-directory/role-based-access-control-what-is.md)」を参照してください。

* **SQL Server Management Studio**。 最新バージョンの SQL Server Management Studio (SSMS) は、「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」からダウンロードしてインストールすることができます。 新機能が継続的にリリースされているため、Azure SQL Database に接続する場合は、常に最新バージョンの SSMS を使用してください。

* **基本サーバーおよびデータベース**。このチュートリアルで使用するサーバーと&2; つのデータベースをインストールして構成するには、**[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。 ボタンをクリックすると **[Deploy from a template (テンプレートからのデプロイ)]** ブレードが開くので、新しいリソース グループを作成し、作成予定の新しいサーバーの **[管理者ログイン パスワード]** を指定します。

   [![ダウンロード](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fsqldbtutorial.blob.core.windows.net%2Ftemplates%2Fsqldbgetstarted.json)


> [!NOTE]
> このチュートリアルでは、[SQL Database のバックアップ](sql-database-automated-backups.md)、[長期的なバックアップ保有期間](sql-database-long-term-retention.md)、および[自動データベース バックアップを使用した Azure SQL データベースの回復](sql-database-recovery-using-backups.md)という学習トピックの内容を理解する際に役立ちます。
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Azure アカウントを使用して Azure Portal にサインインする
[既存のサブスクリプション](https://account.windowsazure.com/Home/Index)を使用して、次の手順に従って Azure Portal に接続します。

1. 任意のブラウザーを開き、 [Azure ポータル](https://portal.azure.com/)に接続します。
2. [Azure ポータル](https://portal.azure.com/)にサインインします。
3. **[サインイン]** ページが表示されたら、サブスクリプションの資格情報を入力します。
   
   ![[サインイン]](./media/sql-database-get-started/login.png)

<a name="create-logical-server-bk"></a>

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>サービスによって生成されたデータベース バックアップから最も古い復元ポイントを確認する

チュートリアルのこのセクションでは、データベースの[サービスによって生成された自動バックアップ](sql-database-automated-backups.md)から最も古い復元ポイントに関する情報を確認します。 

1. データベース **sqldbtutorialdb** の **[SQL データベース]** ブレードを開きます。

   ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

2. ツール バーの **[復元]** をクリックします。

   ![ツール バーの [復元]](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. [復元] ブレードで、最も古い復元ポイントを確認します。

   ![最も古い復元ポイント](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する

チュートリアルのこのセクションでは、特定の時点のデータベースを新しいデータベースに復元します。

1. データベースの **[復元]** ブレードで、新しいデータベースの既定の名前を確認します (この名前は、既存のデータベース名にタイムスタンプが追加されたものです)。この新しいデータベースに対して、データベースが以前の状態に復元されます。 この名前は、この後のいくつかの手順で指定する時刻を反映するように変更されます。

   ![復元されるデータベースの名前](./media/sql-database-get-started-backup-recovery/restored-database-name.png)

2. **[復元ポイント (UTC)]** 入力ボックスの**カレンダー** アイコンをクリックします。

   ![復元ポイント](./media/sql-database-get-started-backup-recovery/restore-point.png)

2. カレンダーで、保有期間内の日付を選択します。

   ![復元ポイントの日付](./media/sql-database-get-started-backup-recovery/restore-point-date.png)

3. **[復元ポイント (UTC)]** 入力ボックスで、選択した日付の時刻を指定します。データベースのデータは、自動データベース バックアップから指定した時刻の状態に復元されます。

   ![復元ポイントの時刻](./media/sql-database-get-started-backup-recovery/restore-point-time.png)

   >[!NOTE]
   >データベース名が、選択した日時を示すように変更されていることに注意してください。 また、特定の時点への復元の対象となるサーバーを変更できないことにも注意してください。 別のサーバーに復元するには、[geo リストア](sql-database-disaster-recovery.md#recover-using-geo-restore)を使用します。 最後に、[エラスティック プール](sql-database-elastic-jobs-overview.md)または別の価格レベルに復元できることに注意してください。 
   >

4. **[OK]** をクリックして、データベースを新しいデータベースで以前の特定の時点に復元します。

5. ツール バーの通知アイコンをクリックして、復元ジョブの状態を確認します。

   ![復元ジョブの進行状況](./media/sql-database-get-started-backup-recovery/restore-job-progress.png)

6. 復元ジョブが完了したら、**[SQL データベース]** ブレードを開き、新しく復元されたデータベースを確認します。

   ![復元されたデータベース](./media/sql-database-get-started-backup-recovery/restored-database.png)

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](sql-database-recovery-using-backups.md#point-in-time-restore)できます。
>

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Azure Recovery Services コンテナーの自動バックアップの長期的な保有期間を構成する 

チュートリアルのこのセクションでは、ご利用のサービス レベルの保有期間より長い間、[自動バックアップを保持するよう Azure Recovery Services コンテナーを構成](sql-database-long-term-retention.md)します。 


> [!TIP]
> 長期的なバックアップ リテンション期間のバックアップを削除する方法については、[PowerShell を使った長期的なバックアップ リテンション期間の管理](sql-database-manage-long-term-backup-retention-powershell.md)に関する記事をご覧ください。
>

1. サーバー **sqldbtutorialserver** の **[SQL Server]** ブレードを開きます。

   ![[SQL Server] ブレード](./media/sql-database-get-started/sql-server-blade.png)

2. **[Long-term backup retention (長期的なバックアップ保有期間)]** をクリックします。

   ![長期的なバックアップ保有期間のリンク](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. **[sqldbtutorial - Long-term backup retention (sqldbtutorial - 長期的なバックアップ保有期間)]** ブレードで、プレビューの使用条件を確認して同意します (既に同意している場合またはこの機能がプレビュー段階ではなくなった場合を除く)。

   ![プレビューの使用条件の承認](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. sqldbtutorialdb データベースの長期的なバックアップ保有期間を構成するには、グリッドでそのデータベースを選択し、ツール バーの **[構成]** をクリックします。

   ![長期的なバックアップ保有期間の対象となるデータベースの選択](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. **[構成]** ブレードの **[Recovery service vault (Recovery Services コンテナー)]** で **[必要な設定の構成]** をクリックします。

   ![コンテナーの構成リンク](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. **[Recovery Services コンテナー]** ブレードで、既存のコンテナーを選択します (ある場合)。 また、サブスクリプションに Recovery Services コンテナーが見つからなかった場合は、フローをクリックして終了し、Recovery Services コンテナーを作成します。

   ![新しいコンテナーの作成リンク](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. **[Recovery Services コンテナー]** ブレードの **[追加]** をクリックします。

   ![新しいコンテナーの追加リンク](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. **[Recovery Services コンテナー]** ブレードで、新しい Recovery Services コンテナーに有効な名前を指定します。

   ![新しいコンテナーの名前](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. サブスクリプションとリソース グループを選択し、コンテナーの場所を選択します。 完了したら、**[作成]** をクリックします。

   ![新しいコンテナーの作成](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > コンテナーは、Azure SQL 論理サーバーと同じリージョンに配置する必要があります。また、論理サーバーと同じリソース グループを使用する必要があります。
   >

10. 新しいコンテナーが作成されたら、必要な手順を実行して **[Recovery Services コンテナー]** ブレードに戻ります。

11. **[Recovery Services コンテナー]** ブレードで、コンテナーをクリックし、**[選択]** をクリックします。

   ![既存のコンテナーの選択](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. **[構成]** ブレードで、新しい保持ポリシーに有効な名前を指定し、必要に応じて既定の保持ポリシーを変更して、**[OK]** をクリックします。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. **[sqldbtutorial - Long-term backup retention (sqldbtutorial - 長期的なバックアップ保有期間)]** ブレードで、**[保存]** をクリックし、**[OK]** をクリックして、選択したすべてのデータベースに長期的なバックアップ保持ポリシーを適用します。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. **[保存]** をクリックして、構成した Azure Recovery Services コンテナーに対して、この新しいポリシーを使用して長期的なバックアップ保有期間を有効にします。

   ![保持ポリシーの定義](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

15. 長期的なバックアップ保有期間が有効になったら、**[sqldbtutorialvault]** ブレードを開きます (**[すべてのリソース]** に移動し、サブスクリプションのリソースの一覧から選択します)。

   ![Recovery Services コンテナーの表示](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)

> [!IMPORTANT]
> 構成が完了すると、その後&7; 日以内に、コンテナーにバックアップが表示されます。 バックアップがコンテナーに表示されるまで、このチュートリアルを先に進めないでください。
>

## <a name="view-backups-in-long-term-retention"></a>長期的な保有期間内のバックアップを確認する

チュートリアルのこのセクションでは、[長期的なバックアップ保有期間](sql-database-long-term-retention.md)内のデータベース バックアップに関する情報を確認します。 

1. **[sqldbtutorialvault]** ブレードを開き (**[すべてのリソース]** に移動し、サブスクリプションのリソースの一覧から選択します)、コンテナー内のデータベース バックアップで使用されているストレージの容量を確認します。

   ![バックアップを含む Recovery Services コンテナーの表示](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. データベース **sqldbtutorialdb** の **[SQL データベース]** ブレードを開きます。

   ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

3. ツール バーの **[復元]** をクリックします。

   ![ツール バーの [復元]](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. [復元] ブレードの **[長期]** をクリックします。

5. [Azure vault backups (Azure コンテナーのバックアップ)] の **[バックアップの選択]** をクリックして、長期的なバックアップ保有期間内の使用可能なデータベース バックアップを表示します。

   ![コンテナー内のバックアップ](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>長期的なバックアップ保有期間内のバックアップからデータベースを復元する

チュートリアルのこのセクションでは、データベースを Azure Recovery Services コンテナーにあるバックアップから新しいデータベースに復元します。

1. **[Azure vault backups (Azure コンテナーのバックアップ)]** ブレードで、復元するバックアップをクリックし、**[選択]** をクリックします。

   ![コンテナー内のバックアップの選択](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. **[データベース名]** ボックスに、復元されるデータベースの名前を指定します。

   ![新しいデータベース名](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. **[OK]** をクリックして、コンテナー内にあるバックアップから新しいデータベースにデータベースを復元します。

4. ツール バーの通知アイコンをクリックして、復元ジョブの状態を確認します。

   ![コンテナーからの復元ジョブの進行状況](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 復元ジョブが完了したら、**[SQL データベース]** ブレードを開き、新しく復元されたデータベースを確認します。

   ![コンテナーから復元されたデータベース](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> ここから、SQL Server Management Studio を使用して、復元されたデータベースに接続し、必要なタスクを実行できます。たとえば、[復元されたデータベースからデータを少し抽出して既存のデータベースにコピーしたり、既存のデータベースを削除し、復元されたデータベースの名前を既存のデータベース名に変更したり](sql-database-recovery-using-backups.md#point-in-time-restore)できます。
>

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

