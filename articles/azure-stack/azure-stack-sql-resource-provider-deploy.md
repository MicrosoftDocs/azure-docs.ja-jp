---
title: "Azure Stack での SQL データベースの使用 | Microsoft Docs"
description: "SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: JeffGo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 018d556d52aa1a1f436460d9811c43f9b45bd440
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---

# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する


SQL Server リソース プロバイダー アダプターを使用して、SQL データベースを Azure Stack のサービスとして公開します。 リソース プロバイダーをインストールし、それを 1 つ以上の SQL Server インスタンスに接続した後で、SQL Server をホストする仮想マシン (VM) を毎回プロビジョニングすることなく、クラウドネイティブ アプリのデータベース、SQL ベースの Web サイト、SQL ベースのワークロードを作成できます。

リソース プロバイダーでは、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) のすべてのデータベース管理機能がサポートされるわけではありません。 たとえば、エラスティック データベース プールと、データベース パフォーマンスをスケールする機能はサポートされません。 API は、SQL DB と互換性がありません。


## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server リソース プロバイダー アダプターのアーキテクチャ
リソース プロバイダーは、Azure SQL Database のどのデータベース管理機能にも基づかず、これらの機能の提供もしません。 たとえば、エラスティック データベース プールと、データベースのパフォーマンスを自動的に増減する機能は使用できません。 ただし、リソース プロバイダーでは、同様の作成、読み取り、更新、および削除 (CRUD) 操作がサポートされます。

リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **SQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **SQL Server をホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。 

このリリースでは、SQL インスタンスは作成されなくなりました。 1 つ (以上) の外部 SQL インスタンスを作成するか、外部 SQL インスタンスへのアクセスを提供する必要があります。 使用可能ないくつかのオプションとして、[Azure Stack クイックスタート ギャラリー](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)のテンプレートと Marketplace 項目があります。 

>[!NOTE]
いずれかの SQL Marketplace 項目をダウンロードした場合は、SQL IaaS 拡張機能をダウンロードしてください。そうしないと、これらはデプロイされません。


## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ行っていない場合は、開発キットを登録し、Marketplace 管理を通じてダウンロード可能な Windows Server 2016 EVAL イメージをダウンロードします。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 .NET 3.5 ランタイムは必要なくなりました。

