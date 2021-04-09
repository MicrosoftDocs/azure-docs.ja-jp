---
title: Windows での Azure Files に関する問題のトラブルシューティング
description: Windows での Azure Files に関する問題のトラブルシューティング。 Windows クライアントから接続する場合の Azure Files に関連する一般的な問題と、考えられる解決方法を参照してください。 SMB 共有のみ
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878512"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Windows での Azure Files に関する問題のトラブルシューティング (SMB)

この記事では、Windows クライアントから接続するときに生じる、Microsoft Azure Files に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。 この記事のトラブルシューティングの手順のほかに、[AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) を使って Windows クライアント環境が前提条件を適切に満たしているかどうかを確認することもできます。 AzFileDiagnostics は、この記事で説明しているほとんどの症状を自動的に検出し、最適なパフォーマンスが得られる環境のセットアップを支援します。

> [!IMPORTANT]
> この記事の内容は SMB 共有にのみ適用されます。 NFS 共有の詳細については、「[Azure NFS ファイル共有に関するトラブルシューティング](storage-troubleshooting-files-nfs.md)」を参照してください。

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Azure ファイル共有をマウントするときに、エラー 5 が発生する

ファイル共有をマウントしようとすると、以下のエラーが発生する場合があります。

- システム エラー 5 が発生しました。 アクセスが拒否されました。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1:通信チャネルが暗号化されていない

通信チャネルが暗号化されていない場合や、接続の試行が Azure ファイル共有と同じデータセンターから行われていない場合、セキュリティ上の理由により、Azure ファイル共有への接続がブロックされます。 ストレージ アカウントで [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定が有効になっている場合は、同じデータセンター内の暗号化されていない接続もブロックされる可能性があります。 ユーザーのクライアント OS が SMB 暗号化をサポートしている場合に限り、暗号化された通信チャネルを利用できます。

Windows 8 以降および Windows Server 2012 以降の OS であれば、暗号化をサポートしている SMB 3.0 を含む要求をネゴシエートできます。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

1. SMB 暗号化 (Windows 8、Windows Server 2012、またはそれ以降) をサポートするクライアントから接続するか、Azure ファイル共有に使用されている Azure ストレージ アカウントと同じデータ センターにある仮想マシンから接続します。
2. クライアントが SMB 暗号化をサポートしていない場合、ストレージ アカウントで [[安全な転送が必須]](../common/storage-require-secure-transfer.md) 設定が無効になっていることを確認します。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている 

仮想ネットワーク (VNET) またはファイアウォール ルールがストレージ アカウントに構成されている場合、クライアント IP アドレスまたは仮想ネットワークがアクセスを許可されていない限り、ネットワーク トラフィックは拒否されます。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](../common/storage-network-security.md)を参照してください。

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>原因 3:ID ベースの認証を利用するとき、共有レベルのアクセス許可が正しくない

ユーザーが Active Directory (AD) または Azure Active Directory Domain Services (Azure AD DS) 認証を利用して Azure ファイル共有にアクセスしているとき、共有レベルのアクセス許可が正しくないと、ファイル共有にアクセスできず、"アクセスが拒否されました" エラーが表示されます。 

### <a name="solution-for-cause-3"></a>原因 3 の解決策

アクセス許可が正しく構成されていることを確認します。

- **Active Directory (AD)** : 「[ID に共有レベルのアクセス許可を割り当てる](./storage-files-identity-ad-ds-assign-permissions.md)」を参照してください。

    共有レベルのアクセス許可の割り当ては、Azure AD Connect を使用して Active Directory (AD) から Azure Active Directory (Azure AD) に同期されたグループおよびユーザーに対してサポートされています。  共有レベルのアクセス許可が割り当てられているグループとユーザーが、サポートされていない "クラウド専用" グループではないことを確認します。
- **Azure Active Directory Domain Services (Azure AD DS)** : 「[ID にアクセス許可を割り当てる](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)」を参照してください。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Azure ファイル共有をマウントまたはマウント解除するときに、エラー 53、エラー 67、またはエラー 87 が発生する

オンプレミスから、または別のデータセンターからファイル共有をマウントしようとすると、以下のエラーが発生する場合があります。

- システム エラー 53 が発生しました。 ネットワーク パスが見つかりませんでした。
- システム エラー 67 が発生しました。 ネットワーク名が見つかりません。
- システム エラー 87 が発生しました。 パラメーターが正しくありません。

### <a name="cause-1-port-445-is-blocked"></a>原因 1:ポート 445 がブロックされている

