---
title: Windows 認証でデータ ストアとファイル共有にアクセスする
description: Windows 認証を使用してデータ ストアとファイル共有にアクセスするパッケージを実行するように、Azure SQL Database の SSIS カタログと Azure Data Factory の Azure-SSIS Integration Runtime を構成する方法について説明します。
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 8cb92407954ab5376850e57b0ee91d934480b025
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786891"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Azure の SSIS パッケージから Windows 認証を使用してデータ ストアとファイル共有にアクセスする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) 内の Azure-SSIS Integration Runtime (IR) で実行されている SSIS パッケージから、Windows 認証を使用して、SQL Server、ファイル共有、Azure Files などのデータ ストアにアクセスできます。 データ ストアは、オンプレミスであっても、Azure 仮想マシン (VM) でホストされていても、マネージド サービスとして Azure で実行されていてもかまいません。 オンプレミスの場合は、オンプレミス ネットワークに接続されている仮想ネットワーク (Microsoft Azure Virtual Network) に、Azure-SSIS IR を参加させる必要があります。[Azure-SSIS IR を Microsoft Azure Virtual Network に参加させる方法](./join-azure-ssis-integration-runtime-virtual-network.md)に関する記事をご覧ください。 Azure-SSIS IR で実行されている SSIS パッケージから Windows 認証でデータ ストアにアクセスするには、4 つの方法があります。

| 接続方法 | 有効な範囲 | セットアップ手順 | パッケージにおけるアクセス方法 | 資格情報のセットと接続されるリソースの数 | 接続されるリソースの種類 | 
|---|---|---|---|---|---|
| アクティビティ レベルの実行コンテキストを設定する | SSIS パッケージの実行アクティビティ単位 | ADF パイプラインで SSIS パッケージの実行アクティビティとして SSIS パッケージを実行するときに、"実行" コンテキストを設定するように、**Windows 認証** プロパティを構成します。<br/><br/> 詳しくは、[SSIS パッケージの実行アクティビティの構成](./how-to-invoke-ssis-package-ssis-activity.md)に関する記事をご覧ください。 | パッケージ内で直接リソースにアクセスします。たとえば、UNC パスを使用してファイル共有または Azure Files にアクセスする場合: `\\YourFileShareServerName\YourFolderName` または `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 接続されるすべてのリソースに対して資格情報のセットを 1 つだけサポート | - オンプレミスまたは Azure VM 上のファイル共有<br/><br/> - Azure Files ([Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md)に関する記事を参照)<br/><br/> - Windows 認証を使用するオンプレミス/Azure VM の SQL Server<br/><br/> - Windows 認証を使用するその他のリソース |
| カタログ レベルの実行コンテキストを設定する | Azure-SSIS IR 単位、ただしアクティビティ レベルの実行コンテキスト (上記参照) は設定するとオーバーライドされます | SSISDB `catalog.set_execution_credential` ストアド プロシージャを実行し、"実行" コンテキストを設定します。<br/><br/> 詳細については、この記事の以降の内容を参照してください。 | パッケージ内で直接リソースにアクセスします。たとえば、UNC パスを使用してファイル共有または Azure Files にアクセスする場合: `\\YourFileShareServerName\YourFolderName` または `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 接続されるすべてのリソースに対して資格情報のセットを 1 つだけサポート | - オンプレミスまたは Azure VM 上のファイル共有<br/><br/> - Azure Files ([Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md)に関する記事を参照)<br/><br/> - Windows 認証を使用するオンプレミス/Azure VM の SQL Server<br/><br/> - Windows 認証を使用するその他のリソース |
| `cmdkey` コマンドを使用して資格情報を保持する | Azure-SSIS IR 単位、ただしアクティビティ レベルまたはカタログ レベルの実行コンテキスト (上記参照) は設定するとオーバーライドされます | Azure-SSIS IR のプロビジョニングの際にカスタム セットアップ スクリプト (`main.cmd`) の `cmdkey` コマンドを実行します。たとえば、ファイル共有、Azure Files、または SQL Server を使用する場合:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`、または <br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)」を参照してください。 | パッケージ内で直接リソースにアクセスします。たとえば、UNC パスを使用してファイル共有または Azure Files にアクセスする場合: `\\YourFileShareServerName\YourFolderName` または `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 接続されるリソースごとに複数の資格情報のセットをサポート | - オンプレミスまたは Azure VM 上のファイル共有<br/><br/> - Azure Files ([Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md)に関する記事を参照)<br/><br/> - Windows 認証を使用するオンプレミス/Azure VM の SQL Server<br/><br/> - Windows 認証を使用するその他のリソース |
| パッケージの実行時にドライブをマウントする (非永続化) | パッケージ単位 | プロセス実行タスクの `net use` コマンドを実行します。このコマンドは、パッケージ内の制御フローの先頭に追加されます。たとえば、次のように指定します。`net use D: \\YourFileShareServerName\YourFolderName` | マップ済みドライブを使用してファイル共有にアクセスします。 | ファイル共有ごとに複数のドライブをサポート | - オンプレミスまたは Azure VM 上のファイル共有<br/><br/> - Azure Files ([Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md)に関する記事を参照) |
|||||||

