---
title: Azure-SSIS Integration Runtime パッケージ ストアを使用したパッケージの管理
description: Azure-SSIS Integration Runtime パッケージ ストアを使用してパッケージを管理する方法について説明します。
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 66fdd20e66360be7cdaa6d918e18ca09c413da07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393838"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Azure-SSIS Integration Runtime パッケージ ストアを使用したパッケージの管理

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

オンプレミスの SQL Server Integration Services (SSIS) ワークロードをクラウドにリフト アンド シフトするには、Azure Data Factory (ADF) で Azure-SSIS Integration Runtime (IR) をプロビジョニングします。 詳細については、[Azure-SSIS IR のプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)に関するページを参照してください。 Azure-SSIS IR では、次のことがサポートされます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) のいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

パッケージ デプロイ モデルを使用する場合、パッケージ ストアを使用して Azure-SSIS IR をプロビジョニングするかどうかを選択できます。 これらには、Azure SQL Managed Instance によってホストされるファイル システム、Azure Files、または MSDB の上にパッケージ管理レイヤーが用意されています。 Azure-SSIS IR パッケージ ストアを使用すると、パッケージのインポート、エクスポート、削除、実行のほか、実行中のパッケージの監視と停止を、[従来の SSIS パッケージ ストア](/sql/integration-services/service/package-management-ssis-service)と同様に SQL Server Management Studio (SSMS) を介して行うことができます。 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR への接続

Azure-SSIS IR がプロビジョニングされたら、それに接続して SSMS でパッケージ ストアを参照することができます。