ポート 445 から Azure Files データセンターへの送信方向の通信がブロックされている場合、システム エラー 53 またはシステム エラー 67 が発生することがあります。 ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認するには、[TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) を参照してください。

ファイアウォールまたは ISP がポート 445 をブロックしているかどうかを確認するには、[AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) ツールまたは `Test-NetConnection` コマンドレットを使用してください。 

`Test-NetConnection` コマンドレットを使用するには、Azure PowerShell モジュールがインストール済みである必要があります。詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 `<your-storage-account-name>` と `<your-resource-group-name>` は、実際のストレージ アカウントの該当する名前に置き換えてください。

   
```azurepowershell
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
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> 前出のコマンドからは、ストレージ アカウントの最新の IP アドレスが返されます。 この IP アドレスは同じ状態で維持される保証がなく、常に変化する可能性があります。 スクリプトやファイアウォールの構成にこの IP アドレスをハードコーディングすることは避けてください。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

#### <a name="solution-1---use-azure-file-sync"></a>ソリューション 1 - Azure File Sync を使用する
Azure File Sync により、オンプレミスの Windows Server を Azure ファイル共有の高速キャッシュに変えることができます。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 Azure File Sync は、ポート 443 上で動作するため、ポート 445 がブロックされているクライアントから Azure Files にアクセスするための回避策として使用できます。 [Azure File Sync を設定する方法を確認してください](./storage-sync-files-extend-servers.md)。

#### <a name="solution-2---use-vpn"></a>ソリューション 2 - VPN を使用する
特定のストレージ アカウントへの VPN を設定すると、トラフィックは、インターネット経由ではなく安全なトンネル経由で送信されます。 Windows から Azure Files にアクセスするための [VPN の設定手順](storage-files-configure-p2s-vpn-windows.md)に従ってください。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>ソリューション 3 - ISP/IT 管理者の支援を受けてポート 445 のブロックを解除する
IT 部門または ISP と連携して、ポート 445 の送信方向の通信を [Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に解放します。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>ソリューション 4 - REST API ベースのツール (Storage Explorer や  Powershell など) を使用する
Azure Files は、SMB だけでなく、REST もサポートしています。 REST アクセスは、ポート 443 (標準の tcp) 上で動作します。 REST API を使用して作成された、豊富な UI エクスペリエンスを可能にするさまざまなツールがあります。 [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) もその 1 つです。 [Storage Explorer をダウンロードしてインストールし](https://azure.microsoft.com/features/storage-explorer/)、Azure Files でサポートされるファイル共有に接続します。 同じく REST API を使用する [PowerShell](./storage-how-to-use-files-powershell.md) を使用することもできます。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因 2:NTLMv1 が有効になっている

クライアント側で NTLMv1 通信が有効になっていると、システム エラー 53 またはシステム エラー 87 が発生することがあります。 Azure Files では、NTLMv2 認証のみがサポートされています。 NTLMv1 が有効になっていると、クライアントの安全性が低下します。 そのため、Azure Files に対する通信がブロックされます。 

これがエラーの原因であるかどうかを確認するには、次のレジストリ サブキが 3 未満の値に設定されていないことを確認します。

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

詳細については、TechNet のトピック「[LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10))」を参照してください。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

次のレジストリ サブキーで、**LmCompatibilityLevel** の値を既定値である 3 に戻します。

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>エラー 1816 - このコマンドを実行するのに十分なクォータがありません

### <a name="cause"></a>原因

エラー 1816 は、Azure ファイル共有上のファイルまたはディレクトリに対して許可されている、同時に開くことのできるハンドルの上限に達したときに発生します。 詳細については、「[Azure Files のスケール ターゲット](./storage-files-scale-targets.md#azure-files-scale-targets)」をご覧ください。

### <a name="solution"></a>解決策

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らしてから、再試行します。 詳細については、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを表示するには、[Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用します。  

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを閉じるには、[Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用します。

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Azure ファイル共有にアクセスするか、Azure ファイル共有を削除しようとしたときのエラー "アクセスなし"  
ポータルで Azure ファイル共有にアクセスするか、Azure ファイル共有を削除しようとした時、以下のエラーが発生する場合があります。

アクセス権なし  
エラー コード:403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 1:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている

### <a name="solution-for-cause-1"></a>原因 1 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](../common/storage-network-security.md)を参照してください。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 2:現在のユーザー アカウントには、ストレージ アカウントへのアクセス権がありません

### <a name="solution-for-cause-2"></a>原因 2 の解決策

Azure ファイル共有が置かれたストレージ アカウントを参照して、 **[アクセス制御 (IAM)]** をクリックし、ユーザー アカウントにストレージ アカウントへのアクセス権があることを確認します。 詳しくは、[ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントをセキュリティで保護する方法](../blobs/security-recommendations.md#data-protection)に関するページをご覧ください。

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>ファイルまたはディレクトリの変更、移動、名前の変更、または削除を行うことができません
ファイル共有の主な目的の 1 つは、複数のユーザーおよびアプリケーションが、共有内のファイルとディレクトリを同時に操作できることです。 このようなやり取りを支援するために、ファイル共有は、ファイルとディレクトリへのアクセスを複数の方法で提供します。

SMB 経由でマウントされた Azure ファイル共有からファイルを開くと、アプリケーションまたはオペレーティング システムは、ファイルを参照するファイル ハンドルを要求します。 特に、Azure Files が強制するファイルへの排他アクセスのレベルを指定するファイル ハンドルをアプリケーションが要求するときに、アプリケーションでファイル共有モードが指定されます。 

- `None`: 排他アクセスがあります。 
- `Read`: ファイルを開いている間、他のユーザーはそのファイルを読み取ることができます。
- `Write`: ファイルを開いている間、他のユーザーはそのファイルに書き込むことができます。 
- `ReadWrite`: `Read` と `Write` の両方の共有モードの組み合わせです。
- `Delete`: ファイルを開いている間、他のユーザーはそのファイルを削除できます。 

FileREST プロトコルはステートレス プロトコルであるため、ファイル ハンドルの概念はありませんが、類似のメカニズムとして、スクリプト、アプリケーション、またはサービスが使用できるファイルやフォルダーへのアクセスを仲介するためのファイル リースが用意されています。 ファイルがリースされると、ファイル共有モードが `None` のファイル ハンドルと同等として扱われます。 

ファイル ハンドルとリースは重要な目的がありますが、ファイル ハンドルやリースが孤立する場合もあります。 この場合、ファイルの変更または削除に関する問題が発生する可能性があります。 次のようなエラー メッセージが表示される場合があります。

- ファイルは別のプロセスで使用されているため、このプロセスからアクセスすることはできません。
- ファイルを別のプログラムで開いているため、アクションを完了できません。
- ドキュメントは、別のユーザーが編集するためにロックされています。
- 指定されたリソースは SMB クライアントが削除対象に設定しています。

この問題の解決策は、問題が孤立したファイル ハンドルまたはリースによって発生しているかどうかによって異なります。 

### <a name="cause-1"></a>原因 1
ファイル ハンドルが原因で、ファイルまたはディレクトリの変更または削除が妨げられています。 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用して、開いているハンドルを表示できます。 

すべての SMB クライアントがファイルまたはディレクトリの開いているハンドルを閉じていても、問題が引き続き発生する場合は、ファイル ハンドルを強制的に閉じることができます。

### <a name="solution-1"></a>解決策 1
ファイル ハンドルを強制的に閉じるには、[Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用します。 

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

### <a name="cause-2"></a>原因 2
ファイルのリースのために、ファイルを変更または削除できません。 ファイルにファイル リースがあるかどうかを次の PowerShell で確認できます。これには、`<resource-group>`、`<storage-account>`、`<file-share>`、`<path-to-file>` を環境に適した値に置き換えます。

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

ファイルにリースがある場合、返されるオブジェクトに次のプロパティが含まれているはずです。

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>解決策 2
ファイルからリースを削除するには、リースをリリースするか、リースを解除します。 リースをリリースするには、リースの作成時に設定した、リースの LeaseId が必要です。 リースを解除するには、LeaseId は必要ありません。

次の例は、原因 2 で示されているファイルのリースを解除する方法を示しています (この例では、原因 2 の PowerShell 変数が引き続き使用されています)。

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows で Azure Files との間でのファイルのコピーが遅い

Azure のファイル サービスにファイルを転送しようとした場合に、パフォーマンスが低下することがあります。

- 特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MiB を使用することをお勧めします。
-   書き込みによって大きくなるファイルの最終サイズがわかっており、まだ書き込まれていないファイル末尾にゼロが含まれていてもソフトウェアに互換性の問題がない場合は、書き込みごとにサイズを増やすのではなく、事前にファイル サイズを設定します。
-   次のように適切なコピー方法を使用します。
    -   2 つのファイル共有間の転送には、[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用します。
    -   オンプレミス コンピューター上のファイル共有間では、[Robocopy](./storage-how-to-create-file-share.md) を使用します。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 または Windows Server 2012 R2 に関する考慮事項

Windows 8.1 または Windows Server 2012 R2 を実行しているクライアントに、[KB3114025](https://support.microsoft.com/help/3114025) 修正プログラムがインストールされていることを確認します。 この修正プログラムによって、ハンドルを作成する処理と閉じる処理のパフォーマンスが向上します。

次のスクリプトを実行すると、この修正プログラムがインストールされているかどうかを確認できます。

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

修正プログラムがインストールされている場合、次のような出力が表示されます。

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Azure Marketplace の Windows Server 2012 R2 イメージには、2015 12 月以降、既定で修正プログラム KB3114025 がインストールされています。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>"マイ コンピューター" または "この PC" にドライブ文字を持つフォルダーがない

管理者として net use を使用して Azure ファイル共有をマップすると、共有がないかのような状態になります。

### <a name="cause"></a>原因

既定では、Windows エクスプローラーは管理者として実行されません。 管理コマンド プロンプトから net use を実行すると、ネットワーク ドライブを管理者としてマップすることになります。 マップされたドライブはユーザー主体であるため、異なるユーザー アカウントでドライブがマップされている場合、ログインしているユーザー アカウントにドライブが表示されません。

### <a name="solution"></a>解決策
管理者以外のコマンド ラインから共有をマウントします。 そのほか、[この TechNet の記事](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10))に従って **EnableLinkedConnections** レジストリ値を構成する方法もあります。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>ストレージ アカウントにスラッシュが含まれている場合に、net use コマンドが失敗する

### <a name="cause"></a>原因

net use コマンドは、スラッシュ (/) をコマンド ライン オプションとして解釈します。 ユーザー アカウント名がスラッシュで始まっていると、ドライブのマッピングが失敗します。

### <a name="solution"></a>解決策

この問題を回避する方法としては、次の手順のどちらかを使用できます。

- 次の PowerShell コマンドを実行します。

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  バッチ ファイルから、コマンドを次のように実行することもできます。

  `Echo new-smbMapping ... | powershell -command –`

- 二重引用符でキーを囲みます。スラッシュが最初の文字である場合を除き、この問題を回避できます。 スラッシュが最初の文字である場合は、対話モードを使用してパスワードを別途入力するか、キーを再生成してスラッシュで始まらないキーを取得します。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>アプリケーションまたはサービスが、マウントされた Azure Files ドライブにアクセスできない

### <a name="cause"></a>原因

ドライブは、ユーザーごとにマウントされます。 アプリケーションまたはサービスが、ドライブをマウントしたユーザー アカウントとは別のユーザー アカウントで実行されている場合、アプリケーションがドライブを認識しません。

### <a name="solution"></a>解決策

次のいずれかのソリューションを使用します。

-   アプリケーションが属しているのと同じユーザー アカウントからドライブをマウントします。 PsExec などのツールを使用することができます。
- net use コマンドのユーザー名とパスワードのパラメーターで、ストレージ アカウント名とキーを渡します。
- cmdkey コマンドを使って、資格情報を資格情報マネージャーに追加します。 対話型ログインまたは `runas` を使用することで、サービス アカウントのコンテキストで、コマンド ラインからこれを実行します。
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- マップ済みドライブ文字を使わずに、共有を直接マップします。 一部のアプリケーションはドライブ文字に適切に再接続しない場合があるため、完全な UNC パスを使うとより確実である可能性があります。 

  `net use * \\storage-account-name.file.core.windows.net\share`

これらの手順を実行した後、システム/ネットワーク サービス アカウントに対して net use を実行すると、"システム エラー 1312 が発生しました。 指定されたログオン セッションは存在しません。 そのセッションは既に終了している可能性があります" というエラー メッセージが表示されることがあります。 このエラーが発生した場合は、net use に渡されるユーザー名にドメイン情報 ("[ストレージ アカウント名].file.core.windows.net" など) が含まれていることを確認してください。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>エラー "暗号化をサポートしていない宛先に、ファイルをコピーします"

ファイルがネットワーク経由でコピーされる場合、ファイルはコピー元コンピューターで暗号化が解除された後、プレーンテキストとして送信され、コピー先で再暗号化されます。 ただし、暗号化されたファイルをコピーしようとしている場合は、"暗号化をサポートしていない宛先に、ファイルをコピーします" というエラーが表示されることがあります。

### <a name="cause"></a>原因
この問題は、暗号化ファイル システム (EFS) を使用している場合に発生することがあります。 BitLocker で暗号化されたファイルは Azure Files にコピーできます。 ただし、Azure Files は、NTFS EFS をサポートしていません。

### <a name="workaround"></a>回避策
ネットワーク経由でファイルをコピーするには、まず、ファイルの暗号化を解除します。 以下のいずれかの方法を使用します。

- **copy /d** コマンドを使用します。 この方法では、暗号化されたファイルを、暗号化を解除したファイルとしてコピー先に保存することができます。
- 次のレジストリ キーを設定します。
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

レジストリ キーの設定は、ネットワーク共有に対するすべてのコピー操作に影響しますのでご注意ください。

## <a name="slow-enumeration-of-files-and-folders"></a>ファイルとフォルダーの列挙が遅い

### <a name="cause"></a>原因

この問題は、クライアント コンピューターに大規模なディレクトリに対応するための十分なキャッシュがない場合に発生することがあります。

### <a name="solution"></a>解決策

この問題を解決するには、**DirectoryCacheEntrySizeMax** レジストリ値を、クライアント コンピューターでより大規模なディレクトリ一覧のキャッシュが可能になるように調整します。

- 場所:HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 値の名前:DirectoryCacheEntrySizeMax 
- 値の型: DWORD
 
 
たとえば、これを 0x100000 に設定して、パフォーマンスが向上するかどうかを確認することができます。

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Azure Files に対して Azure Active Directory Domain Service (Azure AD DS) 認証を有効にするときに AadDsTenantNotFound エラーが発生し、"Unable to locate active tenants with tenant Id aad-tenant-id" (テナント ID aad-tenant-id のアクティブなテナントを見つけることができません) というメッセージが表示される

### <a name="cause"></a>原因

関連するサブスクリプションの Azure AD テナント上で [Azure AD Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md) が作成されていない場合、ストレージ アカウント上で [Azure Files に対して Azure Active Directory Domain Service (Azure AD DS) 認証を有効](storage-files-identity-auth-active-directory-domain-service-enable.md)にしようとすると、AadDsTenantNotFound エラーが発生します。  

### <a name="solution"></a>解決策

ストレージ アカウントがデプロイされているサブスクリプションの Azure AD テナント上で Azure AD DS を有効にします。 マネージド ドメインを作成するには、Azure AD テナントの管理者特権が必要です。 Azure AD テナントの管理者でない場合は、管理者に連絡し、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成する](../../active-directory-domain-services/tutorial-create-instance.md)ためのステップバイステップ ガイダンスに従ってください。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>AD 資格情報を使用して Azure Files をマウントできない 

### <a name="self-diagnostics-steps"></a>自己診断の手順
最初に、[Azure Files AD 認証を有効にする](./storage-files-identity-auth-active-directory-enable.md)ための 4 つのステップをすべて実行していることを確認します。

次に、[ストレージ アカウント キーを使用して Azure ファイル共有をマウント](./storage-how-to-use-files-windows.md)してみます。 マウントできない場合は、[AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) をダウンロードして、クライアントの実行環境を検証し、自己修正に関する規範的なガイダンスが提供される、Azure Files のアクセス エラーの原因となる互換性のないクライアント構成を検出し、診断トレースを収集します。

その後、Debug-AzStorageAccountAuth コマンドレットを実行し、ログオンした AD ユーザーで AD の構成に対する一連の基本的なチェックを実行します。 このコマンドレットは、[AzFilesHybrid バージョン 0.1.2 以降](https://github.com/Azure-Samples/azure-files-samples/releases)でサポートされています。 このコマンドレットは、対象のストレージ アカウントに対する所有者アクセス許可を持っている AD ユーザーで実行する必要があります。  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
コマンドレットでは、以下のチェックが順番に実行されて、障害に関するガイダンスが提供されます。
1. CheckADObjectPasswordIsCorrect: ストレージ アカウントを表す AD ID に対して構成されているパスワードが、ストレージ アカウントの kerb1 キー、または kerb2 キーのパスワードと確実に一致しているようにします。 パスワードが正しくない場合、[Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) を実行してパスワードをリセットできます。 
2. CheckADObject: ストレージ アカウントを表すオブジェクトが Active Directory にあり、正しい SPN (サービス プリンシパル名) を持っていることを確認します。 SPN が正しくセットアップされていない場合、デバッグ コマンドレットで返される Set-AD コマンドレットを実行して SPN を構成してください。
3. CheckDomainJoined: クライアント コンピューターが AD にドメイン参加していることを検証します。 クライアント コンピューターが AD にドメイン参加していない場合、ドメイン参加方法についてはこちらの[記事](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)を参照してください。
4. CheckPort445Connectivity: SMB 接続に対してポート 445 が開いていることを確認します。 必要なポートが開いていない場合、Azure Files の接続問題について、トラブルシューティング ツール [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) を参照してください。
5. CheckSidHasAadUser: ログオンしている AD ユーザーが Azure AD と同期されていることを確認します。 特定の AD ユーザーが Azure AD に同期されているかどうかを調べる場合は、入力パラメーターに -UserName と -Domain を指定します。 
6. CheckGetKerberosTicket: ストレージ アカウントに接続するための Kerberos チケットの取得を試みます。 有効な Kerberos トークンがない場合、klist get cifs/storage-account-name.file.core.windows.net コマンドレットを実行し、エラー コードを調べ、チケット取得失敗の根本原因を探ります。
7. CheckStorageAccountDomainJoined: AD 認証が有効になっており、アカウントの AD プロパティが設定されているかどうかを確認します。 有効になっていない場合、[こちら](./storage-files-identity-ad-ds-enable.md)の指示を参照し、Azure Files で AD DS 認証を有効にします。 
8. CheckUserRbacAssignment:Azure Files にアクセスするための共有レベルのアクセス許可を付与するために、AD ユーザーに適切な RBAC ロールの割り当てがあるかどうかを確認します。 ない場合は、[こちら](./storage-files-identity-ad-ds-assign-permissions.md)の指示を参照して、共有レベルのアクセス許可を構成してください。 (AzFilesHybrid バージョン 0.2.3 以降でサポートされています)
9. CheckUserFileAccess:AD ユーザーに Azure Files にアクセスするための適切なディレクトリ/ファイル アクセス許可 (Windows ACL) があるかどうかを確認します。 ない場合は、[こちら](./storage-files-identity-ad-ds-configure-permissions.md)の指示を参照して、ディレクトリ/ファイル レベルのアクセス許可を構成してください。 (AzFilesHybrid バージョン 0.2.3 以降でサポートされています)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Windows エクスプローラーでディレクトリまたはファイル レベルのアクセス許可 (Windows ACL) を構成できない

### <a name="symptom"></a>症状

マウントされたファイル共有でエクスプローラーを使用して Windows ACL を構成しようとすると、次に示すどちらかの現象が発生する可能性があります。
- [セキュリティ] タブの [アクセス許可の編集] をクリックした後、アクセス許可ウィザードが読み込まれない。 
- 新しいユーザーまたはグループを選択しようとすると、ドメインの場所に正しい AD DS ドメインが表示されない。 

### <a name="solution"></a>解決策

回避策として、[icacls tool](/windows-server/administration/windows-commands/icacls) を使用してディレクトリまたはファイル レベルのアクセス許可を構成することをお勧めします。 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Join-AzStorageAccountForAuth コマンドレットの実行中にエラーが発生した

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>エラー:"ディレクトリ サービスは、相対 ID を割り当てられませんでした"

このエラーは、RID マスタ FSMO の役割を保持しているドメイン コントローラーが利用できないか、ドメインから削除され、バックアップから復元された場合に発生する可能性があります。  すべてのドメイン コントローラーが実行されていて、使用可能であることを確認します。

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>エラー: "名前が指定されていないため、位置指定パラメーターをバインドできません"

このエラーは、通常、Join-AzStorageAccountforAuth コマンドの構文エラーによってトリガーされます。  コマンドでスペルミスや構文エラーを確認し、最新バージョンの AzFilesHybrid モジュール (https://github.com/Azure-Samples/azure-files-samples/releases) ) がインストールされていることを確認します。  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files オンプレミス AD DS 認証による AES 256 Kerberos 暗号化のサポート

[AzFilesHybrid module v0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases) を使用した Azure Files オンプレミス AD DS 認証に対する AES 256 Kerberos 暗号化のサポートを導入しました。 v0.2.2 より前のモジュール バージョンで AD DS 認証を有効にしている場合は、最新の AzFilesHybrid モジュール (v0.2.2+) をダウンロードし、以下の PowerShell を実行する必要があります。 ストレージ アカウントで AD DS 認証をまだ有効にしていない場合は、この有効化の[ガイダンス](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)に従って有効にすることができます。 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
