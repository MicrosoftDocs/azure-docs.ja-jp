---
title: "Azure Stack で MySQL データベースを PaaS として使用する | Microsoft Docs"
description: "Azure Stack で MySQL リソース プロバイダーをデプロイし、MySQL データベースをサービスとして提供する方法を説明します"
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
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 45c7edcc645e82107805b3e62d87655a830fb22a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で MySQL データベースを使用する


Azure Stack に MySQL リソース プロバイダーをデプロイできます。 リソース プロバイダーをデプロイしたら、Azure Resource Manager デプロイ テンプレートを使用して MySQL サーバーおよびデータベースを作成し、MySQL データベースをサービスとして提供できます。 MySQL データベースは、Web サイトで一般的であり、多くの Web サイト プラットフォームをサポートしています。 たとえば、リソース プロバイダーをデプロイした後に、Azure Stack のサービスとしての Azure Web Apps プラットフォーム (PaaS) アドオンから WordPress Web サイトを作成できます。

インターネットにアクセスできないシステムに MySQL プロバイダーをデプロイするには、ファイル [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) をローカル共有にコピーし、プロンプトが表示されたら、その共有名を指定できます (下記参照)。 Azure および Azure Stack PowerShell モジュールもインストールする必要があります。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL サーバー リソース プロバイダー アダプターのアーキテクチャ

リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **MySQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **MySQL サーバーをホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。 

このリリースでは、MySQL インスタンスは作成されなくなりました。 それらを作成するか、外部 SQL インスタンスへのアクセスを提供する必要があります。 [Azure Stack クイックスタート ギャラリー](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) にアクセスして、自動的に MySQL サーバーを作成できるサンプル テンプレートを取得するか、または Marketplace から MySQL サーバーをダウンロードしてデプロイできます。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は、登録して、Marketplace 管理を通じてダウンロード可能な Windows Server 2016 Datacenter - Eval イメージをダウンロードします。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。

