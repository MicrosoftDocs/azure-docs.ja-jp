---
title: "Linux での Azure Files に関する問題のトラブルシューティング | Microsoft Docs"
description: "Linux での Azure Files に関する問題のトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: bef3e7bf8b1fd9199d0c8a083d94660b8eed3365
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---
# <a name="troubleshoot-azure-files-problems-in-linux"></a>Linux での Azure Files に関する問題のトラブルシューティング

この記事では、Linux クライアントから接続するときに生じる、Microsoft Azure Files に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>ファイルを開こうとしたときの "[アクセス許可が拒否されました] ディスク クォータを超えました"

Linux では、次のようなエラー メッセージが表示されます。

**<filename> [アクセス許可が拒否されました] ディスク クォータを超えました**

### <a name="cause"></a>原因

ファイルで許容される、同時に開くことのできるハンドルの上限に達しました。

### <a name="solution"></a>解決策

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らしてから、操作を再試行します。 詳細については、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux で Azure Files との間でのファイルのコピーが遅い

-   特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MB を使用することをお勧めします。
-   書き込みによって大きくなるファイルの最終サイズがわかっており、まだ書き込まれていないファイル末尾にゼロが含まれていてもソフトウェアに互換性の問題が発生しない場合は、書き込みごとにサイズを増やすのではなく、事前にファイル サイズを設定します。
-   次のように適切なコピー方法を使用します。
    -   2 つのファイル共有間の転送には、[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用します。
    -   オンプレミス コンピューター上のファイル共有間では、[Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) を使用します。

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>再接続タイムアウトによる "マウント エラー (112): ホストがダウンしています"

"112" マウント エラーは、Linux クライアント上で、クライアントが長時間アイドル状態になった場合に発生します。 アイドル状態の時間が長いと、クライアントが切断され、接続がタイムアウトになります。  

### <a name="cause"></a>原因

接続は、以下の理由でアイドル状態になることがあります。

-   既定の "ソフト" マウント オプションを使用している場合に、ネットワーク通信エラーによりサーバーへの TCP 接続を再確立できない
-   再接続に関する問題の最新の修正プログラムが存在しない比較的古いカーネルを使用している

### <a name="solution"></a>解決策

Linux カーネルの再接続に関するこの問題は、以下の変更の一環として修正されました。

- [ソケットの再接続後、smb3 セッションの再接続を長時間保留しないようにする修正](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
-   [ソケットの再接続後すぐにエコー サービスを呼び出す](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
-   [CIFS: 再接続中にメモリが破損する可能性がある問題の修正](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
-   [CIFS: 再接続中にミューテックスのダブル ロックが発生する可能性がある問題の修正 (カーネル v4.9 以降)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

ただし、これらの変更が既にすべての Linux ディストリビューションに移植されているとは限りません。 この修正とその他の再接続の修正は、一般的な Linux カーネル (4.4.40、4.8.16、および 4.9.1) で行われています。 この修正プログラムを取得するには、これらの推奨されるカーネルのバージョンのいずれかにアップグレードします。

### <a name="workaround"></a>対処法

ハード マウントを指定することによって、この問題を回避することができます。 これにより、接続が確立されるか、明示的に中断されるまで、クライアントを強制的に待機させ、ネットワーク タイムアウトによるエラーを防ぐことができます。 ただし、この回避策では、待機が無期限に続く可能性があります。 必要に応じて、接続を停止できるように準備しておいてください。

最新バージョンのカーネルにアップグレードできない場合は、Azure ファイル共有にファイルを保持し、30 秒以下の間隔で書き込みを実行することによって、この問題を回避できます。 これは、ファイルの作成日/変更日が書き換えられるような書き込み操作である必要があります。 そうでない場合、キャッシュされた結果が取得され、操作によって再接続がトリガーされない可能性があります。

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>SMB 3.0 を使用して Azure Files をマウントするときの "マウント エラー (115): 操作は現在実行中です"

### <a name="cause"></a>原因

一部のディストリビューションでは、SMB 3.0 での暗号化機能がまだサポートされておらず、ユーザーが SMB 3.0 を使用して Azure Files をマウントしようとしたときに、機能が見つからないため、"115" エラー メッセージが表示される場合があります。

### <a name="solution"></a>解決策

Linux 用 SMB 3.0 の暗号化機能は 4.11 カーネルで導入されました。 この機能によって、オンプレミスから、または異なる Azure リージョンから Azure ファイル共有をマウントできます。 この機能は、公開時に Ubuntu 17.04 と Ubuntu 16.10 に移植されました。 Linux SMB クライアントが暗号化をサポートしていない場合は、File Storage アカウントと同じデータ センターにある Azure Linux VM から SMB 2.1 を使用して Azure Files をマウントします。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM にマウントされている Azure ファイル共有のパフォーマンスが低下している

### <a name="cause"></a>原因

パフォーマンスの低下の原因の 1 つとして考えられるのは、キャッシュの無効化です。

### <a name="solution"></a>解決策

キャッシュが無効になっているかどうかを確認するには、**cache=** エントリを探します。 

**cache=none** であれば、キャッシュが無効になっています。  既定のマウント コマンドを使用して共有を再マウントするか、マウント コマンドに明示的に **cache=strict** オプションを追加して、既定のキャッシュまたは "strict" キャッシュ モードを有効にします。

一部のシナリオでは、**serverino** マウント オプションが原因となり、**ls** コマンドによってすべてのディレクトリ エントリに対して stat が実行されることがあります。 この動作のために、大きなディレクトリを一覧表示するときのパフォーマンスが低下します。 **/etc/fstab** エントリで、マウント オプションを確認できます。

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

**sudo mount | grep cifs** コマンドを実行し、その出力を調べることによって、正しいオプションが使用されているかどうかを確認することもできます。以下に、出力の例を示します。

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

**cache=strict** または **serverino** オプションが存在しない場合は、[ドキュメント](../storage-how-to-use-files-linux.md)のマウント コマンドを実行して、Azure Files をマウント解除してから再マウントします。 その後、**/etc/fstab** エントリに正しいオプションが指定されていることを再確認します。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Windows から Linux にファイルをコピーすると、タイム スタンプが失われる

Linux/Unix プラットフォームでは、ファイル 1 とファイル 2 の所有者が異なる場合、**cp -p** コマンドが失敗します。

### <a name="cause"></a>原因

COPYFILE の強制フラグ **f** を使用すると、Unix 上で **cp -p -f** が実行されます。 このコマンドでも、所有者でないファイルのタイム スタンプの保持が失敗します。

### <a name="workaround"></a>対処法

ファイルをコピーする際に、ストレージ アカウント ユーザーを使用します。

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="cannot-connect-or-mount-an-azure-file-share"></a>Azure ファイル共有を接続またはマウントできない

### <a name="cause"></a>原因

この問題の一般的な原因は次のとおりです。


- 互換性のない Linux ディストリビューション クライアントを使用しています。 次の Linux ディストリビューションを使用して、Azure ファイル共有に接続することをお勧めします。

    - Ubuntu Server 14.04+ 
    - RHEL 7+ 
    - CentOS 7+ 
    - Debian 8 
    - openSUSE 13.2+ 
    - SUSE Linux Enterprise Server 12

- CIFS-utils がクライアントにインストールされていません。
- SMB/CIFS の最小バージョン 2.1 がクライアントにインストールされていません。
- SMB 3.0 暗号化がクライアントでサポートされていません。 SMB 3.0 暗号化は、Ubuntu 16.4 以降のバージョンと SUSE 12.3 以降のバージョンで使用可能です。 他のディストリビューションの場合は、カーネル 4.11 以降のバージョンが必要です。
- サポートされていない TCP ポート 445 経由でストレージ アカウントに接続しようとしています。
- Azure VM から Azure ファイル共有に接続しようとしていますが、VM はストレージ アカウントと同じリージョンにありません。

### <a name="solution"></a>解決策

この問題を解決するには、「[Troubleshooting tool for Azure Files mounting errors on Linux](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-02184089)」 (Lunux での Azure Files のマウント エラー用トラブルシューティング ツール) を使用します。 このツールは、クライアントの実行環境を検証する場合や、Azure Files のアクセス エラーの原因となる互換性のないクライアント構成を検出するのに役立ち、自己修正に関する規範的なガイダンスを提供し、診断トレースを収集します。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

