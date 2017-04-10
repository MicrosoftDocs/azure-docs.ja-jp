---
title: "Azure File Storage に関する問題のトラブルシューティング | Microsoft Docs"
description: "Azure File Storage に関する問題のトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7f719fb38709f4bb7083b7f21a5979f7e0588d0f
ms.lasthandoff: 03/22/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Azure File Storage に関する問題のトラブルシューティング
この記事では、Windows および Linux クライアントから接続するときに生じる、Microsoft Azure File Storage に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。

**一般的な問題 (Windows および Linux クライアントの両方で発生)**

* [ファイルを開こうとしたときに、クォータ エラーが発生する](#quotaerror)
* [Windows または Linux から Azure File Storage にアクセスしたときにパフォーマンスが低下する](#slowboth)
* [Azure File Storage で読み取りおよび書き込み操作をトレースする方法](#traceop)

**Windows クライアントの問題**

* [Windows 8.1 または Windows Server 2012 R2 から Azure File Storage にアクセスしたときにパフォーマンスが低下する](#windowsslow)
* [Azure ファイル共有をマウントしようとしたときに、エラー 53 が発生する](#error53)
* [エラー 87: Azure ファイル共有をマウントしようとしたときにパラメーターが正しくない](#error87)
* [net use は成功したが、エクスプローラー UI で、マウントされた Azure ファイル共有またはドライブ文字が表示されない](#netuse)
* [ストレージ アカウントに "/" が含まれており、net use コマンドが失敗する](#slashfails)
* [アプリケーション/サービスがマウントされた Azure Files ドライブにアクセスできない](#accessfiledrive)
* [パフォーマンスを最適化するためのその他の推奨事項](#additional)
* [Azure Files にファイルをアップロードまたはコピーしようとしたときに、"暗号化をサポートしていない宛先に、ファイルをコピーします" というエラーが発生する](#encryption)

**Linux クライアントの問題**

* [断続的な IO エラー - マウント ポイントで list コマンドを実行すると、既存のファイル共有で "ホストがダウンしています (エラー 112)" というエラーが発生するか、シェルが応答を停止する](#errorhold)
* [Linux VM で Azure Files をマウントしようとしたときに、マウント エラー 115 が発生する](#error15)
* [Linux VM にマウントされている Azure ファイル共有のパフォーマンスが低下している](#delayproblem)
* [マウント エラー (11): Ubuntu 4.8+ カーネルにマウントするときにリソースが一時的に使用不能になる](#ubuntumounterror)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>ファイルを開こうとしたときに、クォータ エラーが発生する
Windows では、次のようなエラー メッセージが表示されます。

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

Linux では、次のようなエラー メッセージが表示されます。

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>原因
この問題は、ファイルで許容される、同時に開くことのできるハンドルの上限に達したことが原因で発生します。

### <a name="solution"></a>解決策
ハンドルをいくつか閉じ、同時に開いているハンドルの数を減らしてから、再試行します。 詳細については、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](storage-performance-checklist.md)」を参照してください。

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Windows または Linux から File Storage にアクセスしたときにパフォーマンスが低下する
* 特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MB を使用することをお勧めします。
* 書き込みによって大きくなるファイルの最終サイズがわかっており、まだ書き込まれていないファイル末尾にゼロが含まれていてもソフトウェアに互換性の問題がない場合は、書き込みによってサイズを増やすのではなく、事前にファイル サイズを設定します。
* 次のように適切なコピー方法を使用します。
      * 2 つのファイル共有間のすべての転送には、AZCopy を使用します。 詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy)」を参照してください。
      * ファイル共有とオンプレミスのコンピューター間では、robocopy を使用します。 詳細については「[Multi-threaded robocopy for faster copies (マルチスレッドの robocopy を使用してコピーを高速化する)](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)」を参照してください。
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 または Windows Server 2012 R2 から File Storage にアクセスしたときにパフォーマンスが低下する
Windows 8.1 または Windows Server 2012 R2 を実行しているクライアントに、修正プログラム [KB3114025](https://support.microsoft.com/kb/3114025) がインストールされていることを確認します。 この修正プログラムによって、ハンドルを作成する処理と閉じる処理のパフォーマンスが向上します。

次のスクリプトを実行して、この修正プログラムがインストールされているかどうかを確認できます。

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

修正プログラムがインストールされている場合、次のような出力が表示されます。

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> Azure Marketplace の Windows Server 2012 R2 イメージには、2015 12 月以降、既定で修正プログラム KB3114025 がインストールされています。
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Azure File Storage で読み取りおよび書き込み操作をトレースする方法

[Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226) ではクライアントの要求をクリア テキストで表示でき、ネットワーク要求とトランザクションの間にとてもよい関係があります (SMB が REST ではない場合)。  欠点は、各クライアントでこれを実行する必要があることで、多くの IaaS VM ワーカーがある場合は時間がかかります。

ProcMon で Message Analyze を使った場合、トランザクションの基になっているアプリ コードがよくわかります。

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>パフォーマンスを最適化するためのその他の推奨事項
読み取りアクセスではなく書き込みアクセスを要求するキャッシュ I/O のファイルを作成したり、開いたりしないでください。 つまり、**CreateFile()** を呼び出すときに、**GENERIC_WRITE** だけを指定せずに、必ず **GENERIC_READ | GENERIC_WRITE** を指定してください。 書き込み専用のハンドルは、ファイルのハンドルを開くだけの場合でも、小規模の書き込みをローカルにキャッシュできません。 これにより、小規模な書き込みに対して深刻なパフォーマンス ペナルティが発生します。 CRT **fopen()** に対して "a" モードを指定すると、書き込み専用のハンドルが開くことに注意してください。

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>Azure ファイル共有をマウントしたり、マウントを解除したりしようとしたときに、"エラー 53" または "エラー 67" が発生する
この問題は、次のような状況で発生する場合があります。

### <a name="cause-1"></a>原因 1
"システム エラー 53 が発生しました。 アクセスが拒否されました。" セキュリティ上の理由により、通信チャネルが暗号化されていない場合や、接続の試行が Azure ファイル共有と同じデータ センターから行われていない場合、Azure ファイル共有への接続はブロックされます。 ユーザーのクライアント OS が SMB 暗号化をサポートしていない場合、通信チャネルの暗号化は提供されません。 このことは、ユーザーがオンプレミスまたは異なるデータ センターからファイル共有をマウントしようとしたときに、"システム エラー 53 が発生しました。 アクセスが拒否されました" というエラー メッセージによって示されます。 Windows 8、Windows Server 2012、およびそれ以降のバージョンの SMB 3.0 を含む各ネゴシエート要求は、暗号化をサポートします。

### <a name="solution-for-cause-1"></a>原因 1 の解決策
Windows 8、Windows Server 2012、またはそれ以降というバージョンの要件を満たすクライアントから接続するか、Azure ファイル共有に使用されている Azure ストレージ アカウントと同じデータ センターにある仮想マシンから接続します。

### <a name="cause-2"></a>原因 2
"システム エラー 53" または "システム エラー 67" は、Azure ファイル共有をマウントするときに、ポート 445 から Azure Files データ センターへの送信方向の通信がブロックされている場合に表示されます。 [ここ](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)をクリックして、ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認してください。

Comcast や一部の IT 組織ではこのポートがブロックされます。 これが "システム エラー 53" メッセージの原因であるかどうかを把握するために、Portqry を使用して、TCP:445 エンドポイントを照会できます。 TCP:445 エンドポイントがフィルター処理済みとして表示される場合、TCP ポートがブロックされています。 クエリの使用例を次に示します。

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

TCP 445 がネットワーク パスに沿った規則によってブロックされている場合、次の出力が表示されます。

**TCP port 445 (microsoft-ds service): FILTERED**

Portqry の使用の詳細については、「[Portqry.exe コマンド ライン ユーティリティの説明](https://support.microsoft.com/kb/310099)」を参照してください。

### <a name="solution-for-cause-2"></a>原因 2 の解決策
IT 組織と連携して、ポート 445 の送信方向の通信を [Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に解放します。

<a id="error87"></a>
### <a name="cause-3"></a>原因 3
"システム エラー 53 またはシステム エラー 87" は、NTLMv1 通信がクライアント側で有効になっている場合にも発生することがあります。 NTLMv1 が有効になっていると、クライアントの安全性が低下します。 そのため、Azure Files に対する通信がブロックされます。 これがエラーの原因であるかどうかを確認するには、次のレジストリ サブキーに 3 の値が設定されていることを確認します。

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

詳細については、TechNet のトピック「[LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx)」を参照してください。

### <a name="solution-for-cause-3"></a>原因 3 の解決策
この問題を解決するには、HKLM\SYSTEM\CurrentControlSet\Control\Lsa レジストリ キーの LmCompatibilityLevel 値を既定値の 3 に戻します。

Azure Files では、NTLMv2 認証のみがサポートされています。 グループ ポリシーがクライアントに適用されていることを確認してください。 これにより、このエラーが発生するのを防ぐことができます。 これは、セキュリティのベスト プラクティスでもあります。 詳細については、[グループ ポリシーを使用して NTLMv2 を使用するようにクライアントを構成する方法](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx)に関する記事を参照してください。

推奨されるポリシーの設定は、**[NTLMv2 応答のみ送信する]** です。 これはレジストリ値 3 に対応します。 クライアントは NTLMv2 認証のみを使用し、サーバーでサポートされている場合は NTLMv2 セッション セキュリティを使用します。 ドメイン コントローラーは、LM 認証、NTLM 認証、および NTLMv2 認証を受け付けます。

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>net use は成功したが、エクスプローラーで、マウントされた Azure ファイル共有が表示されない
### <a name="cause"></a>原因
既定では、エクスプローラーは管理者として実行されません。 管理者のコマンド プロンプトから **net use** を実行する場合、ネットワーク ドライブを "管理者として" マップします。 マップされたドライブはユーザー主体であるため、異なるユーザー アカウントでドライブがマップされている場合、ログインしているユーザー アカウントにドライブが表示されません。

### <a name="solution"></a>解決策
管理者以外のコマンド ラインから共有をマウントします。 そのほか、[この TechNet の記事](https://technet.microsoft.com/library/ee844140.aspx)に従って **EnableLinkedConnections** レジストリ値を構成する方法もあります。

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>ストレージ アカウントに "/" が含まれており、net use コマンドが失敗する
### <a name="cause"></a>原因
**net use** コマンドをコマンド プロンプト (cmd.exe) で実行する場合、コマンド ライン オプションとして "/" を追加することで解析が行われます。 これにより、ドライブのマッピングが失敗します。

### <a name="solution"></a>解決策
この問題を回避するために、次の手順のどちらかを使用できます。

•    次の PowerShell コマンドを使用します。

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

バッチ ファイルからは次のように実行できます。

`Echo new-smbMapping ... | powershell -command –`

•    この問題を回避するには、"/" が最初の文字である場合を除いて、二重引用符でキーを囲みます。 "/" が最初の文字である場合は、対話モードを使用してパスワードを別に入力するか、キーを再生成してスラッシュ (/) 文字で始まらないキーを取得します。

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>アプリケーション/サービスがマウントされた Azure Files ドライブにアクセスできない
### <a name="cause"></a>原因
ドライブは、ユーザーごとにマウントされます。 異なるユーザー アカウントでアプリケーションまたはサービスが実行されている場合、ユーザーにはドライブが表示されません。

### <a name="solution"></a>解決策
アプリケーションが属するのと同じユーザー アカウントからドライブをマウントします。 この操作は、psexec などのツールを使用して実行できます。

**net use** の別のオプションとして、**net use** コマンドのユーザー名とパスワードのパラメーターで、ストレージ アカウント名とキーを渡すことができます。

これらの手順に従った後に、次のエラー メッセージが表示される場合があります: "システム エラー 1312 が発生しました。 指定されたログオン セッションは存在しません。 そのセッションは既に終了している可能性があります" (システム/ネットワーク サービス アカウントに対して **net use** を実行した場合)。 このエラーが発生した場合は、**net use** に渡されるユーザー名にドメイン情報 ("[ストレージ アカウント名].file.core.windows.net" など) が含まれていることを確認してください。

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>エラー "暗号化をサポートしていない宛先に、ファイルをコピーします"
### <a name="cause"></a>原因
BitLocker で暗号化されたファイルは Azure Files にコピーできます。 ただし、File Storage は、NTFS EFS をサポートしていません。 そのため、このエラーが発生する場合、ユーザーは EFS を使用している可能性があります。 EFS を使用して暗号化されたファイルがある場合は、コピーしたファイルの暗号化を copy コマンドで解除しない限り、File Storage へのコピー操作は失敗する可能性があります。

### <a name="workaround"></a>対処法
File Storage にファイルをコピーするには、最初にファイルの暗号化を解除します。 これは、次のメソッドのいずれかを使用して実行できます。

•    **copy /d** を使用します。

•    次のレジストリ キーを設定します。

* Path=HKLM\Software\Policies\Microsoft\Windows\System
* Value type=DWORD
* Name = CopyFileAllowDecryptedRemoteDestination
* Value = 1

ただし、レジストリ キーの設定は、ネットワーク共有に対するすべてのコピー操作に影響します。

<a id="errorhold"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>マウント ポイントで list コマンドを実行すると、既存のファイル共有で "ホストがダウンしています (エラー 112)" というエラーが発生するか、シェルが応答を停止する
### <a name="cause"></a>原因
このエラーは、Linux クライアントがある長時間アイドル状態である場合に発生します。 クライアントが長時間アイドル状態である場合、クライアントが切断され、接続がタイムアウトになります。 

接続は、さまざまな理由によりアイドル状態になります。 ネットワーク通信エラーの原因の 1 つは、既定である「ソフト」マウント オプションが使用されたときに、サーバーへの TCP 接続が再確立できなくなるためです。

また、古いカーネルに再接続の問題の修正が存在しないことも原因の 1 つと考えられます。

### <a name="solution"></a>解決策

ハード マウントを指定すると、接続が確立されるまで、または明示的に中断されるまで、クライアントが強制的に待機させられます。これはネットワーク タイムアウトによるエラーを防ぐために使用できます。 ただし、ユーザーは、この方法では待機が無期限に続く可能性があることや、必要に応じて接続を一時停止すべきことを認識しなければなりません。

Linux カーネルのこの再接続の問題は、次の変更に伴い解決されました。

* [ソケットの再接続後、smb3 セッションの再接続を長時間保留しないようにする修正](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)

* [ソケットの再接続後すぐにエコー サービスを呼び出す](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)

* [CIFS: 再接続中にメモリが破損する可能性がある問題の修正](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)

* [CIFS: 再接続中にミューテックスのダブルロックが発生する可能性がある問題の修正 (カーネル v4.9 以上)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) 

ただし、この変更が既にすべての Linux ディストリビューションに移植されているとは限りません。 再接続の問題が修正されている一般的な Linux カーネルは、4.4.40 以上、4.8.16 以上、4.9.1 以上です。
最新の修正内容が反映されたこれらの推奨バージョンを利用することをお勧めします。

### <a name="workaround"></a>対処法
最新バージョンのカーネルに移行できない場合は、Azure ファイル共有にファイルを保持して 30 秒以下ごとに書き込むことで、この問題を回避できます。 これは、ファイルに作成日/変更日を再書き込みするなどの書き込み操作である必要があります。 そうでないと、キャッシュされた結果が得られ、操作によって接続がトリガーされない可能性があります。 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Linux VM で Azure Files をマウントしようとしたときに、"マウント エラー 115" が発生する
### <a name="cause"></a>原因
Linux ディストリビューションは、SMB 3.0 の暗号化機能を現時点ではサポートしていません。 いくつかのディストリビューションでは、ユーザーが SMB 3.0 を使用して Azure Files をマウントしようとしたときに、"115" エラー メッセージが表示される場合があります。これは、SMB 3.0 がサポートされていない機能であるためです。

### <a name="solution"></a>解決策
使用している Linux SMB クライアントが暗号化をサポートしていない場合は、File ストレージ アカウントと同じデータ センターにある Linux VM から SMB 2.1 を使用して Azure Files をマウントします。

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>Linux VM にマウントされている Azure ファイル共有のパフォーマンスが低下している

パフォーマンス低下の原因として、キャッシュが無効になっていることが考えられます。 キャッシュが有効になっているかを確認するには、"cache=" を探します。  *cache=none* は、キャッシングが無効になっていることを示します。 既定のマウント コマンドで共有を再マウントするか、明示的に **cache=strict** オプションを追加してコマンドをマウントして、既定のキャッシングまたは "strict" キャッシング モードが有効になっていることを確認してください。

一部のシナリオでは、serverino マウント オプションを指定していると、ls コマンドによってすべてのディレクトリ エントリに対して stat が実行されます。そのため、大規模なディレクトリを一覧表示するときにパフォーマンスが低下します。 "/Etc/fstab" エントリでマウント オプションを確認できます。

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

**sudo mount | grep cifs** コマンドを実行して出力を調べるだけで、正しいオプションが使われているかどうかを確認できます。

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

cache=strict または serverino オプションが指定されていない場合は、Azure ファイルをマウント解除してから mount コマンドで再マウントし ([ドキュメント](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share)を参照)、"/etc/fstab" エントリに適切なオプションがあることを確認します。

<a id="ubuntumounterror"></a>
## <a name="mount-error11-resource-temporarily-unavailable-when-mounting-to-ubuntu-48-kernel"></a>マウント エラー (11): Ubuntu 4.8+ カーネルにマウントするときにリソースが一時的に使用不能になる

### <a name="cause"></a>原因
Ubuntu 16.10 カーネル (v.4.8) には、クライアントが暗号化のサポートを要求しているがサポートされていないという既知の問題があります。 

### <a name="solution"></a>解決策
Ubuntu 16.10 が修正されるまでは、"vers=2.1" マウント オプションを指定するか Ubuntu 16.04 を使用します。
## <a name="learn-more"></a>詳細情報
* [Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)
* [Linux で Azure File Storage を使用する](storage-how-to-use-files-linux.md)

