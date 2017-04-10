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
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: b00c320eeb1e8b30a0fad9634844ceeca60d1c29
ms.lasthandoff: 02/16/2017


---
# <a name="back-up-your-app-in-azure"></a>Azure でのアプリのバックアップ
[Azure App Service](../app-service/app-service-value-prop-what-is.md) のバックアップと復元の機能により、アプリのバックアップを手動または自動で簡単に作成できます。 アプリを前の状態に復元するか、元のアプリのいずれかのバックアップに基づく新しいアプリを作成できます。 

アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>バックアップ対象
App Service では、次の情報をバックアップできます。

* アプリの構成
* ファイルのコンテンツ
* アプリに接続されているすべての Azure SQL Database または Azure MySQL (ClearDB) データベース (バックアップに含めるデータベースを選択できます)

この情報は、指定した Azure のストレージ アカウントとコンテナーにバックアップされます。 

> [!NOTE]
> 各バックアップは増分更新ではなく、アプリの完全なオフライン コピーです。
> 
> 

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>要件および制限
* バックアップと復元の機能には、 **Standard** 以上のレベルにある App Service プランが必要です。 上位レベルを使用するための App Service プランの拡張の詳細については、 [Azure でのアプリのスケールアップ](web-sites-scale.md)に関するページを参照してください。 **Premium** レベルでは、**Standard** レベルよりも多くの回数の日次バックアップが可能です。
* バックアップするアプリと同じサブスクリプション内に Azure ストレージ アカウントとコンテナーが必要です。 Azure のストレージ アカウントの詳細については、この記事の末尾に示されている [リンク](#moreaboutstorage) を参照してください。
* 最大 10 GB のアプリとデータベースのコンテンツをバックアップできます。 バックアップのサイズがこの制限を超えた場合、エラーが発生します。 

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>手動バックアップの作成
1. [Azure ポータル](https://portal.azure.com)で、アプリのブレードに移動し、**[設定]**、**[バックアップ]** の順に選択します。 **[バックアップ]** ブレードが表示されます。
   
    ![バックアップ ページ][ChooseBackupsPage]
   
   > [!NOTE]
   > 次のメッセージが表示された場合は、メッセージをクリックして App Service プランをアップグレードした後、バックアップに進むことができます。
   > 詳細については、 [Azure でのアプリのスケールアップ](web-sites-scale.md) に関するページを参照してください。  
   > ![ストレージ アカウントの選択](./media/web-sites-backup/01UpgradePlan.png)
   > 
   > 
2. **[バックアップ]** ブレードで、**[ストレージ: 未構成]** をクリックして、ストレージ アカウントを構成します。
   
    ![ストレージ アカウントの選択][ChooseStorageAccount]
3. **[ストレージ アカウント]** と **[コンテナー]** を選択して、バックアップ先を選択します。 ストレージ アカウントは、バックアップするアプリと同じサブスクリプションに属する必要があります。 必要に応じて、各ブレードで新しいストレージ アカウントまたは新しいコンテナーを作成できます。 完了したら、 **[選択]**をクリックします。
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. 開いたままになっている **[バックアップ設定の構成]** ブレードで、**[データベースの設定]** をクリックし、バックアップに含めるデータベース (SQL データベースまたは MySQL) を選択してから、**[OK]** をクリックします。  
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > この一覧に表示されるデータベースでは、その接続文字列がアプリの **[アプリケーション設定]** ブレードの **[接続文字列]** セクションに存在する必要があります。
   > 
   > 
5. **[バックアップ設定の構成]** ブレードで、**[保存]** をクリックします。    
6. **[バックアップ]** ブレードのコマンド バーで、**[今すぐバックアップ]** をクリックします。
   
    ![BackUpNow ボタン][BackUpNow]
   
    バックアップ処理中に、進行状況についてのメッセージが表示されます。

バックアップ用のストレージ アカウントとコンテナーを構成した後、手動バックアップをいつでも実行できます。  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>自動バックアップの構成
1. **[バックアップ]** ブレードで、**[スケジュール: 未構成]** をクリックします。 
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/05ScheduleBackup.png)
2. **[バックアップ スケジュールの設定]** ブレードで、**[スケジュールされたバックアップ]** を**オン**に設定し、必要に応じてバックアップ スケジュールを構成し、**[OK]** をクリックします。
   
    ![自動化されたバックアップを有効にする][SetAutomatedBackupOn]
3. 開いたままになっている **[バックアップ設定の構成]** ブレードで、**[ストレージ設定]** をクリックし、**[ストレージ アカウント]** と **[コンテナー]** を選択してバックアップ先を選択します。 ストレージ アカウントは、バックアップするアプリと同じサブスクリプションに属する必要があります。 必要に応じて、各ブレードで新しいストレージ アカウントまたは新しいコンテナーを作成できます。 完了したら、 **[選択]**をクリックします。
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/02ChooseStorageAccount1.png)
4. **[バックアップ設定の構成]** ブレードで、**[データベースの設定]** をクリックし、バックアップに含めるデータベース (SQL データベースまたは MySQL) を選択してから、**[OK]** をクリックします。  
   
    ![ストレージ アカウントの選択](./media/web-sites-backup/03ConfigureDatabase.png)
   
   > [!NOTE]
   > この一覧に表示されるデータベースでは、その接続文字列がアプリの **[アプリケーション設定]** ブレードの **[接続文字列]** セクションに存在する必要があります。
   > 
   > 
