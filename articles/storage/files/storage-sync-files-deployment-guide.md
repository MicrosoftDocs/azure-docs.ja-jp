---
title: Azure File Sync のデプロイ | Microsoft Docs
description: Azure portal、PowerShell、または Azure CLI を使用して Azure File Sync をデプロイする方法について最初から最後まで説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225372"
---
# <a name="deploy-azure-file-sync"></a>Azure File Sync のデプロイ
Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事に記載されている手順を完了する前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」と「[Azure File Sync のデプロイの計画](storage-sync-files-planning.md)」を読むことを強くお勧めします。

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure File Sync をデプロイするのと同じリージョンに Azure ファイル共有が存在すること。詳細については、次を参照してください。
    - Azure File Sync の「[利用可能なリージョン](storage-sync-files-planning.md#azure-file-sync-region-availability)」
    - ファイル共有を作成する方法の詳細な手順を示す「[ファイル共有の作成](storage-how-to-create-file-share.md)」
1. Azure File Sync と同期する、サポートされている Windows Server または Windows Server クラスターの少なくとも 1 つのインスタンス。サポートされる Windows Server のバージョンと推奨されるシステム リソースの詳細については、[Windows ファイル サーバーの考慮事項](storage-sync-files-planning.md#windows-file-server-considerations)に関する記事を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Azure File Sync をデプロイするのと同じリージョンに Azure ファイル共有が存在すること。詳細については、次を参照してください。
    - Azure File Sync の「[利用可能なリージョン](storage-sync-files-planning.md#azure-file-sync-region-availability)」
    - ファイル共有を作成する方法の詳細な手順を示す「[ファイル共有の作成](storage-how-to-create-file-share.md)」
1. Azure File Sync と同期する、サポートされている Windows Server または Windows Server クラスターの少なくとも 1 つのインスタンス。サポートされる Windows Server のバージョンと推奨されるシステム リソースの詳細については、[Windows ファイル サーバーの考慮事項](storage-sync-files-planning.md#windows-file-server-considerations)に関する記事を参照してください。

1. Az PowerShell モジュールは、PowerShell 5.1 または PowerShell 6+ のどちらでも使用できます。 Azure File Sync 用の Az PowerShell モジュールは、サポートされているすべてのシステム (Windows 以外のシステムを含む) で使用できますが、サーバー登録コマンドレットは常に、登録している Windows Server インスタンスで実行する必要があります (これは直接、または PowerShell リモート処理経由で行うことができます)。 Windows Server 2012 R2 では、 **$PSVersionTable** オブジェクトの **PSVersion** プロパティの値を調べることによって、少なくとも PowerShell 5.1.\* を実行していることを確認できます。

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Windows Server 2012 R2 の最新のインストールのように、**PSVersion** の値が 5.1.\* 未満の場合は、[Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) をダウンロードしてインストールすることで簡単にアップグレードできます。 Windows Server 2012 R2 でダウンロードしてインストールする適切なパッケージは、**Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu** です。 

    PowerShell 6+ は、サポートされているすべてのシステムで使用でき、その [GitHub ページ](https://github.com/PowerShell/PowerShell#get-powershell)経由でダウンロードできます。 

    > [!Important]  
    > PowerShell から直接登録するのではなく、サーバー登録 UI を使用する予定がある場合は、PowerShell 5.1 を使用する必要があります。

1. PowerShell 5.1 を使用することを選択した場合は、少なくとも .NET 4.7.2 がインストールされていることを確認してください。 詳細については、使用しているシステムで「[.NET Framework のバージョンおよび依存関係](/dotnet/framework/migration-guide/versions-and-dependencies)」を参照してください。

    > [!Important]  
    > Windows Server Core で .NET 4.7.2+ をインストールする場合は、`quiet` フラグと `norestart` フラグを指定してインストールする必要があります。そうしないと、インストールが失敗します。 たとえば、.NET 4.8 をインストールする場合、コマンドは次のようになります。
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell モジュール。これは、次の手順に従ってインストールできます。[Azure PowerShell をインストールして構成](/powershell/azure/install-Az-ps)します。
     
    > [!Note]  
    > Az PowerShell モジュールをインストールするときに、Az.StorageSync モジュールが自動的にインストールされるようになりました。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure File Sync をデプロイするのと同じリージョンに Azure ファイル共有が存在すること。詳細については、次を参照してください。
    - Azure File Sync の「[利用可能なリージョン](storage-sync-files-planning.md#azure-file-sync-region-availability)」
    - ファイル共有を作成する方法の詳細な手順を示す「[ファイル共有の作成](storage-how-to-create-file-share.md)」
1. Azure File Sync と同期する、サポートされている Windows Server または Windows Server クラスターの少なくとも 1 つのインスタンス。サポートされる Windows Server のバージョンと推奨されるシステム リソースの詳細については、[Windows ファイル サーバーの考慮事項](storage-sync-files-planning.md#windows-file-server-considerations)に関する記事を参照してください。

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)

   必要に応じて、Azure Cloud Shell を使用してこのチュートリアルの手順を完了することもできます。  Azure Cloud Shell は、ブラウザーを介して使用する対話型のシェル環境です。  次のいずれかの方法で Cloud Shell を起動します。

   - コード ブロックの右上隅にある **[使ってみる]** を選択します。 **[使ってみる]** によって Azure Cloud Shell が開きますが、コードが Cloud Shell に自動的にコピーされることはありません。

   - [https://shell.azure.com](https://shell.azure.com) に移動して Cloud Shell を開きます

   - [Azure portal](https://portal.azure.com) の右上にあるメニュー バーの **[Cloud Shell]** ボタンを選択します

1. サインインします。

   CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。

   ```azurecli
   az login
   ```

    ターミナルに表示される手順に従って、認証プロセスを完了します。

1. Azure CLI 拡張機能 [az filesync](/cli/azure/ext/storagesync/storagesync) をインストールする

   ```azurecli
   az extension add --name storagesync
   ```

   拡張機能のリファレンス **storagesync** をインストールすると、次の警告が表示されます。

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Azure File Sync で使用する Windows Server の準備
フェールオーバー クラスターの各サーバー ノードなど、Azure File Sync で使用する各サーバーで、**Internet Explorer セキュリティ強化の構成** を無効にします。 これは、初回のサーバー登録でのみ必要です。 サーバーの登録後に再び有効にできます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
> [!Note]  
> Windows Server Core で Azure File Sync をデプロイする場合は、この手順を省略できます。

1. サーバー マネージャーを開きます。
2. **[ローカル サーバー]** をクリックします。  
    ![サーバー マネージャー UI の左側にある [ローカル サーバー]](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. **[プロパティ]** サブウィンドウで、 **[IE セキュリティ強化の構成]** リンクを選択します。  
    ![サーバー マネージャー UI の [IE セキュリティ強化の構成] ウィンドウ](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、 **[管理者]** と **[ユーザー]** の両方で **[オフ]** を選択します。  
    ![[オフ] が選択された [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウ](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Internet Explorer セキュリティ強化の構成を無効にするには、管理者特権の PowerShell セッションから次を実行します。

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal または PowerShell の手順に従います。

---

## <a name="deploy-the-storage-sync-service"></a>ストレージ同期サービスのデプロイ 
Azure File Sync のデプロイでは最初に、選択したサブスクリプションのリソース グループに **ストレージ同期サービス** リソースを配置します。 必要に応じて、これらをプロビジョニングすることをお勧めします。 後でサーバーとこのリソースの間に信頼関係を作成し、サーバーは 1 つのストレージ同期サービスにのみ登録できます。 そのため、必要な数のストレージ同期サービスをデプロイし、サーバーのグループを分離することをお勧めします。 異なるストレージ同期サービスを使用するサーバー間では同期できないことに注意してください。

> [!Note]
> ストレージ同期サービスは、デプロイされたサブスクリプションとリソース グループからアクセス許可を継承します。 アクセス権を持つユーザーを慎重に確認することをお勧めします。 書き込みアクセス権を持つエンティティは、このストレージ同期サービスに登録されたサーバーからファイルの新しいセットの同期を開始することができ、それらにアクセスできる Azure Storage にデータが送られます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
ストレージ同期サービスをデプロイするには、[Azure portal](https://portal.azure.com/) に移動し、 *[リソースの作成]* をクリックして、Azure File Sync を検索します。検索結果から **[Azure File Sync]** を選択した後、 **[作成]** を選択して **[ストレージ同期のデプロイ]** タブを開きます。

開いたウィンドウに、次の情報を入力します。

- **Name**:ストレージ同期サービスの (リージョンごとに) 一意の名前。
- **サブスクリプション**:ストレージ同期サービスを作成するサブスクリプション。 組織の構成方針によっては、1 つ以上のサブスクリプションにアクセスできることがあります。 Azure サブスクリプションは、各クラウド サービス (Azure Files など) に対する課金の最も基本的なコンテナーです。
- **[リソース グループ]** :リソース グループは、ストレージ アカウントやストレージ同期サービスなどの Azure リソースの論理グループです。 Azure File Sync 用の新しいリソース グループを作成するか、既存のリソース グループを選択できます (リソース グループをコンテナーとして使用して、組織のリソースを論理的に分離することをお勧めします。たとえば、HR リソースや特定のプロジェクトのリソースをグループ化します)。
- **[場所]** :Azure File Sync をデプロイするリージョン。この一覧ではサポートされているリージョンのみを使用できます。

完了したら、 **[作成]** を選択して、ストレージ同期サービスをデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
`<Az_Region>`、`<RG_Name>`、および `<my_storage_sync_service>` を独自の値に置き換え、次のコマンドを使用してストレージ同期サービスを作成およびデプロイします。

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal または PowerShell の手順に従います。

---

## <a name="install-the-azure-file-sync-agent"></a>Azure File Sync エージェントをインストールする
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
このエージェントは、[Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?linkid=858257) からダウンロードできます。 ダウンロードが完了したら、MSI パッケージをダブルクリックして Azure File Sync エージェントのインストールを開始します。

> [!Important]  
> フェールオーバー クラスターで Azure File Sync を使用する場合は、クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。 Azure File Sync で動作するようにクラスター内の各ノードを登録する必要があります。

次を実行することをお勧めします。
- トラブルシューティングとサーバーのメンテナンスを簡素化するために、既定のインストール パス (C:\Program Files\Azure\StorageSyncAgent) をそのまま使用します。
- Microsoft Update を有効にして、Azure File Sync を最新の状態に保ちます。 機能の更新プログラムと修正プログラムを含め、Azure File Sync エージェントに対するすべての更新が Microsoft Update から実行されます。 最新の更新プログラムを Azure File Sync に適用することをお勧めします。詳細については、[Azure File Sync の更新ポリシー](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)に関する記事をご覧ください。

Azure File Sync エージェントのインストールが完了すると、サーバー登録 UI が自動的に開きます。 登録の前にストレージ同期サービスが必要です。ストレージ同期サービスを作成する方法については、次のセクションをご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
次の PowerShell コードを実行して、お使いの OS に適したバージョンの Azure File Sync エージェントをダウンロードし、システムにインストールします。

> [!Important]  
> フェールオーバー クラスターで Azure File Sync を使用する場合は、クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。 Azure File Sync で動作するようにクラスター内の各ノードを登録する必要があります。

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal または PowerShell の手順に従います。

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server をストレージ同期サービスに登録する
Windows Server をストレージ同期サービスに登録すると、サーバー (またはクラスター) とストレージ同期サービスの間に信頼関係が確立されます。 1 つのサーバーは、1 つのストレージ同期サービスにのみ登録でき、同じストレージ同期サービスに関連付けられている他のサーバーおよび Azure ファイル共有と同期できます。

> [!Note]
> サーバーの登録時には、Azure 資格情報を使ってストレージ同期サービスと Windows Server の間に信頼関係が作成されますが、その後、サーバーは独自の ID を作成して使用します。この ID は、サーバーが登録されていて、現在の Shared Access Signature トークン (ストレージ SAS) が有効な間だけ有効です。 サーバーの登録が解除されるとサーバーに新しい SAS トークンを発行できなくなるので、サーバーは Azure ファイル共有にアクセスできず、すべての同期を停止します。

サーバーを登録する管理者は、特定のストレージ同期サービスに対する **所有者** または **共同作成者** の管理ロールのメンバーである必要があります。 これは、Azure portal においてストレージ同期サービスに対する **[アクセス制御 (IAM)]** で構成できます。

また、サーバーを登録できる管理者と、ストレージ同期サービスで同期を構成することも許可されている管理者を、区別することもできます。 その場合、サーバーの登録のみが許可される管理者の一覧を取得するカスタム ロールを作成し、そのカスタム ロールに次のアクセス許可を付与する必要があります。

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure File Sync エージェントがインストールされると、サーバー登録 UI が自動的に開きます。 開かない場合、これを次に示すファイルの場所から手動で開くことができます: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。 サーバー登録 UI が開いたら、 **[サインイン]** を選択して開始します。

サインインすると、次の情報の入力を求められます。

![サーバー登録 UI のスクリーンショット](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure サブスクリプション**: ストレージ同期サービスを含むサブスクリプション (「[ストレージ同期サービスのデプロイ](#deploy-the-storage-sync-service)」をご覧ください)。 
- **リソース グループ**:ストレージ同期サービスを含むリソース グループ。
- **ストレージ同期サービス**: 登録するストレージ同期サービスの名前。

適切な情報を選択したら、 **[登録]** を選択してサーバー登録を完了します。 登録プロセスの一環として、追加のサインインを求められます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal または PowerShell の手順に従います。

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>同期グループとクラウド エンドポイントを作成する
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期を維持されます。 同期グループには、Azure ファイル共有と 1 つ以上のサーバー エンドポイントを表す、1 つのクラウド エンドポイントが含まれている必要があります。 サーバー エンドポイントは、登録されているサーバー上のパスを表します。 1 つのサーバーが、複数の同期グループにサーバー エンドポイントを持つことができます。 目的の同期トポロジを適切に記述するために必要なだけいくつでも同期グループを作成できます。

クラウド エンドポイントは、Azure ファイル共有へのポインターです。 すべてのサーバー エンドポイントはクラウド エンドポイントと同期するので、クラウド エンドポイントはハブになります。 Azure ファイル共有のストレージ アカウントは、ストレージ同期サービスと同じリージョンに存在する必要があります。 Azure ファイル共有の全体が同期されますが、1 つ例外があり、NTFS ボリューム上の非表示の "システム ボリューム情報" フォルダーに相当する特別なフォルダーがプロビジョニングされます。 このディレクトリの名前は ".SystemShareInformation" です。 このディレクトリには、他のエンドポイントに同期されない重要な同期メタデータが含まれます。 このディレクトリを使用したり削除したりしないでください。

> [!Important]  
> 同期グループ内の任意のクラウド エンドポイントまたはサーバー エンドポイントで変更を行うことにより、ファイルを同期グループ内の他のエンドポイントに同期できます。 クラウド エンドポイント (Azure ファイル共有) を直接変更した場合、その変更は、Azure File Sync の変更検出ジョブによって最初に認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md#afs-change-detection)」を参照してください。

クラウド エンドポイントを作成する管理者は、クラウドエンド ポイントで参照されている Azure ファイル共有が含まれるストレージ アカウントの **所有者** 管理ロールのメンバーである必要があります。 これは、Azure portal においてストレージ アカウントに対する **[アクセス制御 (IAM)]** で構成できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
同期グループを作成するには、[Azure Portal](https://portal.azure.com/) でストレージ同期サービスに移動し、 **[+ 同期グループ]** を選びます。

![Azure Portal で新しい同期グループを作成する](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

表示されるウィンドウで次の情報を入力して、同期グループとクラウド エンドポイントを作成します。

- **同期グループ名**: 作成する同期グループの名前。 この名前は、ストレージ同期サービス内で一意である必要がありますが、理にかなった任意の名前を指定できます。
- **サブスクリプション**:「[ストレージ同期サービスのデプロイ](#deploy-the-storage-sync-service)」でストレージ同期サービスをデプロイしたサブスクリプション。
- **ストレージ アカウント**: **[ストレージ アカウントの選択]** を選んだ場合は、同期する Azure ファイル共有を持っているストレージ アカウントを選択できる別のウィンドウが表示されます。
- **Azure ファイル共有**: 同期する Azure ファイル共有の名前。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
同期グループを作成するには、次の PowerShell を実行します。 `<my-sync-group>` は、同期グループの適切な名前に置き換えます。

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

同期グループが正常に作成された後は、クラウド エンドポイントを作成できます。 `<my-storage-account>` と `<my-file-share>` は適切な値に置き換えてください。

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az storagesync sync-group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) コマンドを使用して、新しい同期グループを作成します。  すべての CLI コマンドでリソース グループを既定に設定するには、[az configure](/cli/azure/reference-index#az-configure) を使用します。

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

[az storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) コマンドを使用して、新しいクラウド エンドポイントを作成します。

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>サーバー エンドポイントを作成する
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーなどです。 サーバー エンドポイントは (マウントされた共有ではなく) 登録されたサーバー上のパスであり、クラウドの階層化を使用するには、パスが非システム ボリューム上にある必要があります。 ネットワーク接続ストレージ (NAS) はサポートされていません。

> [!NOTE]
> ボリューム上でサーバー エンドポイントを確立した後もパスまたはドライブ文字を変更することはサポートされていません。 登録済みサーバーで最終的なパスを使用していることを確認します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
サーバー エンドポイントを追加するには、新しく作成した同期グループに移動し、 **[サーバー エンドポイントの追加]** を選びます。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

**[サーバー エンドポイントの追加]** ウィンドウで、次の情報を入力してサーバー エンドポイントを作成します。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**:同期グループの一部として同期される Windows Server のパス。
- **クラウドの階層化**: クラウドの階層化を有効または無効にするスイッチ。 クラウドの階層化によって、使用頻度やアクセス頻度が低いファイルを Azure Files に階層化できます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、単一のサーバー エンドポイントで [ボリュームの空き領域] をボリュームの 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。
- **初期ダウンロード モード**:これは、エージェント バージョン 11 からの省略可能な選択で、Azure ファイル共有にはファイルがあるが、サーバー上にはない場合に役立ちます。 このような状況は、たとえば、別のブランチ オフィス サーバーを同期グループに追加するためにサーバー エンドポイントを作成する場合や、障害が発生したサーバーをディザスター リカバリーする場合などに起こりうる可能性があります。 クラウドを使った階層化が有効になっている場合、既定では、最初にファイルのコンテンツではなく、名前空間のみが呼び戻されます。 これは、ユーザーのアクセス要求で、サーバーにどのファイルのコンテンツを呼び戻すかを決めるべきだと考えている場合に便利です。 クラウドを使った階層化が無効になっている場合、既定では、名前空間が最初にダウンロードされた後、ローカル容量に到達するまで、最終更新タイムスタンプに基づいてファイルが呼び戻されます。 ただし、初期ダウンロード モードは名前空間のみに変更できます。 3 番目のモードは、このサーバー エンドポイントでクラウドを使った階層化が無効になっている場合にのみ使用できます。 このモードでは、最初に名前空間が呼び戻されることを回避します。 ファイルは、完全にダウンロードされた場合にのみ、ローカル サーバーに表示されます。 このモードは、例えばアプリケーションでは完全なファイルが必要とされ、名前空間に階層化されたファイルがあることが許容されない場合に便利です。

サーバー エンドポイントを追加するには、 **[作成]** を選びます。 Azure ファイル共有と Windows Server でファイルの同期が維持されます。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
サーバー エンドポイントを作成するには、必ず `<your-server-endpoint-path>` と `<your-volume-free-space>` を適切な値に置き換え、オプションの初期ダウンロード ポリシーのオプション設定を確認して、次の PowerShell コマンドを実行します。

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az storagesync sync-group server-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) コマンドを使用して、新しいサーバー エンドポイントを作成します。

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>ファイアウォールと仮想ネットワークの設定を構成する

### <a name="portal"></a>ポータル
ファイアウォールと仮想ネットワークの設定で動作するように Azure File Sync を構成したい場合、次の手順を実行します。

1. Azure portal から、セキュリティを確保するストレージ アカウントに移動します。
1. 左側のメニューにある **[ファイアウォールと仮想ネットワーク]** ボタンを選択します。
1. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します。
1. ご利用のサーバーの IP または仮想ネットワークが、対応するセクションに一覧表示されていることを確認します。
1. **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** チェック ボックスがオンになっていることを確認します。
1. **[Save]\(保存\)** を選択して設定を保存します。

![Azure File Sync が動作するようにファイアウォールと仮想ネットワークの設定を構成する](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Azure File Sync でのオンボード
Azure File Sync での最初のオンボードで完全なファイル忠実性とアクセス制御リスト (ACL) を維持しながらダウンタイムをゼロにするための推奨手順を以下に示します。
 
1. ストレージ同期サービスをデプロイします。
1. 同期グループを作成します。
1. 完全なデータ セットがあるサーバーに Azure File Sync エージェントをインストールします。
1. そのサーバーを登録し、共有にサーバー エンドポイントを作成します。 
1. 同期で、Azure ファイル共有 (クラウド エンドポイント) への完全アップロードを行います。  
1. 初期アップロードが完了したら、残りの各サーバーに Azure File Sync エージェントをインストールします。
1. 残りの各サーバーに新しいファイル共有を作成します。
1. 必要に応じて、クラウドの階層化ポリシーを使用して新しいファイル共有にサーバー エンドポイントを作成します。 (この手順では、初期セットアップに使用できる追加のストレージが必要です。)
1. Azure File Sync エージェントで、実際にデータ転送を行わずに完全な名前空間の迅速な復元を行います。 完全な名前空間の同期後、同期エンジンは、サーバー エンドポイントのクラウドの階層化ポリシーに基づいてローカル ディスク領域を満たします。 
1. 同期の完了を確認した後、必要に応じてトポロジをテストします。 
1. ユーザーとアプリケーションをこの新しい共有にリダイレクトします。
1. 必要に応じて、サーバー上の重複する共有を削除できます。
 
初期オンボード用の余分なストレージがなく、既存の共有に接続する場合は、Azure ファイル共有内のデータを事前シードすることができます。 このアプローチが推奨されるのは、ダウンタイムを受け入れることができ、初期のオンボード プロセス中にサーバー共有上のデータが変更されないことを確実に保証できる場合のみです。 
 
1. オンボード プロセス中にいずれのサーバー上のデータも変更されないようにします。
1. SMB 経由で任意のデータ転送ツールを使用してサーバー データを Azure ファイル共有に事前シードします。 たとえば、Robocopy があります。 また、REST 上で AzCopy を使用することもできます。 ACL タイムスタンプと属性を保持するには、適切なスイッチと共に AzCopy を使用してください。
1. 既存の共有を指している目的のサーバー エンドポイントで Azure File Sync トポロジを作成します。
1. 同期によるすべてのエンドポイントでの調整プロセスを完了します。 
1. 調整が完了したら、変更のために共有を開くことができます。
 
現時点では、事前シード処理のアプローチにはいくつかの制限があります。 
- 同期トポロジが完全に稼働する前にサーバー上のデータを変更すると、サーバー エンドポイントで競合が発生することがあります。  
- クラウド エンドポイントを作成した後、Azure File Sync は、最初の同期を開始する前にクラウド内のファイルを検出するプロセスを実行します。このプロセスの完了にかかる時間は、ネットワークの速度、使用可能な帯域幅、ファイルとフォルダーの数など、さまざまな要因によって異なります。 プレビュー リリースでの大まかな見積もりでは、検出プロセスは約 10 ファイル/秒で実行されます。そのため、データがクラウドに事前シードされる場合は、事前シード処理の実行が高速であっても、システムが完全に稼働するまでの全体的な時間が大幅に長くなることがあります。

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>以前のバージョンおよび VSS (ボリューム シャドウ コピー サービス) を使用するセルフサービス復元

> [!IMPORTANT]
> 次の情報は、ストレージ同期エージェントのバージョン 9 (以降) でのみ使用できます。 9 より前のバージョンでは、StorageSyncSelfService コマンドレットがありません。

以前のバージョンとは、ボリュームのサーバー側 VSS スナップショットを利用してファイルの復元可能なバージョンを SMB クライアントに提供する、Windows の機能です。
これにより、IT 管理者に復元を依頼しなくても、インフォメーション ワーカーが直接利用できる強力なシナリオが実現します。これは一般に、セルフサービス復元と呼ばれます。

VSS スナップショットと以前のバージョンは、Azure File Sync からは独立して機能します。ただし、クラウドを使った階層化を互換モードに設定する必要があります。 多くの Azure File Sync サーバー エンドポイントが、同じボリューム上に存在できます。 クラウドを使った階層化を使用しているか使用する予定のサーバー エンドポイントが 1 つでもあるボリュームでは、次の PowerShell 呼び出しを実行する必要があります。

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

ボリューム全体の VSS スナップショットが作成されます。 既定では、スナップショットを保存する十分なスペースがある場合、特定のボリュームに対して最大で 64 のスナップショットを保持することができます。 VSS はこれを自動的に処理します。 既定のスナップショット スケジュールでは、月曜日から金曜日まで、1 日に 2 つのスナップショットを作成します。 このスケジュールは、Windows のスケジュールされたタスクを使用して構成できます。 上記の PowerShell コマンドレットは、以下の 2 つのことを行います。
1. 指定されたボリューム上の Azure File Sync のクラウドを使った階層化を以前のバージョンと互換性を持つように構成し、ファイルがサーバー上でクラウドに対して階層化されている場合でも、確実に以前のバージョンから復元できるようにします。 
1. 既定の VSS スケジュールを有効にします。 後で変更することもできます。 

> [!Note]  
> 注意すべき重要な点が 2 つあります。
>- -Force パラメーターを使用し、かつ VSS が現在有効になっている場合、現在の VSS スナップショット スケジュールは上書きされ、既定のスケジュールで置き換えられます。 コマンドレットを実行する前に、必ずカスタム構成を保存してください。
> - クラスター ノード上でこのコマンドレットを使用している場合、クラスター内の他のすべてのノードでも実行する必要があります。 

セルフサービス復元の互換性が有効かどうかを確認するには、次のコマンドレットを実行する必要があります。

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

これにより、サーバー上のすべてのボリュームと、そのそれぞれの、クラウドを使った階層化との互換性が維持される日数の一覧が表示されます。 この日数は、ボリュームごとに可能な最大のスナップショット数と、既定のスナップショット スケジュールに基づいて自動的に計算されます。 そのため既定では、インフォメーション ワーカーに提示されている以前のバージョンはすべて、復元のために使用することができます。 既定のスケジュールを、より多くのスナップショットを作成するように変更する場合も同様です。
ただし、ボリュームで利用可能なスナップショットの中で、互換性が維持される日数の値よりも古いものが生じるようにスケジュールを変更した場合、ユーザーはその古いスナップショット (以前のバージョン) を使用して復元を行うことはできなくなります。

> [!Note]
> セルフサービス復元を有効にすると、Azure ストレージの使用量と請求に影響があります。 この影響は、現時点でサーバー上で階層化されているファイルだけに限定されます。 この機能を有効にすると、以前のバージョン (VSS スナップショット) のエントリによって参照できる、クラウドで利用可能なファイル バージョンが必ず存在するようになります。
>
> この機能を無効にすると、互換性が維持される日数が経過するまで、Azure ストレージの使用量は徐々に低下していきます。 これを速める方法はありません。 

ボリュームごとの既定の VSS スナップショット最大数 (64) とそれを作成する既定のスケジュールでは、インフォメーション ワーカーが以前のバージョンから復元できる最大日数は 45 日間になります (ボリューム上でどれほどの VSS スナップショットを保存できるかによって異なります)。

ボリュームごとの VSS スナップショット最大数として 64 が適切ではない場合、[レジストリ キーでその値を変更する](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)ことができます。
新しい制限値を有効にするには、既に有効にされている、以前のバージョンの互換性を有効にするためのコマンドレットを各ボリュームで再実行する必要があります。ボリュームごとの新しい VSS スナップショット最大数を反映するために、-Force フラグを使用します。 これにより、互換性が維持される日数が新しく計算されます。 この変更は、新しく階層化されたファイルでのみ有効になり、既に実行した VSS スケジュールへのカスタマイズはすべて上書きされることにご注意ください。

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>新規および変更されたファイルを Azure ファイル共有から事前に呼び戻す

エージェント バージョン 11 では、サーバー エンドポイントで新しいモードが使用できるようになります。 このモードでは、世界各地に分散している企業向けに、ローカル ユーザーがファイルにアクセスする前に、リモート リージョンのサーバー キャッシュを事前に設定しておくことができます。 このモードがサーバーエンド ポイントで有効になっている場合、Azure ファイル共有で作成または変更されたファイルがこのサーバーによって呼び戻されます。

### <a name="scenario"></a>シナリオ

世界各地に分散しているある企業は、米国およびインドにブランチ オフィスを構えています。 朝 (米国時間)、インフォメーション ワーカーが新しいプロジェクト用の新しいフォルダーと新しいファイルを作成し、それらを使用して一日作業しました。 Azure File Sync によって、フォルダーとファイルが Azure ファイル共有 (クラウド エンドポイント) に同期されます。 インドにいるインフォメーション ワーカーは、インドのタイムゾーンでプロジェクトの作業を続行します。 彼らが朝に作業に取り掛かる際、インドにあるローカルの Azure File Sync 対応サーバーでは、インド チームが効率的にローカル キャッシュで作業できるように、これらの新しいファイルがローカルで利用できるようになっている必要があります。 このモードを有効にすることで、オンデマンドの呼び戻しによる最初のファイル アクセスが遅くなることを回避し、Azure ファイル共有でファイルが変更または作成されるとすぐにサーバーによって事前にファイルが呼び戻されるようになります。

> [!IMPORTANT]
> ここまで詳細に Azure ファイル共有の変更をサーバー上で追跡すると、エグレス トラフィックとAzure からの請求額が増加する可能性があることを認識することが重要です。 サーバーに呼び戻されたファイルが実際にはローカルで必要でない場合、サーバーへの不要な呼び戻しによって、悪影響を及ぼす可能性があります。 このモードは、クラウドの最近の変更をサーバー上のキャッシュに事前に保存しておくことで、そのサーバー上のファイルを使用しているユーザーやアプリケーションに良い影響を与えることがわかっている場合に使用します。

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>サーバー エンドポイントで Azure ファイル共有の変更内容を事前に呼び戻すよう有効にする

# <a name="portal"></a>[ポータル](#tab/proactive-portal)

1. [Azure portal](https://portal.azure.com/) で、ストレージ同期サービスにアクセスし、適切な同期グループを選択して、Azure ファイル共有 (クラウド エンドポイント) で変更を詳細に追跡するサーバー エンドポイントを特定します。
1. [クラウドを使った階層化] セクションで、"Azure ファイル共有のダウンロード" トピックを探します。 現在選択されているモードが表示され、それを変更して Azure ファイル共有の変更をより詳細に追跡し、サーバーに事前に呼び戻すことができます。

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="現在有効になっているサーバー エンドポイントにおける Azure ファイル共有のダウンロード動作と、それを変更するためのメニューを開くボタンを示す画像。":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

PowerShell で [Set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) コマンドレットを使用することで、サーバー エンドポイントのプロパティを変更できます。

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS レプリケーション (DFS-R) のデプロイを Azure File Sync に移行する
DFS-R のデプロイを Azure File Sync に移行するには:

1. 置き換える DFS-R トポロジを表すための同期グループを作成します。
2. 移行する DFS-R トポロジの完全なデータ セットがあるサーバーで開始します。 そのサーバーに Azure File Sync をインストールします。
3. そのサーバーを登録し、移行する最初のサーバーのサーバー エンドポイントを作成します。 クラウドの階層化を有効にしないでください。
4. すべてのデータが Azure ファイル共有 (クラウド エンドポイント) に同期されるようにします。
5. 残りの各 DFS-R サーバーに、Azure File Sync エージェントをインストールします。
6. DFS-R を無効にします。 
7. 各 DFS-R サーバーにサーバー エンドポイントを作成します。 クラウドの階層化を有効にしないでください。
8. 同期の完了を確認した後、必要に応じてトポロジをテストします。
9. DFS-R の使用を終了します。
10. 必要に応じて、任意のサーバー エンドポイントでクラウドの階層化を有効にできるようになります。

詳しくは、[Azure File Sync と分散ファイル システム (DFS) の相互運用](storage-sync-files-planning.md#distributed-file-system-dfs)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
- [Azure File Sync のサーバー エンドポイントの追加/削除](storage-sync-files-server-endpoint.md)
- [Azure File Sync (プレビュー) へのサーバーの登録/登録解除](storage-sync-files-server-registration.md)
- [Azure File Sync の監視](storage-sync-files-monitoring.md)
