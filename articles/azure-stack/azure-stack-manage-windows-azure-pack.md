---
title: "Azure Stack からの Windows Azure Pack 仮想マシンの管理 | Microsoft Docs"
description: "Azure Stack 内のユーザー ポータルから Windows Azure Pack (WAP) VM を管理する方法について説明します。"
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>Azure Stack からの Windows Azure Pack 仮想マシンの管理

*適用対象: Azure Stack 開発キット*

Azure Stack Development Kit では、Azure Stack ユーザー ポータルから Windows Azure Pack 上で実行されているテナント仮想マシンへのアクセスを有効にできます。 ユーザーは Azure Stack ポータルを使用して、その既存の IaaS 仮想マシンおよび仮想ネットワークを管理できます。 これらのリソースは、基になる Service Provider Foundation (SPF) および Virtual Machine Manager (VMM) コンポーネント経由で Windows Azure Pack で使用可能になります。 具体的には、ユーザーは次のことができます。

* リソースを参照する
* 構成値を検証する
* 仮想マシンを停止または起動する
* リモート デスクトップ プロトコル (RDP) 経由で仮想マシンに接続する
* チェックポイントを作成および管理する
* 仮想マシンおよび仮想ネットワークを削除する

この機能は、Azure Stack 用の Windows Azure Pack コネクタ (プレビュー) によって提供されます。 この記事では、単一ノード Azure Stack 環境用のコネクタを構成する方法を示します。

コネクタのこのプレビュー リリースについては、次の点に注意してください。
* Windows Azure Pack コネクタは運用環境のデプロイではなく、テスト環境 (Azure Stack と Windows Azure Pack の両方共) でのみ使用してください。
* Windows Azure Pack 更新プログラム ロールアップ (UR) 9.1 以降と System Center SPF および VMM UR 9 以降が必要です。
* VMM および SPF コンポーネントは、System Center 2012 R2 または System Center 2016 のどちらでもかまいません。
* Azure Stack と Windows Azure Pack の両方で構成手順を実行する必要があります。
* これらの指示は、Cloud Platform System (CPS) 以外の環境に適用されます。
* 既知の問題を確認するには、「[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)」を参照してください。


## <a name="architecture"></a>アーキテクチャ
次の図は、Windows Azure Pack コネクタの主要なコンポーネントを示しています。

![Windows Azure Pack コネクタのコンポーネント](media/azure-stack-manage-wap/image1.png)

次の詳細事項に注意してください。
* Azure Stack ユーザー ポータルは、両方のクラウド (Azure Stack と Windows Azure Pack) からリソース情報にアクセスします。
* ユーザーには、この両方の環境で有効なアカウントが必要です。
* Azure Stack ユーザー ポータルには、Windows Azure Pack 上で実行されているコンポーネントへのネットワーク アクセスが必要です。
* 図の **WAP** セクションでは、新しい Windows Azure Pack コネクタ モジュール (WAP 拡張機能およびコネクタ) と、SPF および VMM コンポーネントとの既存の Windows Azure Pack テナント API を確認できます。


## <a name="identity-management"></a>ID 管理
Windows Azure Pack テナント API は、Azure Stack セキュリティ トークン サービス (STS) を信頼している必要があります。

ユーザーが Windows Azure Pack リソースをターゲットとする Azure Stack ポータル経由でアクションを実行すると、そのポータルは Windows Azure Pack テナント API を使用します。 そのため、提供されるユーザー認証トークンは信頼できる STS からのものである必要があります。 次の図を参照してください。

![Windows Azure Pack コネクタの認証の図](media/azure-stack-manage-wap/image2.png)

開発キット環境では、Windows Azure Pack と Azure Stack には独立した ID プロバイダーがあります。 Azure Stack ポータルから両方の環境にアクセスするユーザーは、両方の ID プロバイダーに同じユーザー プリンシパル名 (UPN) を持っている必要があります。 たとえば、アカウント *azurestackadmin@azurestack.local* は Windows Azure Pack の STS にも存在する必要があります。 AD FS が送信の信頼関係をサポートするように設定されていない場合は、Windows Azure Pack コンポーネント (テナント API) から AD FS の Azure Stack インスタンスへの信頼を確立します。

## <a name="prerequisites"></a>前提条件

