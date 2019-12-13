---
title: Windows での Azure Files に関する問題のトラブルシューティング | Microsoft Docs
description: Windows での Azure Files に関する問題のトラブルシューティング
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 86b4b19ca80b7dfb2bd9a1a56069fe3d347377ec
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927850"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Windows での Azure Files に関する問題のトラブルシューティング

この記事では、Windows クライアントから接続するときに生じる、Microsoft Azure Files に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。 この記事のトラブルシューティングの手順のほかに、[AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)  を使って Windows クライアント環境が前提条件を適切に満たしているかどうかを確認することもできます。 AzFileDiagnostics は、この記事で説明しているほとんどの症状を自動的に検出し、最適なパフォーマンスが得られる環境のセットアップを支援します。 この情報は、[Azure ファイル共有のトラブルシューティング ツール](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)で入手することもできます。記載されている手順に従って、Azure ファイル共有の接続、マッピング、マウントに関する問題を解決することができます。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Azure ファイル共有をマウントするときに、エラー 5 が発生する

ファイル共有をマウントしようとすると、以下のエラーが発生する場合があります。

- システム エラー 5 が発生しました。 アクセスが拒否されました。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1:通信チャネルが暗号化されていない

通信チャネルが暗号化されていない場合や、接続の試行が Azure ファイル共有と同じデータセンターから行われていない場合、セキュリティ上の理由により、Azure ファイル共有への接続がブロックされます。 ストレージ アカウントで [[安全な転送が必須]](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) 設定が有効になっている場合は、同じデータセンター内の暗号化されていない接続もブロックされる可能性があります。 ユーザーのクライアント OS が SMB 暗号化をサポートしている場合に限り、暗号化された通信チャネルを利用できます。

Windows 8 以降および Windows Server 2012 以降の OS であれば、暗号化をサポートしている SMB 3.0 を含む要求をネゴシエートできます。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

1. SMB 暗号化 (Windows 8、Windows Server 2012、またはそれ以降) をサポートするクライアントから接続するか、Azure ファイル共有に使用されている Azure ストレージ アカウントと同じデータ センターにある仮想マシンから接続します。
2. クライアントが SMB 暗号化をサポートしていない場合、ストレージ アカウントで [[安全な転送が必須]](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) 設定が無効になっていることを確認します。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている 

