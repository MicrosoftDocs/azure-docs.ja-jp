---
title: "Azure でのアプリの復元"
description: "アプリをバックアップから復元する方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 71d07e64480d4b4eaff7fdef53c8f5ff7a97a944
ms.lasthandoff: 01/20/2017


---
# <a name="restore-an-app-in-azure"></a>Azure でのアプリの復元
この記事では、[Azure App Service](../app-service/app-service-value-prop-what-is.md) でアプリをバックアップから復元する方法を示します (バックアップについては「[Azure App Service での Web アプリのバックアップ](web-sites-backup.md)」を参照してください)。 リンクされたデータベース (SQL Database または MySQL) をオンデマンドで使用してアプリを以前の状態に戻したり、元のアプリのいずれかのバックアップに基づいて新しいアプリを作成したりすることができます。 最新バージョンと並行して実行される新しいアプリを作成すると、A/B テストを実施する場合に役立ちます。

バックアップからの復元は、**Standard** レベルか **Premium** レベルで実行されているアプリでのみ利用できます。 アプリのスケーリングについて詳しくは、「 [Azure App Service の Web アプリをスケーリングする](web-sites-scale.md)」をご覧ください。 **Premium** レベルでは、**Standard** レベルよりも多くの回数の日次バックアップが可能です。

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>既存のバックアップからのアプリの復元
1. Azure Portal にあるアプリの **[設定]** ブレードで、**[バックアップ]** をクリックして **[バックアップ]** ブレードを表示します。 コマンド バーで、 **[今すぐ復元]** をクリックします。
   
    ![今すぐ復元の選択][ChooseRestoreNow]
2. **[復元]** ブレードで、最初にバックアップ ソースを選択します。
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    **[アプリのバックアップ]** オプションには、現在のアプリの既存のバックアップがすべて表示されるので、バックアップを簡単に選択することができます。
    **[ストレージ]** オプションを使用すると、サブスクリプションに含まれる既存の Azure Storage アカウントとコンテナーから、任意のバックアップ ZIP ファイルを選択できます。
    別のアプリのバックアップを復元する場合は、 **[ストレージ]** オプションを使用します。
3. 次に、 **[復元先]**でアプリの復元先を指定します。
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > **[上書き]**を選択した場合、現在のアプリの既存のデータはすべて削除されます。 **[OK]**をクリックする前に、実行する操作内容が正しいことを確認します。
   > 
   > 
   
    **[既存のアプリ]** を選択して、アプリのバックアップを同じリソース グループ内の別のアプリに復元することができます。 このオプションを使用する前に、リソース グループ内に別のアプリを作成済みであり、データベース構成を、アプリのバックアップ内に定義されている構成にミラーリングしている必要があります。
4. **[OK]**をクリックします。

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>ストレージ アカウントからバックアップをダウンロードまたは削除するには
1. Azure Portal のメインの **[参照]** ブレードで、**[ストレージ アカウント]** を選択します。
   
    既存のストレージ アカウントの一覧が表示されます。
2. ダウンロードまたは削除するバックアップが含まれているストレージ アカウントを選択します。
   
    ストレージ アカウントのブレードが表示されます。
3. ストレージ アカウントのブレードで、必要なコンテナーを選択します。
   
    ![コンテナーの表示][ViewContainers]
4. ダウンロードまたは削除するバックアップ ファイルを選択します。
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 目的に応じて、**[ダウンロード]** または **[削除]** をクリックします。  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>復元操作の確認
1. アプリの復元操作が成功したかどうかについて詳細を確認するには、Azure ポータルの **[アクティビティ ログ]** ブレードに移動します。
   
    **[アクティビティ ログ]** ブレードには、すべての操作と、レベル、状態、リソース、および時刻が表示されます。
2. 下方向へスクロールし、目的の復元操作が見つかったらクリックして選択します。

詳細ブレードに、復元操作に関連する利用可能な情報が表示されます。

## <a name="next-steps"></a>次のステップ
REST API を使用して、App Service アプリのバックアップと復元を実行することもできます (「 [REST を使用して App Service アプリのバックアップと復元を実行する](websites-csm-backup.md)」を参照してください)。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