> [!WARNING]
> Windows 認証を使用してデータ ストアに接続するときに上記のどの方法も使用しないと、Windows 認証に依存するパッケージはデータ ストアにアクセスできず、実行時に失敗します。 

この記事の残りの部分では、Windows 認証を使用してデータ ストアにアクセスするパッケージを Azure-SSIS IR 上で実行するために、SQL Database/SQL Managed Instance でホストされている SSIS カタログ (SSISDB) を構成する方法について説明します。 

## <a name="you-can-only-use-one-set-of-credentials"></a>使用できる資格情報は 1 セットのみ

SSIS パッケージ内の Windows 認証を使用する場合は、資格情報のセットを 1 つだけ使用できます。 この記事の手順を実行するときに指定したドメインの資格情報は、資格情報を変更または削除するまで、Azure-SSIS IR のすべてのパッケージの実行 (インタラクティブまたはスケジュール) に適用されます。 パッケージを資格情報のセットが異なる複数のデータ ストアに接続しなければならない場合は、前述の別の方法を検討する必要があります。

## <a name="provide-domain-credentials-for-windows-authentication"></a>Windows 認証のドメイン資格情報を提供する

パッケージが Windows 認証を使用して、オンプレミスのデータ ストアにアクセスできるようにするドメイン資格情報を提供するには、次のことを行います。

1. SQL Server Management Studio (SSMS) または別のツールを使用して、SSISDB をホストする SQL Database/SQL Managed Instance に接続します。 詳しくは、[Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)に関する記事をご覧ください。

2. SSISDB を現在のデータベースとして使用し、クエリ ウィンドウを開きます。

3. 次のストアド プロシージャを実行し、適切なドメイン資格情報を提供します。

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. SSIS パッケージを実行します。 パッケージは、指定された資格情報を使用して、Windows 認証でオンプレミスのデータ ストアにアクセスします。

### <a name="view-domain-credentials"></a>ドメイン資格情報の表示

アクティブなドメイン資格情報を表示するには、次のことを行います。

1. SSMS または別のツールを使用して、SSISDB をホストする SQL Database/SQL Managed Instance に接続します。 詳しくは、[Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)に関する記事をご覧ください。

2. SSISDB を現在のデータベースとして使用し、クエリ ウィンドウを開きます。

3. 次のストアド プロシージャを実行し、出力を調べます。

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>ドメイン資格情報のクリア
この記事の説明に従って指定した資格情報を削除するには、次のことを行います。

1. SSMS または別のツールを使用して、SSISDB をホストする SQL Database/SQL Managed Instance に接続します。 詳しくは、[Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)に関する記事をご覧ください。

2. SSISDB を現在のデータベースとして使用し、クエリ ウィンドウを開きます。

3. 次のストアド プロシージャを実行します。

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>オンプレミスの SQL Server に接続する

オンプレミスの SQL Server に接続できるかどうかを確認するには、次のことを行います。

1. このテストを実行するには、ドメインに参加していないコンピューターを見つけます。