### <a name="download-the-windows-azure-pack-connector"></a>Windows Azure Pack コネクタのダウンロード
[Microsoft ダウンロード センター](https://aka.ms/wapconnectorazurestackdlc)で、.exe ファイルをダウンロードし、それをローカル コンピューターに抽出します。 後で、その内容を Windows Azure Pack 環境にアクセスできるコンピューターにコピーします。

### <a name="deployment-option-requirement"></a>デプロイ オプションの要件
Windows Azure Pack と統合するには、AD FS オプションまたは Azure Active Directory オプションを使用して Azure Stack をデプロイできます。

### <a name="connectivity-requirements"></a>接続の要件
1. Azure Stack ユーザー ポータルにアクセスするコンピューターから、Web ブラウザー経由で Windows Azure Pack テナント ポータルにアクセスできることを確認してください。
2. Azure Stack 上の AzS-WASP01 仮想マシンは、Windows Azure Pack テナント ポータル コンピューターに接続できる必要があります。 ネットワーク接続を確認するには、Ping.exe を使用します。 
3.  新しいコネクタ サービスの有効な証明書が必要です。 これらの SSL 証明書は、信頼できる証明機関 (CA) によって発行されている必要があります。 自己署名証明書を使用することはできません。 SSL 証明書は、Azure Stack (具体的には AzS-WASP01 VM) や、テナントが Azure Stack ユーザー ポータルにアクセスするために使用する可能性のあるその他のすべてのコンピューターによって信頼されている必要があります。
 
    >[!NOTE]
    AzS-WASP01 は Server Core インストール オプションで Windows Server を実行するため、Certutil.ext などのコマンドライン ツールを使用して証明書をインポートできます。 たとえば、.cer ファイルを c:\temp on AzS-WASP01 にコピーしてから、コマンド **certutil -addstore "CA" "c:\temp\certname.cer"** を実行できます。

4.  Azure Stack ポータル経由で Windows Azure Pack テナント仮想マシンへの RDP 接続を確立するには、Windows Azure Pack 環境がテナント VM へのリモート デスクトップ トラフィックを許可する必要があります。
5.  Azure Stack テナント仮想マシンと Windows Azure Pack テナント仮想マシンの間の接続のためには、それらの外部 IP アドレスが 2 つの環境にまたがってルーティング可能である必要があります。 この接続には、これらの環境間で仮想マシン名を解決するための DNS サーバーの関連付けも含まれる可能性があります。

### <a name="iis-requirements"></a>IIS の要件
Windows Azure Pack テナント ポータルをホストするコンピューター (これは物理ホスト、仮想マシン、または複数の仮想マシンである可能性があります) には、URL 書き換え IIS 拡張機能がインストールされている必要があります。 まだインストールされていない場合は、それを[ここ](https://www.iis.net/downloads/microsoft/url-rewrite)からダウンロードできます。 複数の仮想マシンがテナント ポータルをホストする場合は、この拡張機能を各仮想マシンにインストールします。

## <a name="configure-azure-stack"></a>Azure Stack の構成
Windows Azure Pack コネクタを構成する前に、Azure Stack ユーザー ポータルでマルチクラウド モードを有効にする必要があります。 このモードでは、ユーザーはどのクラウドからリソースにアクセスするかを選択できます。

マルチクラウド モードを有効にするには、Azure Stack デプロイの後に Add-AzurePackConnector.ps1 スクリプトを実行する必要があります。 次の表で、スクリプト パラメーターについて説明します。


|  パラメーター | Description | 例 |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Windows Azure Pack コネクタの URI。 これらの URI は、Windows Azure Pack テナント ポータルに対応している必要があります。 | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (既定では、ポート値は 40005 です。) |  
| AzureStackCloudName | ローカルの Azure Stack クラウドを表すラベル。| "AzureStack" |
| DisableMultiCloud | マルチクラウド モードを無効にするためのスイッチ。| 該当なし |
| | |

Add-AzurePackConnector.ps1 スクリプトはデプロイの直後か、または後で実行できます。 このスクリプトをデプロイの直後に実行するには、Azure Stack デプロイが完了した同じ Windows PowerShell セッションを使用します。 それ以外の場合は、(azurestackadmin アカウントとしてサインインされた) 管理者として新しい Windows PowerShell セッションを開くことができます。

1. 次のコマンドを (環境に固有の値と共に) 使用して、Add-AzurePackConnector.ps1 スクリプトを実行します。 Add-AzurePackConnector スクリプトでは、複数の Windows Azure Pack コネクタ エンドポイントを追加できることに注意してください。
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> 現在のビルドには、Add-AzurePackConnector スクリプトが終了した後、ポーリング ループが終了するまで長時間 (数分) にわたってスクリプトがそのループ内に残るという問題が存在します。 **VERBOSE: Step 'Configure Azure Pack Connector' status: 'Success' (詳細: 手順 'Azure Pack コネクタの構成' 状態: '成功')** というメッセージが表示された後、スクリプトを停止するか、またはそのまま停止するまで待つことができます。 構成は既に成功しているため、それらに違いはありません。

2. このスクリプトによって生成された出力ファイル (指定した Windows Azure Pack 環境ごとに 1 つ) をメモしておきます。 これらのファイルは、\\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput にあります。 これらのファイルには、ターゲットの Windows Azure Pack 環境を構成するために必要な情報が含まれています。 このファイルは、この手順内の後でパラメーターとしてスクリプトに渡します。 このファイルには、次の情報が含まれています。

    * **AzurePackConnectorEndpoint**: Windows Azure Pack コネクタ サービスへのエンドポイントが含まれています。
    * **AuthenticationIdentityProviderPartner**: 次の値のペアが含まれています。
        * Windows Azure Pack テナント API が Azure Stack ポータル拡張機能からの呼び出しを受け付けるために信頼している必要のある認証トークン署名証明書。

        * 署名証明書に関連付けられた "領域"。 例: https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/。

3.  出力ファイルを含むフォルダー (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) を参照し、それらのファイルをローカル コンピューターにコピーします。 これらは、AzurePack-06-27-15-50.txt のようなファイルです。

4.  構成をテストします。

    a. ブラウザーを開き、Azure Stack ユーザー ポータル (https://portal.local.azurestack.external/) にサインインします。
    
    b. テナントとしてサインインし、ポータルが読み込まれた後、Azure Pack クラウドからサブスクリプションまたは拡張機能をフェッチできないことに関するエラーが表示されます。 **[OK]** をクリックして、これらのメッセージを閉じます。 (これらのエラー メッセージは、Windows Azure Pack を構成した後に消えます。)

    c. ポータルの左上隅にある **[クラウド]** ドロップダウン リストに注意してください。

    ![Azure Stack ユーザー インターフェイス内のクラウド セレクター](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>Windows Azure Pack の構成
このコネクタ プレビュー リリースでのみ、Windows Azure Pack には手動の構成が必要です。

>[!IMPORTANT]
このプレビュー リリースの場合、Windows Azure Pack コネクタは運用環境のデプロイではなく、テスト環境でのみ使用してください。

1.  Windows Azure Pack テナント ポータル仮想マシンにコネクタ MSI ファイルをインストールし、証明書をインストールします。 (複数のテナント ポータル仮想マシンがある場合は、各仮想マシンでこの手順を完了する必要があります。)

    a. ファイル エクスプローラーで、**WAPConnector** フォルダー (前にダウンロードしたもの) をテナント ポータル仮想マシン内の **c:\temp** という名前のフォルダーにコピーします。

    b. テナント ポータル仮想マシンへのコンソールまたは RDP 接続を開きます。

    c. ディレクトリを **c:\temp\wapconnector\setup\scripts** に変更し、**Install-Connector.ps1** スクリプトを実行して 3 つの MSI ファイルをインストールします。 パラメーターは必要ありません。

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. ディレクトリを **c:\inetpub** に変更し、次の 3 つの新しいサイトがインストールされていることを確認します。

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. 同じ **c:\temp\wapconnector\setup\scripts** フォルダーから、**Configure-Certificates.ps1** スクリプトを実行して証明書をインストールします。 既定では、Windows Azure Pack でテナント ポータル サイトに対して使用可能な同じ証明書が使用されます。 これが (Azure Stack AzS-WASP01 仮想マシンや、Azure Stack ポータルにアクセスするすべてのクライアント コンピューターによって信頼されている) 有効な証明書であることを確認してください。 そうでない場合は、通信が機能しません。 (あるいは、-Thumbprint パラメーターを使用して、証明書の拇印をパラメーターとして明示的に渡すこともできます。)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 これらの 3 つのサービスの構成を完了するには、**Configure-WapConnector.ps1** スクリプトを実行して Web.config ファイル パラメーターを更新します。

    |  パラメーター | Description | 例 |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Windows Azure Pack テナント ポータルの FQDN。 | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure Pack テナント API の FQDN。 | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Azure Stack ユーザー ポータルの FQDN。 | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. 複数のテナント ポータル仮想マシンがある場合は、これらの各仮想マシンに対して手順 1. を繰り返します。

2. 各 Windows Azure Pack テナント API 仮想マシンに新しいテナント API MSI をインストールします。

    a. ロード バランサーが使用されている場合は、仮想マシンをオフラインとしてマークすることもできます。

    b. ファイル エクスプローラーで、**WAPConnector** フォルダーを各テナント API マシン上の **c:\temp** という名前のフォルダーにコピーします。

    c. 前に保存した AzurePackConnectorOutput.txt ファイルを **c:\temp\WAPConnector** にコピーします。

    d. ファイルをコピーした先のテナント API VM へのコンソールまたは RDP 接続を開きます。
    
    e. ディレクトリを **c:\temp\wapconnector\setup\scripts** に変更し、**Update-TenantAPI.ps1** を実行します。 この新しいバージョンの WAP テナント API には、現在の STS との信頼関係だけでなく、Azure Stack 内の AD FS のインスタンスとの信頼関係も有効にするための変更が含まれています。

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。  テナント API を実行しているその他のすべての仮想マシンに対して手順 2. を繰り返します。
3. テナント API VM のうちの **1 つだけ**から、Configure-TrustAzureStack.ps1 スクリプトを実行して、テナント API と Azure Stack 上の AD FS インスタンスの間の信頼関係を追加します。 Microsoft.MgmtSvc.Store データベースへの sysadmin アクセス権を持つアカウントを使用する必要があります。 このスクリプトには、次のパラメーターがあります。

    | パラメーター | Description | 例 |
    | --------- | ------------| ------- |
   | SqlServer | Microsoft.MgmtSvc.Store データベースを含む SQL Server の名前。 このパラメーターは必須です。 | SQLServer | 
   | DataFile | 以前の Azure Stack マルチクラウド モードの構成中に生成された出力ファイル。 このパラメーターは必須です。 | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | SQL Server インスタンスに接続するときに、スクリプトから対話的に、使用する SQL 認証の資格情報を入力するよう求めるプロンプトが表示されることを示します。 指定される資格情報は、データベースやスキーマをアンインストールしたり、ユーザー ログインを削除したりするための十分なアクセス許可を持っている必要があります。 何も指定されない場合、スクリプトは現在のユーザー コンテキストにアクセス権があると見なします。 | 値は必要ありません。 |
   |  |  |

    使用する SQL Server がわからない場合は、それを検出できます。 テナント API コンピューターに接続し、Unprotect-MgmtSvc コマンドを使用してテナント API Web.config ファイルの保護を解除し、接続文字列内のサーバー名を検索します。 Protect-MgmtSvc を再度実行してテナント API Web.config ファイルを保護することを忘れないでください。

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>例
次の例は、単一ノード Azure Stack 構成上の完全な Windows Azure Pack コネクタ デプロイと、2 つの Windows Azure Pack Express インストールを示しています。 (各 Express インストールは *wapcomputer1* と *wapcomputer2* という例の名前を持ち、1 台のコンピューター上にあります。)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
[Microsoft ダウンロード センター](https://aka.ms/wapconnectorazurestackdlc)から .exe ファイルをダウンロードして抽出し、WAPConnector フォルダーを Windows Azure Pack コンピューター上の **c:\temp** フォルダーにコピーします。 以前のスクリプトで出力として生成された (\\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput にある) ファイルを **c:\temp\WAPConnector** フォルダーにコピーします。 (これらは、AzurePack-06-27-15-50.txt のようなファイルです。)次に、次のスクリプトを Windows Azure Pack のインスタンスごとに 1 回実行します。

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント
1.  Azure Stack と Windows Azure Pack の間にネットワーク接続が存在することを確認してください。 この接続は、Azure Stack ポータルにアクセスするすべてのテナント コンピューターと、新しいコネクタ サービスが実行されている Windows Azure Pack テナント ポータル仮想マシンの間に存在する必要があります。
2.  指定されたすべての FQDN が正しいことを確認してください。
3.  新しいコネクタ サービスで使用される SSL 証明書が Azure Stack (具体的には AzS-WASP01 VM) や、テナントが Azure Stack ユーザー ポータルにアクセスするために使用する可能性のあるその他のすべてのコンピューターによって信頼されていることを確認してください。
4. 既知の問題については、「[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)」を参照してください。


## <a name="next-steps"></a>次のステップ
[Azure Stack での管理者およびユーザー ポータルの使用](azure-stack-manage-portals.md)