5. **[バックアップ設定の構成]** ブレードで、**[保存]** をクリックします。    

<a name="partialbackups"></a>

## <a name="backup-just-part-of-your-app"></a>アプリの一部のみのバックアップ
アプリのすべてをバックアップしたくない場合があります。 次に例をいくつか示します。

* 古いブログの投稿や画像などの、変更されることがない静的コンテンツを含むアプリを [毎週バックアップするように設定](web-sites-backup.md#configure-automated-backups) している。
* アプリのコンテンツが 10 GB を超えている (つまり、一度にバックアップできる最大量を超えている)。
* ログ ファイルはバックアップしない。

部分バックアップでは、バックアップするファイルを詳細に選択できます。

### <a name="exclude-files-from-your-backup"></a>バックアップからファイルを除外する
バックアップからファイルとフォルダーを除外するには、`_backup.filter` ファイルをアプリの D:\home\site\wwwroot フォルダー内に作成し、除外するファイルとフォルダーをその中に指定します。 このファイルにアクセスする簡単な方法は、 [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)を使用することです。 

今後まったく変更されることがない過去のログ ファイルや静止画像を含むアプリがあるとします。 古い画像を含むアプリの完全バックアップは既に存在します。 今後、アプリのバックアップを毎日実行しますが、変更されることがないログ ファイルや静止イメージ ファイルはバックアップしないようにするとします。

![Logs フォルダー][LogsFolder]
![イメージ フォルダー][ImagesFolder]

次の手順は、これらのファイルをバックアップから除外する方法を示しています。

1. `http://{yourapp}.scm.azurewebsites.net/DebugConsole` に移動し、バックアップから除外するフォルダーを識別します。 この例では、UI に表示されている次のファイルとフォルダーを除外します。
   
        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png
   
    [AZURE.NOTE] 最後の行は、フォルダーと同じように個々のファイルを除外できることを示しています。
2. `_backup.filter` という名前のファイルを作成し、上のリストをこのファイルに配置します。ただし `D:\home` は削除します。 1 つのディレクトリまたはファイルを&1; 行に配置します。 したがって、ファイルの内容は次のようになります。
   
    \site\wwwroot\Logs \LogFiles \site\wwwroot\Images\2013 \site\wwwroot\Images\2014 \site\wwwroot\Images\brand.png
3. このファイルを、[ftp](web-sites-deploy.md#ftp)やその他の方法を使用して、サイトの `D:\home\site\wwwroot\` ディレクトリにアップロードします。 ファイルを `http://{yourapp}.scm.azurewebsites.net/DebugConsole` 内に直接作成し、そこにコンテンツを挿入することもできます。
4. 通常と同じ方法 ([手動](#create-a-manual-backup)または[自動](#configure-automated-backups)) でバックアップを実行します。

これで、 `_backup.filter` 内に指定されたファイルとフォルダーはバックアップから除外されます。 この例では、ログ ファイル、2013 年と 2014 年の画像ファイル、および brand.png がバックアップされなくなります。

> [!NOTE]
> [定期的なバックアップからの復元](web-sites-restore.md)と同様の手順で、サイトを部分バックアップから復元することもできます。 復元プロセスは正常に実行されます。
> 
> 完全バックアップが復元されると、サイト上のすべてのコンテンツは、バックアップの中身に置き換えられます。 サイト上に存在したファイルも、バックアップに存在しなければ削除されます。 ただし、部分バックアップが復元されるとき、ブラック リスト化されたディレクトリまたはファイルのいずれかに配置されているコンテンツは放置されます。
> 
> 

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>バックアップの保存方法
アプリの&1; つ以上のバックアップを作成すると、ストレージ アカウントの **[コンテナー]** ブレードに、アプリだけではなくバックアップも表示されます。 ストレージ アカウントでは、各バックアップは、バックアップ データを含む .zip ファイルと、.zip ファイルの内容のマニフェストを含む .xml ファイルで構成されます。 バックアップにアクセスする場合は、これらのファイルを解凍して参照でき、アプリを実際に復元する必要はありません。

アプリのデータベースのバックアップは、.zip ファイルのルートに保存されます。 SQL データベースの場合、これは BACPAC ファイルで (ファイル拡張子はありません)、インポートできます。 BACPAC のエクスポートに基づく新しい SQL データベースの作成については、「 [BACPAC ファイルのインポートによる新しいユーザー データベースの作成](http://technet.microsoft.com/library/hh710052.aspx)」を参照してください。

> [!WARNING]
> **websitebackups** コンテナー内のファイルを変更すると、バックアップが無効になり、復元できなくなる可能性があります。
> 
> 

<a name="nextsteps"></a>

## <a name="next-steps"></a>次のステップ
アプリをバックアップから復元する方法については、 [Azure でのアプリの復元](web-sites-restore.md)に関するページを参照してください。 REST API を使用して、App Service アプリのバックアップと復元を実行することもできます (「 [REST を使用して App Service アプリのバックアップと復元を実行する](websites-csm-backup.md)」を参照してください)。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png