仮想ネットワーク (VNET) またはファイアウォール ルールがストレージ アカウントに構成されている場合、クライアント IP アドレスまたは仮想ネットワークがアクセスを許可されていない限り、ネットワーク トラフィックは拒否されます。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](https://docs.microsoft.com/azure/storage/common/storage-network-security)を参照してください。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Azure ファイル共有をマウントまたはマウント解除するときに、エラー 53、エラー 67、またはエラー 87 が発生する

オンプレミスから、または別のデータセンターからファイル共有をマウントしようとすると、以下のエラーが発生する場合があります。

- システム エラー 53 が発生しました。 ネットワーク パスが見つかりませんでした。
- システム エラー 67 が発生しました。 ネットワーク名が見つかりません。
- システム エラー 87 が発生しました。 パラメーターが正しくありません。

### <a name="cause-1-port-445-is-blocked"></a>原因 1:ポート 445 がブロックされている

ポート 445 から Azure Files データセンターへの送信方向の通信がブロックされている場合、システム エラー 53 またはシステム エラー 67 が発生することがあります。 ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認するには、[TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) を参照してください。

ファイアウォールまたは ISP がポート 445 をブロックしているかどうかを確認するには、[AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ツールまたは `Test-NetConnection` コマンドレットを使用してください。 

`Test-NetConnection` コマンドレットを使用するには、Azure PowerShell モジュールがインストール済みである必要があります。詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 `<your-storage-account-name>` と `<your-resource-group-name>` は、実際のストレージ アカウントの該当する名前に置き換えてください。

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
接続に成功した場合、次の出力結果が表示されます。
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> 前出のコマンドからは、ストレージ アカウントの最新の IP アドレスが返されます。 この IP アドレスは同じ状態で維持される保証がなく、常に変化する可能性があります。 スクリプトやファイアウォールの構成にこの IP アドレスをハードコーディングすることは避けてください。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

#### <a name="solution-1---use-azure-file-sync"></a>ソリューション 1 - Azure File Sync を使用する
Azure File Sync により、オンプレミスの Windows Server を Azure ファイル共有の高速キャッシュに変えることができます。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 Azure File Sync は、ポート 443 上で動作するため、ポート 445 がブロックされているクライアントから Azure Files にアクセスするための回避策として使用できます。 [Azure File Sync を設定する方法を確認してください](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers)。

#### <a name="solution-2---use-vpn"></a>ソリューション 2 - VPN を使用する
特定のストレージ アカウントへの VPN を設定すると、トラフィックは、インターネット経由ではなく安全なトンネル経由で送信されます。 Windows から Azure Files にアクセスするための [VPN の設定手順](storage-files-configure-p2s-vpn-windows.md)に従ってください。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>ソリューション 3 - ISP/IT 管理者の支援を受けてポート 445 のブロックを解除する
IT 部門または ISP と連携して、ポート 445 の送信方向の通信を [Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に解放します。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>ソリューション 4 - REST API ベースのツール (Storage Explorer や  Powershell など) を使用する
Azure Files は、SMB だけでなく、REST もサポートしています。 REST アクセスは、ポート 443 (標準の tcp) 上で動作します。 REST API を使用して作成された、豊富な UI エクスペリエンスを可能にするさまざまなツールがあります。 [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) もその 1 つです。 [Storage Explorer をダウンロードしてインストールし](https://azure.microsoft.com/features/storage-explorer/)、Azure Files でサポートされるファイル共有に接続します。 同じく REST API を使用する [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) を使用することもできます。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因 2:NTLMv1 が有効になっている

クライアント側で NTLMv1 通信が有効になっていると、システム エラー 53 またはシステム エラー 87 が発生することがあります。 Azure Files では、NTLMv2 認証のみがサポートされています。 NTLMv1 が有効になっていると、クライアントの安全性が低下します。 そのため、Azure Files に対する通信がブロックされます。 

これがエラーの原因であるかどうかを確認するには、次のレジストリ サブキーで値が 3 に設定されていることを確認します。

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

詳細については、TechNet のトピック「[LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx)」を参照してください。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

次のレジストリ サブキーで、**LmCompatibilityLevel** の値を既定値である 3 に戻します。

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Azure ファイル共有にコピーすると、エラー 1816 "このコマンドを実行するための十分なクォータがありません" が発生する

### <a name="cause"></a>原因

エラー 1816 は、ファイル共有がマウントされているコンピューター上のファイルに対して許可されている、同時に開くことのできるハンドルの上限に達したときに発生します。

### <a name="solution"></a>解決策

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らしてから、再試行します。 詳細については、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを表示するには、[Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用します。  

ファイル共有、ディレクトリ、またはファイルの開いているハンドルを閉じるには、[Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用します。

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

<a id="authorizationfailureportal"></a>
## <a name="error-authorization-failure-when-browsing-to-an-azure-file-share-in-the-portal"></a>ポータルで Azure ファイル共有を参照すると、"承認エラー" エラーが発生する

ポータルで Azure ファイル共有を参照すると、以下のエラーが発生する場合があります。

承認エラー  
アクセス権がありません 

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 1:現在のユーザー アカウントには、ストレージ アカウントへのアクセス権がありません

### <a name="solution-for-cause-1"></a>原因 1 の解決策

Azure ファイル共有が置かれたストレージ アカウントを参照して、 **[アクセス制御 (IAM)]** をクリックし、ユーザー アカウントにストレージ アカウントへのアクセス権があることを確認します。 詳しくは、「[ロールベースのアクセス制御 (RBAC) を使用してストレージ アカウントをセキュリティで保護する方法](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac)」をご覧ください。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2:ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが有効になっている

### <a name="solution-for-cause-2"></a>原因 2 の解決策

ストレージ アカウントに対して仮想ネットワークまたはファイアウォール ルールが適切に構成されていることを確認します。 仮想ネットワークまたはファイアウォール ルールが問題の原因となっているかどうかをテストするには、ストレージ アカウントの設定を一時的に **[Allow access from all networks]\(すべてのネットワークからのアクセスを許可する\)** に変更する必要があります。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](https://docs.microsoft.com/azure/storage/common/storage-network-security)を参照してください。

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Azure ファイル共有のファイルまたはディレクトリを削除できない

### <a name="cause"></a>原因
この問題は、通常、ファイルまたはディレクトリのハンドルが開いている場合に発生します。 

### <a name="solution"></a>解決策

開いているすべてのハンドルを SMB クライアントで閉じた後も問題が引き続き発生する場合は、次の手順を行います。

- [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) PowerShell コマンドレットを使用して、開いているハンドルを表示します。

- [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) PowerShell コマンドレットを使用して、開いているハンドルを閉じます。 

> [!Note]  
> Get-AzStorageFileHandle および Close-AzStorageFileHandle コマンドレットは、Az PowerShell モジュールのバージョン 2.4 以降に含まれています。 最新の Az PowerShell モジュールをインストールするには、「[Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)」(Azure PowerShell モジュールのインストール) を参照してください。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows で Azure Files との間でのファイルのコピーが遅い

Azure のファイル サービスにファイルを転送しようとした場合に、パフォーマンスが低下することがあります。

- 特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MiB を使用することをお勧めします。
-   書き込みによって大きくなるファイルの最終サイズがわかっており、まだ書き込まれていないファイル末尾にゼロが含まれていてもソフトウェアに互換性の問題がない場合は、書き込みごとにサイズを増やすのではなく、事前にファイル サイズを設定します。
-   次のように適切なコピー方法を使用します。
    -   2 つのファイル共有間の転送には、[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用します。
    -   オンプレミス コンピューター上のファイル共有間では、[Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) を使用します。

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
管理者以外のコマンド ラインから共有をマウントします。 そのほか、[この TechNet の記事](https://technet.microsoft.com/library/ee844140.aspx)に従って **EnableLinkedConnections** レジストリ値を構成する方法もあります。

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

次のいずれかの解決策を使用します。

-   アプリケーションが属しているのと同じユーザー アカウントからドライブをマウントします。 PsExec などのツールを使用することができます。
- net use コマンドのユーザー名とパスワードのパラメーターで、ストレージ アカウント名とキーを渡します。
- cmdkey コマンドを使って、資格情報を資格情報マネージャーに追加します。 対話型ログインまたは runas を使い、サービス アカウントのコンテキストで、コマンド ラインからこれを実行します。
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- マップ済みドライブ文字を使わずに、共有を直接マップします。 一部のアプリケーションはドライブ文字に適切に再接続しない場合があるため、完全な UNC パスを使うとより確実である可能性があります。 

  `net use * \\storage-account-name.file.core.windows.net\share`

これらの手順を実行した後、システム/ネットワーク サービス アカウントに対して net use を実行すると、"システム エラー 1312 が発生しました。 指定されたログオン セッションは存在しません。 そのセッションは既に終了している可能性があります" というエラー メッセージが表示されることがあります。 このエラーが発生した場合は、net use に渡されるユーザー名にドメイン情報 ("[ストレージ アカウント名].file.core.windows.net" など) が含まれていることを確認してください。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>エラー "暗号化をサポートしていない宛先に、ファイルをコピーします"

ファイルがネットワーク経由でコピーされる場合、ファイルはコピー元コンピューターで暗号化が解除された後、プレーンテキストとして送信され、コピー先で再暗号化されます。 ただし、暗号化されたファイルをコピーしようとしている場合は、"暗号化をサポートしていない宛先に、ファイルをコピーします" というエラーが表示されることがあります。

### <a name="cause"></a>原因
この問題は、暗号化ファイル システム (EFS) を使用している場合に発生することがあります。 BitLocker で暗号化されたファイルは Azure Files にコピーできます。 ただし、Azure Files は、NTFS EFS をサポートしていません。

### <a name="workaround"></a>対処法
ネットワーク経由でファイルをコピーするには、まず、ファイルの暗号化を解除します。 次のいずれかの方法を使用します。

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

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Azure Files に対して Azure Active Directory Domain Service (AAD DS) 認証を有効にするときに AadDsTenantNotFound エラーが発生し、"Unable to locate active tenants with tenant Id aad-tenant-id" (テナント ID aad-tenant-id のアクティブなテナントを見つけることができません) というメッセージが表示される

### <a name="cause"></a>原因

関連するサブスクリプションの ADD テナント上で [AAD ドメイン サービス (AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) が作成されていない場合、ストレージ アカウント上で [Azure Files に対して Azure Active Directory Domain Service (AAD DS) 認証を有効](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)にしようとすると、AadDsTenantNotFound エラーが発生します。  

### <a name="solution"></a>解決策

ストレージ アカウントがデプロイされているサブスクリプションの ADD テナント上で AAD DS を有効にします。 マネージド ドメインを作成するには、AAD テナントの管理者特権が必要です。 Azure AD テナントの管理者でない場合は、管理者に連絡し、[Azure portal を使用した Azure Active Directory Domain Services の有効化](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)に関する記事に書かれている手順を実行します。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>エラー "システム エラー 1359 が発生しました。 内部エラー" が、Azure Active Directory Domain Service (AAD DS) 認証が有効なときにファイル共有への SMB アクセスで発生した

### <a name="cause"></a>原因

エラー "システム エラー 1359 が発生しました。 内部エラー" は、数字で始まるドメイン DNS 名を使用して AAD DS に対して AAD DS 認証を有効にしてファイル共有に接続しようとしたときに発生します。 たとえば、AAD DS のドメイン DNS 名が "1domain" の場合、AAD の資格情報を使用してファイル共有をマウントしようとすると、このエラーが発生します。 

### <a name="solution"></a>解決策

現時点では、次の規則に該当する新しいドメイン DNS 名を使用して、AAD DS を再デプロイすることを検討してください。
- 名前の先頭を数字にすることはできない。
- 名前の長さを 3 から 63 文字にする必要がある。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