2. [MySQL リソース プロバイダー バイナリ ファイルをダウンロード](https://aka.ms/azurestackmysqlrp)し、開発キット ホストに抽出します。

3. 開発キット ホストにサインインし、MySQL RP インストーラー ファイルを一時ディレクトリに抽出します。

4. このプロセスの一環として、Azure Stack ルート証明書が取得され、自己署名証明書が作成されます。 

    __省略可能:__ 独自に提供する必要がある場合は証明書を準備し、(インストール スクリプトに渡される) 証明書をカスタマイズする場合はローカル ディレクトリにコピーします。 以下のものが必要になります。

    a. *.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書。 証明機関によって発行されるなど、この証明書が信頼されている必要があります (つまり、中間証明書を必要とせずに信頼チェーンが存在する必要があります)。(1 つのサイト証明書を、インストール時に指定した明示的な VM 名で使用できます)。

    b. Azure Stack のインスタンスの Azure Resource Manager で使用されるルート証明書。 これが見つからない場合は、ルート証明書が取得されます。

5. **新しい**管理者特権の PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. 1.2.9 または 1.2.10 以外の任意のバージョンの AzureRm または AzureStack PowerShell モジュールをインストールした場合は、それらを削除するように求められるか、インストールが続行されません。 これには、バージョン 1.3 以上も含まれます。

7. DeployMySqlProvider.ps1 を実行します。

このスクリプトでは次の手順が実行されます。

* 必要に応じて、Azure PowerShell の互換バージョンをダウンロードします。
* MySQL コネクタ バイナリをダウンロードします (これはオフラインで提供できます)。
* Azure Stack ストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを公開して、ギャラリーを通じて MySQL データベースをデプロイできるようにします。
* リソース プロバイダーをホストする仮想マシン (VM) をデプロイします。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをローカル Azure Resource Manager に登録します。

コマンド ラインに少なくとも必須パラメーターを指定するか、またはパラメーターを指定せずに実行した場合は、パラメーターを入力するように求められます。 

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
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeployMySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "MySqlRG" -VmName "MySQLRP" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath
 ```

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 パラメーター

これらのパラメーターをコマンド ラインで指定できます。 指定しない場合、またはいずれかのパラメーター検証が失敗する場合は、必要なパラメーターの指定を求められます。

| パラメーター名 | Description | コメントまたは既定値 |
| --- | --- | --- |
| **DirectoryTenantID** | Azure または ADFS ディレクトリ ID (guid) | _必須_ |
| **ArmEndpoint** | Azure Stack 管理 Azure Resource Manager エンドポイント | _必須_ |
| **TenantArmEndpoint** | Azure Stack テナント Azure Resource Manager エンドポイント | _必須_ |
| **AzCredential** | Azure Stack Service 管理者アカウント資格情報 (Azure Stack の展開に使用した同じアカウントを使用) | _必須_ |
| **VMLocalCredential** | MySQL リソース プロバイダー VM のローカル管理者アカウント | _必須_ |
| **ResourceGroupName** | このスクリプトで作成された項目のリソース グループ |  _必須_ |
| **VmName** | リソース プロバイダーを保持している VM の名前 |  _必須_ |
| **AcceptLicense** | プロンプトをスキップして、GPL ライセンス (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) に同意します | |
| **DependencyFilesLocalPath** | [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi) を格納するローカル共有のパス。 それらを提供する場合は、証明書ファイルもこのディレクトリに配置する必要があります。 | _省略可能_ |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード | _必須_ |
| **MaxRetryCount** | 失敗した場合、各操作が再試行されます | 2 |
| **RetryDuration** | 再試行間のタイムアウト (秒単位) | 120 |
| **アンインストール** | リソース プロバイダーを削除します | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします | いいえ |


システムのパフォーマンスとダウンロード速度によっては、インストールに 20分しかかからない場合も、数時間にも及ぶことがあります。 MySQLAdapter ブレードを使用できない場合は、管理ポータルを更新する必要があります。

> [!NOTE]
> インストールに要する時間が 90 分を超える場合は、インストールが失敗している可能性があり、画面とログ ファイルにエラー メッセージが表示されることがあります。 失敗した手順からデプロイが再試行されます。 推奨されるメモリとコア仕様を満たしていないシステムは、MySQL RP をデプロイできないことがあります。


## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>MySQL ホスティング サーバに接続して容量を提供する

1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[リソース プロバイダー]** &gt; **[MySQLAdapter]** &gt; **[Hosting Servers] \(ホスティング サーバー)** &gt; **[+追加]** をクリックします。

    **[MySQL Hosting Servers] \(MySQL ホスティング サーバー)** ブレードでは、リソース プロバイダーのバックエンドとして機能する MySQL サーバーの実際のインスタンスに MySQL サーバー リソース プロバイダーを接続できます。

    ![ホスティング サーバー](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. MySQL サーバー インスタンスの接続詳細をフォームに入力します。 短い VM 名ではなく、完全修飾ドメイン名 (FQDN) または有効な IPv4 アドレスを指定します。 このインストールでは、既定の MySQL インスタンスが提供されなくなりました。 指定したサイズは、リソース プロバイダーがデータベース容量を管理するのに役立ちます。 それはデータベース サーバーの物理容量に近い値にする必要があります。

    > [!NOTE]
    > MySQL インスタンスがテナントと管理者の Azure Resource Manager によってアクセスできる限り、リソース プロバイダーの管理下に置くことができます。 MySQL インスタンスは RP に排他的に割り当てられている__必要があります__。

4. サーバーを追加する際に、サービス内容を区別できるように新規または既存の SKU に割り当てる必要があります。 たとえば、データベース容量と自動バックアップを提供するエンタープライズ インスタンスを使用したり、個々の部門用に高パフォーマンス サーバーを予約したりできます。テナントがデータベースを適切に配置でき、SKU 内のすべてのホスティング サーバーが同じ機能を持つように、SKU 名はプロパティを反映する必要があります。


>[!NOTE]
SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまで、データベースを作成できません。


## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>最初の MySQL データベースを作成してデプロイをテストする


1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[新規]** ボタン &gt; **[データ + ストレージ]** &gt; **[MySQL Database (preview)] \(MySQL データベース (プレビュー))** をクリックします。

3. フォームにデータベースの詳細を入力します。

    ![テスト MySQL データベースの作成](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. SKU を選択します。

    ![SKU の選択](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. ログイン設定を作成します。 ログイン設定は再利用するか、新しい設定を作成できます。 これには、データベースのユーザー名とパスワードが含まれます。

    接続文字列には、実際のデータベース サーバー名が含まれます。 それをポータルからコピーします。

    ![MySQL データベースの接続文字列の取得](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> ユーザー名の長さは MySQL 5.7 では 32文字、またはそれ以前のエディションでは 16文字を超えることはできません。 これは MySQL 実装の制限です。


## <a name="add-capacity"></a>容量を追加する

容量を追加するには、Azure Stack ポータルに追加の MySQL サーバを追加します。 MySQL の別のインスタンスを使用する場合は、**[リソース プロバイダー]** &gt; **[MySQLAdapter]** &gt; **[MySQL Hosting Servers] \(MySQL ホスティング サーバー)** &gt; **[+追加]** をクリックします。


## <a name="making-mysql-databases-available-to-tenants"></a>MySQL データベースをテナントで使用できるようにする
プランとサービスを作成して、MySQL データベースをテナントで使用できるようにします。 Microsoft.MySqlAdapter サービスを追加したり、クォータを追加したりします。

![データベースに含めるプランとサービスの作成](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>MySQL アダプター リソース プロバイダーの削除

リソース プロバイダーを削除するには、最初にすべての依存関係を削除する必要があります。

1. このバージョンのリソース プロバイダーに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. リソース プロバイダーからすべてのテナント データベースを削除する必要があります (データは削除されません)。 これは、テナント自体で実行する必要があります。

3. テナントを名前空間から登録解除する必要があります。

4. 管理者は、MySQL アダプターからホスティング サーバーを削除する必要があります

5. 管理者は、MySQL アダプターを参照するすべてのプランを削除する必要があります。

6. 管理者は、MySQL アダプターに関連付けられているすべてのクォータを削除する必要があります。

7. -Uninstall パラメーター、Azure Resource Manager エンドポイント、DirectoryTenantID、サービス管理者アカウントの資格情報を指定してデプロイ スクリプトを再実行します。




## <a name="next-steps"></a>次のステップ


[SQL Server リソース プロバイダー](azure-stack-sql-resource-provider-deploy.md)や [App Services リソース プロバイダー](azure-stack-app-service-overview.md)のような他の [PaaS サービス](azure-stack-tools-paas-services.md)を試します。