2. [SQL リソース プロバイダー バイナリ ファイルをダウンロード](https://aka.ms/azurestacksqlrp)し、開発キットのホストに抽出します。

3. 開発キットのホストにサインインし、SQL RP インストーラー ファイルを一時ディレクトリに抽出します。

4. このプロセスの一環として、Azure Stack ルート証明書が取得され、自己署名証明書が作成されます。 

    __省略可能:__ 独自に提供する必要がある場合は証明書を準備し、(インストール スクリプトに渡される) 証明書をカスタマイズする場合はローカル ディレクトリにコピーします。 以下の証明書が必要です。

    a. *.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書。 証明機関によって発行されるなど、この証明書が信頼されている必要があります (つまり、中間証明書を必要とせずに信頼チェーンが存在する必要があります)。(1 つのサイト証明書を、インストール時に指定した明示的な VM 名で使用できます)。

    b. Azure Stack のインスタンスの Azure Resource Manager で使用されるルート証明書。 これが見つからない場合は、ルート証明書が取得されます。


5. **新しい**管理者特権の PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. 1.2.9 または 1.2.10 以外の任意のバージョンの AzureRm または AzureStack PowerShell モジュールをインストールした場合は、それらを削除するように求められるか、インストールが続行されません。 これには、バージョン 1.3 以上も含まれます。

7. 以下に示すパラメーターを指定して DeploySqlProvider.ps1 スクリプトを実行します。

スクリプトでは次の手順が実行されます。

* 必要に応じて、Azure PowerShell の互換バージョンをダウンロードします。
* Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを公開して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
* 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイし、リソース プロバイダーをインストールします。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをローカル Azure Resource Manager (テナントと管理者) に登録します。

> [!NOTE]
> インストールに要する時間が 90 分を超える場合は、インストールが失敗し、スクリーンとログ ファイルにエラー メッセージが表示されることがありますが、デプロイは失敗した手順から再試行されます。 推奨されるメモリとコア仕様を満たしていないシステムは、SQL RP をデプロイできないことがあります。
>

PowerShell プロンプトから実行できる例を次に示します (ただし、必要に応じてアカウント情報とポータル エンドポイントを変更してください)。

```
# Install the AzureRM.Bootstrapper module
Install-Module -Name AzureRm.BootStrapper -Force

# Installs and imports the API Version Profile required by Azure Stack into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.10 -Force

# Download the Azure Stack Tools from GitHub and set the environment
cd c:\
Invoke-Webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
Expand-Archive master.zip -DestinationPath . -Force

# This endpoint may be different for your installation
Import-Module C:\AzureStack-Tools-master\Connect\AzureStack.Connect.psm1
Add-AzureRmEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external" 

# For AAD, use the following
$tenantID = Get-AzsDirectoryTenantID -AADTenantName "<your directory name>" -EnvironmentName AzureStackAdmin

# For ADFS, replace the previous line with
# $tenantID = Get-AzsDirectoryTenantID -ADFS -EnvironmentName AzureStackAdmin

$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeploySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "SqlRPRG" -VmName "SqlVM" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター
これらのパラメーターはコマンド ラインで指定できます。 指定しない場合、またはいずれかのパラメーター検証が失敗する場合は、必要なパラメーターの指定を求められます。

| パラメーター名 | Description | コメントまたは既定値 |
| --- | --- | --- |
| **DirectoryTenantID** | Azure または ADFS ディレクトリ ID (guid)。 | "_必須_" |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報を指定します。 Azure Stack のデプロイに使用したのと同じ資格情報を使用する必要があります。 | "_必須_" |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報を定義します。 このパスワードは、SQL **sa** アカウントにも使用されます。 | "_必須_" |
| **ResourceGroupName** | このスクリプトによって作成された項目の格納先となるリソース グループの名前を定義します。 たとえば、"*SqlRPRG*" などです。 |  "_必須_" |
| **VmName** | リソース プロバイダーをインストールする仮想マシンの名前を定義します。 たとえば、"*SqlVM*" などです。 |  "_必須_" |
| **DependencyFilesLocalPath** | 証明書ファイルはこのディレクトリにも配置する必要があります。 | "_省略可能_" |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード | "_必須_" |
| **MaxRetryCount** | 障害がある場合は、各操作を再試行する回数を定義します。| 2 |
| **RetryDuration** | 再試行間のタイムアウトを秒単位で定義します。 | 120 |
| **アンインストール** | リソース プロバイダーおよび関連付けられているすべてのリソース (以下のメモを参照) を削除します | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします | いいえ |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトが完了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. コンソール VM デスクトップで、**[Microsoft Azure Stack Portal]\(Microsoft Azure Stack ポータル\)** をクリックして、ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **[リソース グループ]** &gt; 使用したリソース グループをクリックし、ブレードの重要な部分 (上半分) が **["_date_" (Succeeded)]\(日付 (成功)\)** になっていることを確認します。

      ![SQL RP のデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="provide-capacity-by-connecting-to-a-hosting-sql-server"></a>ホスティング SQL Server に接続して容量を提供する

1. Azure Stack 管理ポータルにサービス管理者としてサインインします

2. 使用可能な SQL 仮想マシンがない場合は作成します。 Marketplace 管理は、SQL VM をデプロイするためのオプションをいくつか提供します。

3. **[リソース プロバイダー]** &gt; **[SQLAdapter]** &gt; **[Hosting Servers]\(ホスティング サーバー\)** &gt; **[+追加]** をクリックします。

    **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** ブレードでは、リソース プロバイダーのバックエンドとして機能する SQL Server の実際のインスタンスに SQL Server リソース プロバイダーを接続できます。

    ![ホスティング サーバー](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.PNG)

4. SQL Server インスタンスの接続詳細をフォームに入力します。

    ![新しいホスティング サーバー](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.PNG)

    > [!NOTE]
    > SQL インスタンスがテナントと管理者の Azure Resource Manager によってアクセスできる限り、リソース プロバイダーの管理下に置くことができます。 SQL インスタンスは RP に排他的に割り当てられている__必要があります__。

5. サーバーを追加する際に、サービス内容を区別するために新規または既存の SKU に割り当てる必要があります。 たとえば、データベース容量と自動バックアップを提供する SQL Enterprise インスタンスを使用したり、個々の部門用に高パフォーマンス サーバーを予約したりできます。テナントがデータベースを適切に配置でき、SKU 内のすべてのホスティング サーバーが同じ機能を持つように、SKU 名はプロパティを反映する必要があります。

    例:

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまでは、データベースを作成できません。


## <a name="create-your-first-sql-database-to-test-your-deployment"></a>最初の SQL データベースを作成してデプロイをテストします

1. Azure Stack 管理ポータルにサービス管理者としてサインインします。

2. **[+新規]** &gt; **[データ + ストレージ]** &gt; **[SQL Server Database (preview)]\(SQL Server データベース (プレビュー)\)** &gt; **[追加]** をクリックします。

3. **[データベース名]**、**[最大サイズ]** などのデータベースの詳細をフォームに入力し、必要に応じて他のパラメーターを変更します。 データベースの SKU の選択を求められます。 ホスティング サーバーが追加されると、それらに SKU が割り当てられ、SKU を構成するホスティング サーバーのプールにデータベースが作成されます。

    ![New database](./media/azure-stack-sql-rp-deploy/newsqldb.png)


4. ログイン設定: **[データベース ログイン]** と **[パスワード]** を入力します。 これは、このデータベースのみにアクセスするために作成される SQL 認証資格情報です。 ログイン ユーザー名はグローバルに一意である必要があります。 新しいログイン設定を作成するか、既存の設定を選択します。 同じ SKU を使用して他のデータベースのログイン設定を再利用することができます。

    ![新しいデータベース ログインの作成](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. フォームを送信し、デプロイが完了するまで待ちます。

    結果のブレードで、[接続文字列] フィールドに注目してください。 Azure Stack で SQL Server アクセスを必要とする任意のアプリケーション (たとえば、Web アプリ) でその文字列を使用できます。

    ![接続文字列の取得](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="add-capacity"></a>容量を追加する

Azure Stack ポータルに SQL ホストを追加し、それらに適切な SKU を関連付けることで、容量を追加します。 プロバイダー VM にインストールされているのとは別の SQL のインスタンスを使用する場合は、**[リソース プロバイダー]** &gt; **[SQLAdapter]** &gt; **[SQL Hosting Servers]\(SQL ホスティング サーバー\)** &gt; **[+追加]** をクリックします。

## <a name="making-sql-databases-available-to-tenants"></a>SQL データベースをテナントで使用できるようにする

プランとオファーを作成して、SQL データベースをテナントで使用できるようにします。 プランを作成し、Microsoft.SqlAdapter サービスをプランに追加し、既存のクォータを追加するか、新しいクォータを作成する必要があります。 クォータを作成する場合は、テナントに対応できる容量を指定できます。
    ![プランとオファーを作成してデータベースに含める](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="tenant-usage-of-the-resource-provider"></a>テナントでのリソース プロバイダーの使用

セルフサービス データベースは、テナント ポータル エクスペリエンスを通じて提供されます。

## <a name="removing-the-sql-adapter-resource-provider"></a>SQL アダプター リソース プロバイダーの削除

リソース プロバイダーを削除するには、最初に依存関係を削除することが重要です。

1. このバージョンのリソース プロバイダーについてダウンロードした元のデプロイ パッケージがあることを確認します。

2. リソース プロバイダーからすべてのテナント データベースを削除する必要があります (データは削除されません)。 これは、テナント自体で実行する必要があります。

3. 管理者は、SQL アダプターからホスティング サーバーを削除する必要があります

4. 管理者は、SQL アダプターを参照するプランをすべて削除する必要があります。

5. 管理者は、SQL アダプターに関連付けられている SKU とクォータをすべて削除する必要があります。

6. -Uninstall パラメーター、Azure Resource Manager エンドポイント、DirectoryTenantID、サービス管理者アカウントの資格情報を指定してデプロイ スクリプトを再実行します。



## <a name="next-steps"></a>次のステップ


[MySQL Server リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)や [App Services リソース プロバイダー](azure-stack-app-service-overview.md)のような他の [PaaS サービス](azure-stack-tools-paas-services.md)を試します。