![Azure-SSIS IR への接続](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

SSMS の **[オブジェクト エクスプローラー]** ウィンドウで、 **[接続]** ドロップダウン メニューの **[Azure-SSIS Integration Runtime]** を選択します。 次に、Azure にサインインし、パッケージ ストアを使用してプロビジョニングした関連するサブスクリプション、ADF、および Azure-SSIS IR を選択します。 Azure-SSIS IR が **[実行中のパッケージ]** および **[格納済みパッケージ]** ノードの下に表示されます。 **[格納済みパッケージ]** ノードを展開して、パッケージ ストアを下に表示します。 パッケージ ストアを展開して、配下にあるフォルダーとパッケージを表示します。 SSMS により自動的に接続できない場合は、パッケージ ストアのアクセス資格情報を入力するように求められる場合があります。 たとえば、MSDB の上部でパッケージ ストアを展開したとき、最初に Azure SQL Managed Instance に接続するように求められる場合があります。

![Azure SQL Managed Instance に接続する](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>フォルダーとパッケージの管理

SSMS 上の Azure-SSIS IR に接続すると、任意のパッケージ ストア、フォルダー、またはパッケージを右クリックしてメニューをポップアップ表示し、 **[新しいフォルダー]** 、 **[パッケージのインポート]** 、 **[パッケージのエクスポート]** 、 **[削除]** 、または **[更新]** を選択できます。

   ![フォルダーとパッケージの管理](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  インポートしたパッケージ用の新しいフォルダーを作成するには **[新しいフォルダー]** を選択します。

   *  **ファイル システム**、**SQL Server** (MSDB)、または従来の **SSIS パッケージ ストア** からパッケージ ストアにパッケージをインポートするには、 **[パッケージのインポート]** を選択します。

      ![パッケージのインポート](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      インポート元の **[パッケージの場所]** に応じて、該当する **[サーバー]** / **[認証の種類]** を選択し、必要に応じてアクセス資格情報を入力し、 **[パッケージのパス]** を選択して、新しい **[パッケージ名]** を入力します。 パッケージをインポートするときに、保護レベルを変更することはできません。 これを変更するには、SQL Server Data Tools (SSDT) または `dtutil` コマンド ライン ユーティリティを使用します。

      > [!NOTE]
      > Azure-SSIS IR パッケージ ストアへの SSIS パッケージのインポートは 1 つずつ実行する必要があります。これらは、SQL Server/SSIS のバージョンを維持したままで、基になる MSDB、ファイル システム、または Azure Files に単にコピーされます。 
      >
      > 現在、Azure-SSIS IR は **SQL Server 2017** に基づいているため、それよりも前のバージョンのパッケージを実行すると、実行時に SSIS 2017 パッケージにアップグレードされます。 より新しいバージョンのパッケージの実行はサポートされていません。
      >
      > さらに、レガシ SSIS パッケージ ストアは特定の SQL Server バージョンにバインドされ、そのバージョンの SSMS でのみアクセスできるため、レガシ SSIS パッケージ ストアの下位バージョンのパッケージは、SSMS 2019 以降のバージョンを使用して Azure-SSIS IR パッケージ ストアにインポートする前に、まず指定の SSMS バージョンを使用してファイル システムにエクスポートする必要があります。
      >
      > また、複数の SSIS パッケージを Azure-SSIS IR パッケージ ストアにインポートし、さらに保護レベルを切り替えるには、[dtutil](/sql/integration-services/dtutil-utility) コマンド ライン ユーティリティを使用できます。「[dtutil を使用した複数のパッケージのデプロイ](#deploying-multiple-packages-with-dtutil)」を参照してください。

   *  パッケージ ストアから **ファイル システム**、**SQL Server** (MSDB)、または従来の **SSIS パッケージ ストア** にパッケージをエクスポートするには、 **[パッケージのエクスポート]** を選択します。

      ![パッケージのエクスポート](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      エクスポート先の **[パッケージの場所]** に応じて、該当する **[サーバー]** / **[認証の種類]** を選択し、必要に応じてアクセス資格情報を入力して、 **[パッケージのパス]** を選択します。 パッケージをエクスポートするときに、それらが暗号化されている場合は、最初にパスワードを入力して暗号化を解除します。その後、たとえば、機密データの保存を回避する、ユーザー キーやパスワードを使用してすべてのデータを暗号化する、などに保護レベルを変更できます。

      > [!NOTE]
      > Azure-SSIS IR パッケージ ストアからの SSIS パッケージのエクスポートは、1 つずつ実行する必要があります。保護レベルを切り替えずにこれを行うと、SQL Server/SSIS のバージョンを維持したままで、単にコピーされます。それ以外の場合は、SSIS 2019 以降のバージョンのパッケージにアップグレードされます。
      >
      > 現在、Azure-SSIS IR は **SQL Server 2017** に基づいているため、それよりも前のバージョンのパッケージを実行すると、実行時に SSIS 2017 パッケージにアップグレードされます。 より新しいバージョンのパッケージの実行はサポートされていません。
      >
      > また、複数の SSIS パッケージを Azure-SSIS IR パッケージ ストアからエクスポートし、さらに保護レベルを切り替えるには、[dtutil](/sql/integration-services/dtutil-utility) コマンド ライン ユーティリティを使用できます。「[dtutil を使用した複数のパッケージのデプロイ](#deploying-multiple-packages-with-dtutil)」を参照してください。

   *  パッケージ ストアから既存のフォルダーまたはパッケージを削除するには、 **[削除]** を選択します。

   *  パッケージ ストアに新しく追加されたフォルダーまたはパッケージを表示するには、 **[更新]** を選択します。

## <a name="execute-packages"></a>パッケージの実行

SSMS 上の Azure-SSIS IR に接続すると、格納されている任意のパッケージを右クリックしてメニューをポップアップ表示し、 **[パッケージの実行]** を選択できます。  これにより、 **[パッケージ実行ユーティリティ]** ダイアログが開き、ADF パイプラインでの SSIS パッケージ アクティビティとして、Azure-SSIS IR でパッケージの実行を構成できます。

![パッケージ実行ユーティリティ ページ 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![パッケージ実行ユーティリティ ページ 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**[パッケージ実行ユーティリティ]** ダイアログの **[全般]** 、 **[構成]** 、 **[実行オプション]** 、 **[ログ]** の各ページは、[SSIS パッケージの実行] アクティビティの **[設定]** タブに対応しています。 これらのページでは、パッケージの暗号化パスワードを入力し、パッケージ構成ファイルの情報にアクセスすることができます。 パッケージ実行の資格情報とプロパティだけでなく、ログ フォルダーのアクセス情報を入力することもできます。  **[パッケージ実行ユーティリティ]** ダイアログの **[値の設定]** ページは、[SSIS パッケージの実行] アクティビティの **[プロパティのオーバーライド]** タブに対応しています。ここでは、上書きする既存のパッケージのプロパティを入力できます。 詳細については、[ADF パイプラインで SSIS パッケージ実行アクティビティとして SSIS パッケージを実行する](./how-to-invoke-ssis-package-ssis-activity.md)に方法に関するページを参照してください。

**[実行]** ボタンを選択すると、SSIS パッケージの実行アクティビティを含む、新しい ADF パイプラインが自動的に生成され、トリガーされます。 同じ設定の ADF パイプラインが既に存在する場合は、再実行され、新しいパイプラインは生成されません。 ADF パイプラインと SSIS パッケージ実行アクティビティには、それぞれ `Pipeline_SSMS_YourPackageName_HashString` および `Activity_SSMS_YourPackageName` という名前が付けられます。

![パッケージ実行ユーティリティ ボタン](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS パッケージ アクティビティの実行](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>実行中のパッケージの監視および停止

SSMS 上の Azure-SSIS IR に接続すると、 **[実行中のパッケージ]** ノードを展開して、現在実行中のパッケージを下に表示できます。  そのいずれかを右クリックしてメニューをポップアップ表示し、 **[停止]** または **[更新]** を選択します。

   ![実行中のパッケージの監視および停止](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  [SSIS パッケージの実行] アクティビティとしてパッケージを実行する、現在実行中の ADF パイプラインをキャンセルするには、 **[停止]** を選択します。

   *  パッケージ ストアから新しく実行されたパッケージを表示するには、 **[更新]** を選択します。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR の監視とパッケージ ストアの編集

SSMS 上の Azure-SSIS IR に接続すると、右クリックしてメニューをポップアップ表示し、 **[Go to Azure Data Factory portal]\(Azure Data Factory ポータルに移動\)** または **[更新]** を選択することができます。

   ![ADF ポータルに移動](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  **[Go to Azure Data Factory portal]\(Azure Data Factory ポータルに移動\)** を選択して、ADF 監視ハブの **[統合ランタイム]** ページを開きます。ここでは、お使いの Azure-SSIS IR を監視できます。 **[パッケージ ストア]** タイルで、Azure-SSIS IR にアタッチされているパッケージ ストアの数を確認できます。  この数値を選択すると、パッケージ ストアのアクセス情報を格納する、ADF のリンクされたサービスを編集できるウィンドウがポップアップ表示されます。

      ![パッケージ ストアの編集](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  パッケージ ストアに新しく追加されたフォルダーまたはパッケージ、およびパッケージ ストアから実行されたパッケージを表示するには、 **[更新]** を選択します。

## <a name="deploying-multiple-packages-with-dtutil"></a>dtutil を使用した複数のパッケージのデプロイ

レガシ パッケージ デプロイ モデルを維持しながら、オンプレミスの SSIS ワークロードを ADF の SSIS にリフト アンド シフトするには、ファイル システム、SQL Server によってホストされている MSDB、またはレガシ SSIS パッケージ ストアから、Azure Files、Azure SQL Managed Instance によってホストされている MSDB、または Azure-SSIS IR パッケージ ストアにパッケージをデプロイする必要があります。 同時に、保護レベルをユーザー キーによる暗号化から非暗号化またはパスワードによる暗号化に切り替える必要もあります (まだ行っていない場合)。

SQL Server/SSIS のインストールに付属している [dtutil](/sql/integration-services/dtutil-utility) コマンド ライン ユーティリティを使用すると、複数のパッケージをバッチでデプロイすることができます。 これは特定の SSIS バージョンにバインドされているため、保護レベルを切り替えずに下位バージョンのパッケージをデプロイすると、SSIS バージョンを維持したままで、単にコピーされます。 デプロイにこれを使用し、同時に保護レベルを切り替えると、SSIS バージョンにアップグレードされます。

 現在、Azure-SSIS IR は **SQL Server 2017** に基づいているため、それよりも前のバージョンのパッケージを実行すると、実行時に SSIS 2017 パッケージにアップグレードされます。 より新しいバージョンのパッケージの実行はサポートされていません。

そのため、ランタイム アップグレードを回避するには、パッケージ デプロイ モデルで Azure-SSIS IR で実行するパッケージをデプロイするときに、SQL Server/SSIS 2017 のインストールに付属している dtutil 2017 を使用する必要があります。 この用途のために、無料の [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) をダウンロードしてインストールすることができます。 インストールが完了すると、`YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` フォルダーで dtutil 2017 を見つけることができます。

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>dtutil を使用してオンプレミスのファイル システムから Azure Files へ複数のパッケージをデプロイする

 ファイル システムから Azure Files に複数のパッケージをデプロイし、同時にそれらの保護レベルを切り替えるには、コマンド プロンプトで次のコマンドを実行します。 お客様のケースに固有の文字列をすべて置き換えてください。
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

上記のコマンドをバッチ ファイルで実行するには、`%f` を `%%f` に置き換えます。

ファイル システム上にあるレガシ SSIS パッケージ ストアから Azure Files に複数のパッケージをデプロイし、同時にそれらの保護レベルを切り替えるには、同じコマンドを使用できますが、`YourLocalDrive:\...\YourPackageFolder` をレガシ SSIS パッケージ ストアで使用されるローカル フォルダーに置き換えます`YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder`。 たとえば、レガシ SSIS パッケージ ストアが SQL Server 2016 にバインドされている場合は、`YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` に移動します。  `YourSQLServerDefaultCompatibilityLevel` の値は、[SQL Server の既定の互換性レベルの一覧](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)で確認することができます。

Azure Files 上に Azure-SSIS IR パッケージ ストアを構成した場合は、SSMS 2019 以降のバージョンで Azure-SSIS IR に接続すると、デプロイしたパッケージがそれらに表示されます。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>dtutil を使用してオンプレミスの MSDB から Azure の MSDB に複数のパッケージをデプロイする

 SQL Server でホストされている MSDB または MSDB 上のレガシ SSIS パッケージ ストアから、Azure SQL Managed Instance でホストされている MSDB に複数のパッケージをデプロイし、同時にそれらの保護レベルを切り替えるには、SSMS 上の SQL Server に接続し、SSMS の **オブジェクト エクスプローラー** で `Databases->System Databases->msdb` ノードを右クリックして **[新しいクエリ]** ウィンドウを開き、次の T-SQL スクリプトを実行します。 お客様のケースに固有の文字列をすべて置き換えてください。  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Azure SQL Managed Instance のプライベートおよびパブリックのエンドポイントを使用するには、それぞれ `YourSQLManagedInstanceEndpoint` を `YourSQLMIName.YourDNSPrefix.database.windows.net`/`YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` に置き換えます。

このスクリプトを使用すると、MSDB 内のすべてのパッケージ用に dtutil コマンド ラインが生成されます。これにより、複数選択、コピーと貼り付け、およびコマンド プロンプトでの実行が可能になります。

![dtutil コマンド ラインを生成する](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

MSDB 上に Azure-SSIS IR パッケージ ストアを構成した場合は、SSMS 2019 以降のバージョンで Azure-SSIS IR に接続すると、デプロイしたパッケージがそれらに表示されます。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>dtutil を使用してオンプレミスの MSDB から Azure Files へ複数のパッケージをデプロイする

 SQL Server でホストされている MSDB または MSDB 上のレガシ SSIS パッケージ ストアから、Azure Files に複数のパッケージをデプロイし、同時にそれらの保護レベルを切り替えるには、SSMS 上の SQL Server に接続し、SSMS の **オブジェクト エクスプローラー** で `Databases->System Databases->msdb` ノードを右クリックして **[新しいクエリ]** ウィンドウを開き、次の T-SQL スクリプトを実行します。 お客様のケースに固有の文字列をすべて置き換えてください。  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

このスクリプトを使用すると、MSDB 内のすべてのパッケージ用に dtutil コマンド ラインが生成されます。これにより、複数選択、コピーと貼り付け、およびコマンド プロンプトでの実行が可能になります。

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Azure Files 上に Azure-SSIS IR パッケージ ストアを構成した場合は、SSMS 2019 以降のバージョンで Azure-SSIS IR に接続すると、デプロイしたパッケージがそれらに表示されます。

## <a name="next-steps"></a>次のステップ

自動生成された ADF パイプラインを SSIS パッケージの実行アクティビティを使用して再実行または編集することや、ADF ポータルで新たに作成することができます。 詳細については、[ADF パイプラインで SSIS パッケージ実行アクティビティとして SSIS パッケージを実行する](./how-to-invoke-ssis-package-ssis-activity.md)に方法に関するページを参照してください。