2. ドメインに参加していないコンピューターで、次のコマンドを実行し、使用したいドメイン資格情報を使用して SSMS を起動します。

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. SSMS から、オンプレミスの SQL Server に接続できるかどうかを確認します。

### <a name="prerequisites"></a>前提条件

Azure で実行されているパッケージからオンプレミスの SQL Server にアクセスするには、次のことを行います。

1.  SQL Server 構成マネージャーで、TCP/IP プロトコルを有効にします。

2. Windows ファイアウォール経由のアクセスを許可します。 詳しくは、「[SQL Server のアクセスを許可するための Windows ファイアウォールの構成](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)」をご覧ください。

3. オンプレミスの SQL Server に接続されている Microsoft Azure Virtual Network に Azure-SSIS IR を参加させます。  詳細については、[Azure-SSIS IR を Microsoft Azure Virtual Network に参加させる方法](./join-azure-ssis-integration-runtime-virtual-network.md)に関する記事を参照してください。

4. この記事で説明されているように、SSISDB の `catalog.set_execution_credential` ストアド プロシージャを使用して資格情報を提供します。

## <a name="connect-to-a-file-share-on-premises"></a>オンプレミスのファイル共有に接続する

オンプレミスのファイル共有に接続できるかどうかを確認するには、次のことを行います。

1. このテストを実行するには、ドメインに参加していないコンピューターを見つけます。

2. ドメインに参加していないコンピューターで、次のコマンドを実行します。 これらのコマンドは、使用したいドメイン資格情報を使用してコマンド プロンプト ウィンドウを開き、ディレクトリの一覧を取得することによって、オンプレミスのファイル共有への接続をテストします。

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. オンプレミスのファイル共有に対してディレクトリの一覧が返されるかどうかを確認します。

### <a name="prerequisites"></a>前提条件

Azure で実行されているパッケージからオンプレミスのファイル共有にアクセスするには、次のことを行います。

1. Windows ファイアウォール経由のアクセスを許可します。

2. オンプレミスのファイル共有に接続されている Microsoft Azure Virtual Network に Azure-SSIS IR を参加させます。  詳細については、[Azure-SSIS IR を Microsoft Azure Virtual Network に参加させる方法](./join-azure-ssis-integration-runtime-virtual-network.md)に関する記事を参照してください。

3. この記事で説明されているように、SSISDB の `catalog.set_execution_credential` ストアド プロシージャを使用して資格情報を提供します。

## <a name="connect-to-a-file-share-on-azure-vm"></a>Azure VM のファイル共有に接続する

Azure で実行されているパッケージから Azure VM のファイル共有にアクセスするには、次のことを行います。

1. SSMS または別のツールを使用して、SSISDB をホストする SQL Database/SQL Managed Instance に接続します。 詳しくは、[Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)に関する記事をご覧ください。

2. SSISDB を現在のデータベースとして使用し、クエリ ウィンドウを開きます。

3. 次のストアド プロシージャを実行し、適切なドメイン資格情報を提供します。

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Azure Files のファイル共有への接続

Azure Files に関する詳細については、「[Azure ファイル](https://azure.microsoft.com/services/storage/files/)」を参照してください。

Azure で実行されているパッケージから Azure Files のファイル共有にアクセスするには、次のことを行います。

1. SSMS または別のツールを使用して、SSISDB をホストする SQL Database/SQL Managed Instance に接続します。 詳しくは、[Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)に関する記事をご覧ください。

2. SSISDB を現在のデータベースとして使用し、クエリ ウィンドウを開きます。

3. 次のストアド プロシージャを実行し、適切なドメイン資格情報を提供します。

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>次のステップ

- パッケージをデプロイします。 詳しくは、[SSMS を使用した Azure への SSIS プロジェクトのデプロイ](/sql/integration-services/ssis-quickstart-deploy-ssms)に関する記事をご覧ください。
- パッケージを実行します。 詳しくは、[SSMS を使用した Azure での SSIS パッケージの実行](/sql/integration-services/ssis-quickstart-run-ssms)に関する記事をご覧ください。
- パッケージをスケジュールします。 詳細については、「[Azure で SSIS パッケージのスケジュールを設定する](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)」を参照してください。
