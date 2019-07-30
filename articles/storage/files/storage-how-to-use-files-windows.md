---
title: Windows で Azure ファイル共有を使用する | Microsoft Docs
description: Windows と Windows Server で Azure ファイル共有を使用する方法について説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 02a8b825a513c75ef7c037348ccaecdf5026ded2
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560478"
---
# <a name="use-an-azure-file-share-with-windows"></a>Windows で Azure ファイル共有を使用する
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows と Windows Server でシームレスに使うことができます。 この記事では、Windows と Windows Server で Azure ファイル共有を使う際の注意点について取り上げます。

Azure ファイル共有がホストされている Azure リージョンの外の Azure ファイル共有 (オンプレミスの共有、他の Azure リージョン内の共有など) を使用するために、OS は SMB 3.0 をサポートする必要があります。 

Azure ファイル共有は、Azure VM とオンプレミスのどちらかで実行されている Windows インストール済み環境で使用できます。 次の表は、ファイル共有へのアクセスがサポートされる環境を OS バージョンごとに示したものです。

| Windows のバージョン        | SMB のバージョン | Azure VM でマウント可能 | オンプレミスでマウント可能 |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | はい | はい |
| Windows 10<sup>1</sup> | SMB 3.0 | はい | はい |
| Windows Server 半期チャネル<sup>2</sup> | SMB 3.0 | はい | はい |
| Windows Server 2016    | SMB 3.0     | はい                   | はい                  |
| Windows 8.1            | SMB 3.0     | はい                   | はい                  |
| Windows Server 2012 R2 | SMB 3.0     | はい                   | はい                  |
| Windows Server 2012    | SMB 3.0     | はい                   | はい                  |
| Windows 7              | SMB 2.1     | はい                   | いいえ                   |
| Windows Server 2008 R2 | SMB 2.1     | はい                   | いいえ                   |

<sup>1</sup>Windows 10 バージョン 1507、1607、1703、1709、1803、1809。  
<sup>2</sup>Windows Server バージョン 1709 および 1803。

> [!Note]  
> 常に、各 Windows バージョンの最新のサポート技術情報を参照することをお勧めします。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件 
* **ストレージ アカウント名**: Azure ファイル共有をマウントするには、ストレージ アカウントの名前が必要です。

* **ストレージ アカウント キー**: Azure ファイル共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要です。 現時点では、SAS キーは、マウントではサポートされていません。

* **ポート 445 が開いていることを確認する**: SMB プロトコルでは、TCP ポート 445 が開いている必要があります。ポート 445 がブロックされている場合は、接続が失敗します。 ポート 445 がファイアウォールでブロックされているかどうかは、`Test-NetConnection` コマンドレットで確認できます。 [ポート 445 のブロックを回避するさまざまな方法についてはこちらで](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)確認できます。

    次の PowerShell コードは、Azure PowerShell モジュールがインストール済みであることを想定しています。詳細については、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)に関するページを参照してください。 `<your-storage-account-name>` と `<your-resource-group-name>` は、実際のストレージ アカウントの該当する名前に置き換えてください。

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    接続に成功した場合、次の出力結果が表示されます。

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > 前出のコマンドからは、ストレージ アカウントの最新の IP アドレスが返されます。 この IP アドレスは同じ状態で維持される保証がなく、常に変化する可能性があります。 スクリプトやファイアウォールの構成にこの IP アドレスをハードコーディングすることは避けてください。 

## <a name="using-an-azure-file-share-with-windows"></a>Windows で Azure ファイル共有を使用する
Windows で Azure ファイル共有を使用するには、Azure ファイル共有にドライブ文字 (マウント ポイントのパス) を割り当ててマウントするか、または対応する [UNC パス](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)経由でアクセスする必要があります。 

