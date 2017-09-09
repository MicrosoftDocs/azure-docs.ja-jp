---
title: "Azure でのアプリのバックアップ"
description: "Azure App Service でアプリのバックアップを作成する方法を説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 891359514e776e169bf05df7b84d2b99306f98bf
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017

---
# <a name="back-up-your-app-in-azure"></a>Azure でのアプリのバックアップ
[Azure App Service](../app-service/app-service-value-prop-what-is.md) のバックアップと復元の機能により、アプリのバックアップを手動またはスケジュール設定により簡単に作成できます。 以前の状態のスナップショットにアプリを復元するには、既存のアプリを上書きするか、別のアプリに対して復元を行います。 

アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>バックアップ対象
App Service によって、アプリで使用するようにユーザーが構成した Azure ストレージ アカウントとコンテナーに、次の情報をバックアップできます。 

* アプリの構成
* ファイルのコンテンツ
* アプリに接続されているデータベース

次のデータベース ソリューションがバックアップ機能でサポートされています。 
   - [SQL Database](https://azure.microsoft.com/en-us/services/sql-database/)
   - [Azure Database for MySQL (プレビュー)](https://azure.microsoft.com/en-us/services/mysql)
   - [Azure Database for PostgreSQL (プレビュー)](https://azure.microsoft.com/en-us/services/postgres)
   - [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
   - [アプリ内 MySQL](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>要件および制限
* バックアップと復元の機能には、 **Standard** レベルまたは **Premium** レベル以上のレベルにある App Service プランが必要です。 上位レベルを使用するための App Service プランの拡張の詳細については、 [Azure でのアプリのスケールアップ](web-sites-scale.md)に関するページを参照してください。  
  **Premium** レベルでは、**Standard** レベルよりも多くの回数の日次バックアップが可能です。
* バックアップするアプリと同じサブスクリプション内に Azure ストレージ アカウントとコンテナーが必要です。 Azure のストレージ アカウントの詳細については、この記事の末尾に示されている [リンク](#moreaboutstorage) を参照してください。
* 最大 10 GB のアプリとデータベースのコンテンツをバックアップできます。 バックアップのサイズがこの制限を超えた場合、エラーが発生します。

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>手動バックアップの作成
1. [Azure Portal](https://portal.azure.com) で、アプリのブレードに移動し、**[バックアップ]** を選択します。 **[バックアップ]** ブレードが表示されます。
   
    ![バックアップ ページ][ChooseBackupsPage]
   
   > [!NOTE]
   > 次のメッセージが表示された場合は、メッセージをクリックして App Service プランをアップグレードした後、バックアップに進むことができます。
   > 詳細については、 [Azure でのアプリのスケールアップ](web-sites-scale.md) に関するページを参照してください。  
   > ![ストレージ アカウントの選択](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. **[バックアップ]** ブレードで、**[構成]** をクリックします。
![[構成] をクリックする](./media/web-sites-backup/ClickConfigure1.png)
3. **[バックアップ構成]** ブレードで、**[ストレージ: 未構成]** をクリックして、ストレージ アカウントを構成します。
   
    ![ストレージ アカウントの選択][ChooseStorageAccount]
4. **[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。 ストレージ アカウントは、バックアップするアプリと同じサブスクリプションに属する必要があります。 必要に応じて、各ブレードで新しいストレージ アカウントまたは新しいコンテナーを作成できます。 完了したら、 **[選択]** をクリックします。
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. 開いたままになっている **[バックアップ構成]** ブレードで、**[データベースをバックアップする]** を構成し、バックアップに含めるデータベース (SQL データベースまたは MySQL) を選択してから、**[OK]** をクリックします。  
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > この一覧に表示されるデータベースでは、その接続文字列がアプリの **[アプリケーション設定]** ブレードの **[接続文字列]** セクションに存在する必要があります。
   > 
   > 
6. **[バックアップ構成]** ブレードで、**[保存]** をクリックします。    
7. **[バックアップ]** ブレードで、**[バックアップ]** をクリックします。
   
    ![BackUpNow ボタン][BackUpNow]
   
    バックアップ処理中に、進行状況についてのメッセージが表示されます。

ストレージ アカウントとコンテナーの構成が終わったら、いつでも手動バックアップを開始できます。  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>自動バックアップの構成
1. **[バックアップ構成]** ブレードで、**[スケジュールされたバックアップ]** を**オン**に設定します。 
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/05ScheduleBackup1.png)
2. バックアップ スケジュールのオプションが表示されます。**[スケジュールされたバックアップ]** を**オン**に設定し、必要に応じてバックアップ スケジュールを構成して、**[OK]** をクリックします。
   
    ![自動化されたバックアップを有効にする][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>部分バックアップの構成
アプリのすべてをバックアップしたくない場合があります。 次に例をいくつか示します。

* 古いブログの投稿や画像などの、変更されることがない静的コンテンツを含むアプリを [毎週バックアップするように設定](web-sites-backup.md#configure-automated-backups) している。
* アプリのコンテンツが 10 GB を超えている (つまり、一度にバックアップできる最大量を超えている)。
* ログ ファイルはバックアップしない。

部分バックアップでは、バックアップするファイルを詳細に選択できます。

### <a name="exclude-files-from-your-backup"></a>バックアップからファイルを除外する
一度バックアップされており今後まったく変更されることがないログ ファイルや静止画像を含むアプリがあるとします。 このような場合には、将来のバックアップ時にそれらのフォルダーやファイルを保存しないように除外できます。 バックアップからファイルやフォルダーを除外するには、アプリの `D:\home\site\wwwroot`フォルダー内に `_backup.filter` ファイルを作成します。 このファイルに、除外するファイルやフォルダーの一覧を指定します。 

ファイルにアクセスする簡単な方法として Kudu を使用できます。 Web アプリ の **[高度なツール] -> [移動]** 設定をクリックして、Kudu にアクセスします。

![ポータルを使用する Kudu][kudu-portal]

バックアップから除外するフォルダーを識別します。  たとえば、強調表示されたフォルダーおよびファイルを除外したいとします。

![images フォルダー][ImagesFolder]

`_backup.filter` という名前のファイルを作成し、上のリストをこのファイルに配置します。ただし `D:\home` は削除します。 1 つのディレクトリまたはファイルを 1 行に配置します。 したがって、ファイルの内容は次のようになります。
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

`_backup.filter`ファイルを、[ftp](web-sites-deploy.md#ftp)やその他の方法を使用して、サイトの `D:\home\site\wwwroot\` ディレクトリにアップロードします。 Kudu の `DebugConsole` を使用してファイルを直接作成し、そこにコンテンツを挿入することもできます。

通常と同じ方法 ([手動](#create-a-manual-backup)または[自動](#configure-automated-backups)) でバックアップを実行します。 これで、`_backup.filter` に指定されたファイルとフォルダーは、スケジュール設定されているか手動で開始されるかにかかわらず、将来のバックアップから除外されます。 

> [!NOTE]
> [定期的なバックアップからの復元](web-sites-restore.md)と同様の手順で、サイトを部分バックアップから復元することもできます。 復元プロセスは正常に実行されます。
> 
> 完全バックアップが復元されると、サイト上のすべてのコンテンツは、バックアップの中身に置き換えられます。 サイト上に存在したファイルも、バックアップに存在しなければ削除されます。 ただし、部分バックアップが復元されるとき、ブラック リスト化されたディレクトリまたはファイルのいずれかに配置されているコンテンツは放置されます。
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>バックアップの保存方法
アプリの 1 つ以上のバックアップを作成すると、ストレージ アカウントとアプリの **[コンテナー]** ブレードに、バックアップが表示されます。 ストレージ アカウントでは、各バックアップは、バックアップ データを含む `.zip` ファイルと、`.zip` ファイルの内容のマニフェストを含む `.xml` ファイルで構成されます。 バックアップにアクセスする場合は、これらのファイルを解凍して参照でき、アプリを実際に復元する必要はありません。

アプリのデータベースのバックアップは、.zip ファイルのルートに保存されます。 SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。 BACPAC のエクスポートに基づく SQL データベースの作成については、「[BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」を参照してください。

> [!WARNING]
> **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>次のステップ
アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。 REST API を使用して、App Service アプリのバックアップと復元を実行することもできます (「[REST を使用して App Service アプリのバックアップと復元を実行する](websites-csm-backup.md)」を参照してください)。


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG


