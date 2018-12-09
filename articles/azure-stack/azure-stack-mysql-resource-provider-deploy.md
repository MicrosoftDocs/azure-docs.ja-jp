---
title: Azure Stack での MySQL データベースの使用 | Microsoft Docs
description: MySQL データベースを Azure Stack にサービスとしてデプロイする方法と、MySQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 2017e08361c9ff060fde96b78549133d48372e34
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853899"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Azure Stack への MySQL リソース プロバイダーのデプロイ

MySQL サーバー リソース プロバイダーを使用して、MySQL データベースを Azure Stack サービスとして公開します。 MySQL リソース プロバイダーは、Windows Server 2016 Server Core 仮想マシン (VM) 上でサービスとして実行されます。

> [!IMPORTANT]
> SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。

## <a name="prerequisites"></a>前提条件

Azure Stack MySQL リソース プロバイダーをデプロイする前に、いくつかの前提条件が満たされている必要があります。 これらの要件を満たすには、特権エンドポイント VM にアクセスできるコンピューターでこの記事の手順を実行します。

* まだ登録していない場合は、Azure Marketplace 項目をダウンロードできるよう、Azure に [Azure Stack を登録](.\azure-stack-registration.md)します。
* Azure モジュールと Azure Stack PowerShell モジュールは、このインストールを実行するシステムにインストールする必要があります。 そのシステムは、最新バージョンの .NET ランタイムを伴う Windows 10 または Windows Server 2016 のイメージである必要があります。 [PowerShell for Azure Stack をインストールする](.\azure-stack-powershell-install.md)を参照してください。
* **Windows Server 2016 Datacenter - Server Core** イメージをダウンロードして、必要な Windows Server Core VM を Azure Stack Marketplace に追加します。

* MySQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに展開します。

  >[!NOTE]
  >インターネットにアクセスできないシステムに MySQL プロバイダーをデプロイするには、[mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) ファイルをローカルパスにコピーします。 **DependencyFilesLocalPath** パラメーターを使用してパス名を提供します。

* リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。

  |最小の Azure Stack バージョン|MySQL RP バージョン|
  |-----|-----|
  |バージョン 1808 (1.1808.0.97)|[MySQL RP バージョン 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |バージョン 1804 (1.0.180513.1)|[MySQL RP バージョン 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* データセンターの統合の前提条件を満たしていることを確認します。

    |前提条件|リファレンス|
    |-----|-----|
    |条件付き DNS フォワーダーが正しく設定されている。|[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)|
    |リソース プロバイダー用の受信ポートが開いている。|[Azure Stack とデータセンターの統合 - エンドポイントの公開](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI 証明書のサブジェクトと SAN が正しく設定されている。|[Azure Stack デプロイの必須 PKI 前提条件](azure-stack-pki-certs.md#mandatory-certificates) [Azure Stack デプロイの PaaS 証明書の前提条件](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>証明書

_統合システムのインストールのみを対象_。 [Azure Stack のデプロイの PKI 要件](.\azure-stack-pki-certs.md#optional-paas-certificates)に関するページの「オプションの PaaS 証明書」セクションで説明されている SQL PaaS PKI 証明書を指定する必要があります。 **DependencyFilesLocalPath** パラメーターで指定された場所に .pfx ファイルを配置します。 ASDK システムの証明書は提供しないでください。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

前提条件がすべてインストールされたら、**DeployMySqlProvider.ps1** スクリプトを実行して MYSQL リソース プロバイダーをデプロイします。 DeployMySqlProvider.ps1 スクリプトは、Azure Stack のバージョンに応じてダウンロードした MySQL リソース プロバイダーのバイナリの一部として展開されます。

MySQL リソース プロバイダーをデプロイするには、管理者特権で新しい PowerShell ウィンドウ (PowerShell ISE ではない) を開き、MySQL リソース プロバイダーのバイナリ ファイルを抽出したディレクトリに変更します。 既に読み込まれている PowerShell モジュールによって発生する可能性のある問題を回避するには、新しい PowerShell ウィンドウを使用することをお勧めします。

**DeployMySqlProvider.ps1** スクリプトを実行して次のタスクを完了します。

* Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを公開して、ギャラリーを使用して MySQL データベースをデプロイできるようにします。
* ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
* ダウンロードした Windows Server 2016 Core イメージを使用して VM をデプロイした後、MySQL リソース プロバイダーをインストールします。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをオペレーター アカウントのローカルの Azure Resource Manager に登録します。

> [!NOTE]
> MySQL リソース プロバイダーのデプロイが開始され、**system.local.mysqladapter** リソース グループが作成されます。 このリソース グループに必要なデプロイが完了するまで最大で 75 分かかる可能性があります。

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 パラメーター

これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | MySQL リソースプロバイダー VM のローカル管理者アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **AzureEnvironment** | Azure Stack のデプロイに使用したサービス管理者アカウントの Azure 環境。 Azure AD のデプロイでのみ必須です。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure AD を使用している場合は **AzureChinaCloud** です。 | AzureCloud |
| **DependencyFilesLocalPath** | 統合システムの場合のみ、証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 切断された環境では、[mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) をこのディレクトリにダウンロードします。 必要に応じて、ここで 1 つの Windows Update MSU パッケージをコピーできます。 | _省略可能_ (統合システムまたは切断された環境には _必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |
| **AcceptLicense** | GPL ライセンスに同意するためのプロンプトをスキップします。  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>カスタム スクリプトを使用して MySQL リソース プロバイダーをデプロイする

リソース プロバイダーをデプロイする際の手動による構成を除外するには、次のスクリプトをカスタマイズできます。 必要に応じて、Azure Stack のデプロイに適した既定のアカウント情報とパスワードを変更します。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

リソース プロバイダーのインストール スクリプトが終了したら、最新の更新プログラムが表示されるようにブラウザーを最新の情報に更新します。

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

1. 管理ポータルにサービス管理者としてサインインします。
2. **[リソース グループ]** を選択します
3. **system.\<location\>.mysqladapter** リソース グループを選択します。
4. リソース グループの概要の概要ページで、失敗したデプロイは表示されていないはずです。
5. 最後に、管理者ポータルで**仮想マシン**を選択して、MySQL リソース プロバイダー VM が正常に作成され、実行されていることを確認します。

## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加する](azure-stack-mysql-resource-provider-hosting-servers.md)