Windows Server や Linux Samba サーバー、NAS デバイスをホストとするような従来からある SMB 共有とは異なり、Azure ファイル共有は現在、Active Directory (AD) または Azure Active Directory (AAD) の ID を使った Kerberos 認証をサポートしていません。この認証機能については現在、対応に向けて[作業中](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles)です。 Azure ファイル共有には、それが格納されているストレージ アカウントのストレージ アカウント キーを使ってアクセスする必要があります。 ストレージ アカウント キーは、アクセスするファイル共有内のファイルとフォルダーすべてに対する管理者アクセス許可を含んだストレージ アカウントの管理者キーであると共に、ストレージ アカウントに格納されているすべてのファイル共有および他のストレージ リソース (BLOB、キュー、テーブルなど) の管理者キーでもあります。 それで対応できないワークロードについては、Kerberos 認証と ACL サポートの不足を [Azure File Sync](storage-files-planning.md#data-access-method) で解決できる可能性があります。AAD ベースの Kerberos 認証および ACL サポートが一般提供されるまでの一時的な措置としてご利用ください。

SMB ファイル共有が想定されている基幹業務 (LOB) アプリケーションを Azure にリフトアンドシフトする一般的なパターンは、専用の Windows ファイル サーバーを Azure VM で実行する代わりとして Azure ファイル共有を使うことです。 基幹業務アプリケーションで Azure ファイル共有を使うための移行に関して、その作業を成功させるうえで重要な考慮事項があります。多くの基幹業務アプリケーションは、VM の管理者アカウントではなく、制限されたシステム アクセス許可を与えられた専用のサービス アカウントのコンテキストで実行されるということです。 そのため、Azure ファイル共有の資格情報をマウント/保存する際は、自分の管理者アカウントからではなく、必ずサービス アカウントのコンテキストから行う必要があります。

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Azure ファイル共有の資格情報を Windows で保持する  
ストレージ アカウントの資格情報は、[cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) ユーティリティを使って Windows 内に保持することができます。 つまり Azure ファイル共有に UNC パスでアクセスしたり Azure ファイル共有をマウントしたりする際に、資格情報を指定する必要はありません。 ストレージ アカウントの資格情報を保存するには、次の PowerShell コマンドを実行します。`<your-storage-account-name>` と `<your-resource-group-name>` は、実際の値に置き換えてください。

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

ストレージ アカウントの資格情報が cmdkey ユーティリティによって保存されたことを確認するには、次のように list パラメーターを使用します。

```powershell
cmdkey /list
```

Azure ファイル共有の資格情報が正常に保存された場合、次のような結果が出力されます (実際に格納されているキーはこれよりも多い可能性があります)。

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

以降は、別途資格情報を指定しなくても共有をマウントしたり、共有にアクセスしたりすることができます。

#### <a name="advanced-cmdkey-scenarios"></a>高度な cmdkey のシナリオ
他にも cmdkey には、考慮すべきシナリオが 2 つあります。別のユーザー (サービス アカウントなど) の資格情報をマシンに保存するシナリオと、PowerShell リモート処理でリモート マシンに資格情報を保存するシナリオです。

別のユーザーの資格情報をマシンに保存するのはごく簡単です。アカウントにログインする際、単純に次の PowerShell コマンドを実行します。

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

これで、指定したサービス アカウント (またはユーザー アカウント) のユーザー コンテキストで新しい PowerShell ウィンドウが開きます。 その後は、[前述](#persisting-azure-file-share-credentials-in-windows)の説明のように cmdkey ユーティリティを使うことができます。

ただし、PowerShell リモート処理を使って資格情報をリモート マシンに保存することはできません。PowerShell リモート処理を介してユーザーがログインしているとき、cmdkey では、資格情報ストアへのアクセスが、追加も含め許可されません。 [リモート デスクトップ](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows)でマシンにログインすることをお勧めします。

### <a name="mount-the-azure-file-share-with-powershell"></a>PowerShell を使用した Azure ファイル共有のマウント
Azure ファイル共有をマウントするには、管理者特権ではない通常の PowerShell セッションから次のコマンドを実行します。 `<your-resource-group-name>`、`<your-storage-account-name>`、`<your-file-share-name>`、`<desired-drive-letter>` は、忘れずに適切な情報に置き換えてください。

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> `New-PSDrive` コマンドレットの `-Persist` オプションを使ってできることは、資格情報が保存されている場合に、ファイル共有を起動時に再マウントすることだけです。 資格情報を保存するには、[前述の説明](#persisting-azure-file-share-credentials-in-windows)に従って cmdkey を使います。 

次のPowerShell コマンドレットを使えば、必要に応じて Azure ファイル共有のマウントを解除できます。

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>エクスプローラーを使用した Azure ファイル共有のマウント
> [!Note]  
> 以下の手順は Windows 10 で操作する場合であり、それ以前のリリースでは少し異なることがある点に注意してください。 

1. エクスプローラーを開きます。 [スタート] メニューから開くことも、Windows + E キーを押すショートカットで開くこともできます。

2. ウィンドウの左側の **[PC]** 項目に移動します。 これで、リボンで使用できるメニューが変更されます。 [コンピューター] メニューの **[ネットワーク ドライブの割り当て]** を選択します。
    
    ![[ネットワーク ドライブの割り当て] ドロップダウン メニューのスクリーンショット](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Azure portal の **[接続]** ウィンドウで UNC パスをコピーします。 

    ![Azure Files の [接続] ウィンドウの UNC パス](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. ドライブ文字を選択し、UNC パスを入力します。 
    
    ![[ネットワーク ドライブの割り当て] ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. ユーザー名として先頭に `AZURE\` を付けたストレージ アカウント名を使用し、パスワードとしてストレージ アカウント キーを使用します。
    
    ![ネットワーク資格情報ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Azure ファイル共有を自由に使用します。
    
    ![Azure ファイル共有がマウントされました](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Azure ファイル共有をマウント解除することになったら、エクスプローラーの **[ネットワークの場所]** の下にある共有のエントリを右クリックし、 **[切断]** を選択します。

### <a name="accessing-share-snapshots-from-windows"></a>Windows から共有スナップショットへのアクセス
手動で、またはスクリプトや Azure Backup のようなサービスを通じて自動で共有スナップショットを取得した場合、Windows のファイル共有内にある以前のバージョンの共有、ディレクトリ、または特定のファイルを表示することができます。 共有スナップショットは、[Azure portal](storage-how-to-use-files-portal.md)、[Azure PowerShell](storage-how-to-use-files-powershell.md)、および [Azure CLI](storage-how-to-use-files-cli.md) で取得することができます。

#### <a name="list-previous-versions"></a>以前のバージョンを一覧表示する
復元が必要な項目または親項目を参照します。 ダブルクリックして、目的のディレクトリに移動します。 右クリックして、メニューから **[プロパティ]** を選択します。

![選択したディレクトリの右クリック メニュー](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

**[以前のバージョン]** を選択して、このディレクトリの共有スナップショットの一覧を表示します。 ネットワークの速度とディレクトリ内の共有のスナップショットの数に応じて、一覧の読み込みに数秒かかる場合があります。

![[以前のバージョン] タブ](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

**[開く]** を選択して、特定のスナップショットを開くことができます。 

![開かれたスナップショット](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>以前のバージョンから復元する
共有スナップショット作成時のディレクトリ全体の内容を元の場所に再帰的にコピーするには、 **[復元]** を選択します。
 ![警告メッセージ内の [復元] ボタン](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server のセキュリティ保護
Windows で Azure ファイル共有をマウントするには、ポート 445 がアクセス可能な状態になっている必要があります。 ポート 445 は、SMB 1 に固有のセキュリティ リスクから、多くの組織でブロックされています。 SMB 1 は Windows と Windows Server に備わっているレガシ ファイル システム プロトコルであり、CIFS (Common Internet File System) と呼ばれることもあります。 SMB 1 は、旧式で効率が悪く、また何よりもセキュリティに不安があるプロトコルです。 さいわい、Azure Files は SMB 1 をサポートしておらず、サポートされているいずれのバージョンの Windows および Windows Server も、SMB 1 を削除するか無効にすることができます。 Azure ファイル共有を運用環境で使用する前に、SMB 1 のクライアントおよびサーバーを Windows から削除するか無効にすることを[強くお勧め](https://aka.ms/stopusingsmb1)します。

次の表は、Windows のバージョンごとに、SMB 1 の状態についての詳しい情報をまとめたものです。

| Windows のバージョン                           | SMB 1 の既定の状態 | 無効化/削除の方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Disabled             | Windows の機能を使って削除 |
| Windows Server バージョン 1709 以降            | Disabled             | Windows の機能を使って削除 |
| Windows 10 バージョン 1709 以降                | Disabled             | Windows の機能を使って削除 |
| Windows Server 2016                       | Enabled              | Windows の機能を使って削除 |
| Windows 10 バージョン 1507、1607、1703 | Enabled              | Windows の機能を使って削除 |
| Windows Server 2012 R2                    | Enabled              | Windows の機能を使って削除 | 
| Windows 8.1                               | Enabled              | Windows の機能を使って削除 | 
| Windows Server 2012                       | Enabled              | レジストリで無効化       | 
| Windows Server 2008 R2                    | Enabled              | レジストリで無効化       |
| Windows 7                                 | Enabled              | レジストリで無効化       | 

### <a name="auditing-smb-1-usage"></a>SMB 1 の使用状況の監査
> Windows Server 2019、Windows Server 半期チャネル (バージョン 1709 および 1803)、Windows Server 2016、Windows 10 (バージョン 1507、1607、1703、1709、1803)、Windows Server 2012 R2、Windows 8.1 が対象となります

お使いの環境から SMB 1 を削除する前に、その変更によって支障が生じるクライアントがないかを確認するために、SMB 1 の使用状況を監査したい場合があります。 SMB 共有に対する要求が SMB 1 で行われると、イベント ログの `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` に、監査イベントが記録されます。 

> [!Note]  
> Windows Server 2012 R2 と Windows 8.1 で監査のサポートを有効にするには、少なくとも [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) をインストールする必要があります。

監査を有効にするには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Windows Server から SMB 1 を削除する
> Windows Server 2019、Windows Server 半期チャネル (バージョン 1709 および 1803)、Windows Server 2016、Windows Server 2012 R2 が対象となります

Windows Server インスタンスから SMB 1 を削除するには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

削除処理を完了するために、サーバーを再起動してください。 

> [!Note]  
> Windows 10 および Windows Server バージョン 1709 以降では、SMB 1 が既定ではインストールされておらず、SMB 1 クライアントおよび SMB 1 サーバー用の別個の Windows 機能が存在します。 SMB 1 サーバー (`FS-SMB1-SERVER`) と SMB 1 クライアント (`FS-SMB1-CLIENT`) は、どちらもインストールしないことをお勧めします。

### <a name="removing-smb-1-from-windows-client"></a>Windows クライアントから SMB 1 を削除する
> Windows 10 (バージョン 1507、1607、1703、1709、1803) および Windows 8.1 が対象となります。

Windows クライアントから SMB 1 を削除するには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

削除処理を完了するために、PC を再起動してください。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>レガシ バージョンの Windows/Windows Server で SMB 1 を無効にする
> Windows Server 2012、Windows Server 2008 R2、Windows 7 が対象となります。

レガシ バージョンの Windows/Windows Server では SMB 1 を完全に削除することはできませんが、レジストリで無効にすることができます。 SMB 1 を無効にするには、`DWORD` 型で値 `0` の新しいレジストリ キー `SMB1` を `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` に作成します。

次の PowerShell コマンドレットを使って簡単に実行することもできます。

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

SMB 1 を無効にするには、このレジストリ キーを作成した後、サーバーを再起動する必要があります。

### <a name="smb-resources"></a>SMB のリソース
- [SMB 1 の使用を停止する](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 製品一覧](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [DSCEA で環境内の SMB 1 を検出する](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [グループ ポリシーで SMB 1 を無効にする](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>次の手順
Azure Files の詳細については、次のリンクをご覧ください。
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [FAQ](../storage-files-faq.md)
- [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
